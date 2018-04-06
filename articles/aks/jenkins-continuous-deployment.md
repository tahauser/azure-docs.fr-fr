---
title: Déploiement continu Jenkins avec Kubernetes dans Azure Container Service
description: Automatisation d’un processus de déploiement continu avec Jenkins pour déployer et mettre à niveau une application en conteneur sur Kubernetes dans Azure Container Service
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8238e0f55b88e4fa207357630aa4228250c33249
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Déploiement continu avec Jenkins et Azure Container Service

Ce document explique comment configurer un workflow de déploiement continu de base entre Jenkins et un cluster Azure Container Service (AKS). 

L’exemple de workflow inclut les étapes suivantes :

> [!div class="checklist"]
> * Déployez l’application de vote Azure dans le cluster Kubernetes.
> * Mettez à jour le code de l’application de vote et envoyez-le vers le référentiel GitHub. Ceci démarre le processus de déploiement continu.
> * Jenkins clone le référentiel et génère une image conteneur avec le code mis à jour.
> * Cette image est envoyée à Azure Container Registry (ACR).
> * L’application en cours d’exécution dans le cluster AKS est mise à jour avec la nouvelle image conteneur.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants.

- Notions de base de Kubernetes, Git, déploiement et intégration continus, et Azure Container Registry (ACR).
- Un [cluster Azure Container Service (AKS)][aks-quickstart] et des [informations d’identification AKS configurées][aks-credentials] sur votre système de développement.
- Un [registre Azure Container Registry (ACR)][acr-quickstart], le nom du serveur de connexion ACR, et les [informations d’identification ACR][acr-authentication] avec accès par envoi et par extraction.
- Azure CLI installé sur votre système de développement.
- Docker installé sur votre système de développement.
- Un compte GitHub, un [jeton d’accès personnel GitHub][git-access-token] et un client Git installé sur votre système de développement.

## <a name="prepare-application"></a>Préparer l’application

L’application de vote Azure utilisée dans ce document contient une interface web hébergée dans un pod ou plus, un second pod qui héberge Redis pour du stockage de données temporaires. 

Avant de générer l’intégration de Jenkins / AKS, préparez et déployez l’application de vote dans votre cluster AKS. Considérez cette version comme la version de base.

Créez deux branches de votre référentiel GitHub suivant.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Une fois la branche créée, clonez votre système de développement. Assurez-vous d’utiliser l’URL de votre branche lorsque vous clonez le référentiel.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Modifiez les répertoires de manière à travailler à partir des répertoires clonés.

```bash
cd azure-voting-app-redis
```

Exécutez le fichier `docker-compose.yaml` pour créer l’image conteneur `azure-vote-front`, puis démarrez l’application.

```bash
docker-compose up -d
```

Une fois terminé, utilisez la commande [docker images][docker-images] pour afficher l’image créée.

Notez que les trois images ont été téléchargées ou créées. L’image `azure-vote-front` contient l’application et utilise l’image `nginx-flask` comme base. L’image `redis` est utilisée pour démarrer une instance Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Obtenez le nom du serveur de connexion ACR à l’aide de la commande [az acr list][az-acr-list]. Veillez à mettre à jour le nom du groupe de ressources selon le groupe de ressources qui héberge votre registre ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilisez la commande [docker tag][docker-tag] pour baliser l’image avec le nom du serveur de connexion et le nombre de version `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Mettez à jour la valeur du serveur de connexion ACR avec le nom du serveur de connexion, et envoyez l’image `azure-vote-front` au registre. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Déployer une application dans Kubernetes

Un fichier manifeste Kubernetes peut être trouvé à la racine du référentiel de vote Azure, et utilisé pour déployer l’application dans votre cluster Kubernetes.

Mettez d’abord à jour le fichier manifest **azure-vote-all-in-one-redis.yaml** avec l’emplacement de votre registre ACR. Ouvrez le fichier avec un éditeur de texte et remplacez `microsoft` par le nom du serveur de connexion ACR. Cette valeur se trouve sur la ligne **47** du fichier manifeste.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Utilisez ensuite la commande [kubectl create][kubectl-create] pour exécuter l’application. Cette commande analyse le fichier manifeste et crée les objets Kubernetes définis.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Un [service Kubernetes][kubernetes-service] est créé pour exposer l’application à Internet. Ce processus peut prendre plusieurs minutes. 

Pour surveiller la progression, utilisez la commande [kubectl get service][kubectl-get] avec l’argument `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Au début, *EXTERNAL-IP* pour le service *azure-vote-front* apparaît *En attente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Une fois que l’adresse *EXTERNAL-IP* est passée du statut *En attente* à *Adresse IP*, utilisez `control+c` pour arrêter le processus de surveillance kubectl. 

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Pour afficher l’application, accédez à l’adresse IP externe.

![Image du cluster Kubernetes sur Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Déployer Jenkins dans une machine virtuelle

Un script a été créé en amont pour déployer une machine virtuelle, configurer l’accès réseau et réaliser une installation basique de Jenkins. En outre, le script copie votre fichier de configuration Kubernetes de votre système de développement dans le système Jenkins. Ce fichier est utilisé pour l’authentification entre Jenkins et le cluster AKS.

Exécutez les commandes suivantes pour télécharger et exécuter le script. L’URL ci-dessous peut aussi être utilisée pour examiner le contenu du script.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Une fois le script réalisé, il renvoie une adresse pour le serveur Jenkins, ainsi qu’une clé pour déverrouiller Jenkins. Accédez à l’URL, entrez la clé et suivez les invites à l’écran pour terminer la configuration de Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Si vous rencontrez des problèmes de connexion sur Jenkins, créez une session SSH avec la machine virtuelle Jenkins et redémarrez le service Jenkins. L’adresse IP de la machine virtuelle est la même adresse que celle fournie par le script build. Le nom d’utilisateur administrateur de la machine virtuelle est `azureuser`.

```bash
ssh azureuser@52.166.118.64
```

Redémarrez le service Jenkins.

```bash
sudo service jenkins restart
```

Actualisez votre navigateur et le formulaire de connexion Jenkins doit être affiché.

## <a name="jenkins-environment-variables"></a>Variables d’environnement Jenkins

Une variable d’environnement Jenkins est utilisée pour contenir le nom du serveur de connexion Azure Container Registry (ACR). Cette variable est référencée lors de la tâche de déploiement Jenkins.

Toujours sur le portail d’administration Jenkins, cliquez sur **Gérer Jenkins** > **Configurer le système**.

Sous **Propriétés globales**, sélectionnez **Variables d’environnement**, puis ajoutez une variable nommée `ACR_LOGINSERVER` et une valeur de votre serveur de connexion ACR.

![Variables d’environnement Jenkins](media/aks-jenkins/env-variables.png)

Lorsque vous avez terminé, cliquez sur **Enregistrer** sur la page de configuration Jenkins.

## <a name="jenkins-credentials"></a>Informations d’identification Jenkins

Stockez vos informations d’identification ACR dans un objet Credential. Ces informations d’identification sont référencées lors de la tâche de génération Jenkins.

De retour sur le portail d’administration Jenkins, cliquez sur **Informations d’identification** > **Jenkins** > **Informations d’identification globales (sans restriction)** > **Ajouter des informations d’identification**.

Assurez-vous que le genre d’informations d’identification correspond à **Mot de passe avec nom d’utilisateur** puis entrez les informations suivantes :

- **Nom d’utilisateur** : ID du principal de service utilisé pour s’authentifier avec votre registre ACR.
- **Mot de passe** : clé secrète client du principal de service utilisée pour s’authentifier avec votre registre ACR.
- **ID** : identificateur des informations d’identification, comme `acr-credentials`.

Lorsque vous avez terminé, le formulaire des informations d’identification devrait ressembler à celui sur cette image :

![Informations d’identification ACR](media/aks-jenkins/acr-credentials.png)

Cliquez sur **OK** et retournez sur le portail d’administration Jenkins.

## <a name="create-jenkins-project"></a>Créer le projet Jenkins

Depuis le portail d’administration Jenkins, cliquez sur **Nouvel élément**.

Nommez le projet, par exemple `azure-vote`, sélectionnez **Projet Freestyle** puis cliquez sur **OK**. 

![Projet Jenkins](media/aks-jenkins/jenkins-project.png)

Sous **Général**, sélectionnez **Projet GitHub** et entrez l’URL vers votre branche du projet Github de vote Azure.

![Projet GitHub](media/aks-jenkins/github-project.png)

Sous **Gestion du code source**, sélectionnez **Git**, entrez l’URL vers votre branche du référentiel GitHub de vote Azure. 

Pour les informations d’identification, cliquez sur **Ajouter** > **Jenkins**. Sous **Genre**, sélectionnez **Texte secret** et entrez votre [jeton d’accès personnel GitHub][git-access-token] comme secret. 

Lorsque c’est fait, sélectionnez **Ajouter**.

![Informations d’identification GitHub](media/aks-jenkins/github-creds.png)

Sous **Déclencheurs de build**, sélectionnez **Déclencher un hook GitHub pour l’interrogation GITScm**.

![Déclencheurs de build Jenkins](media/aks-jenkins/build-triggers.png)

Sous **Environnement de build**, sélectionnez **Utiliser des textes ou fichiers secrets**.

![Environnement de build Jenkins](media/aks-jenkins/build-environment.png)

Sous **Liaisons**, sélectionnez **Ajouter** > **Nom d’utilisateur et mot de passe (distincts)**. 

Entrez `ACR_ID` comme **Variable de nom d’utilisateur**, et `ACR_PASSWORD` comme **Variable de mot de passe**.

![Liaisons Jenkins](media/aks-jenkins/bindings.png)

Ajoutez une **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant. Ce script génère une image conteneur et l’envoie dans votre registre ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Ajoutez une autre **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant. Ce script met à jour le déploiement Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Une fois terminé, cliquez sur **Enregistrer**.

## <a name="test-the-jenkins-build"></a>Tester la build Jenkins

Avant de continuer, testez la build Jenkins. Ceci confirme que la tâche de génération a été correctement configurée, que le bon fichier d’authentification Kubernetes est en place et que les bonnes informations d’identification ACR ont été renseignées.

Cliquez sur **Générer maintenant** dans le menu de gauche du projet. 

![Build de test Jenkins](media/aks-jenkins/test-build.png)

Lors de ce processus, le référentiel GitHub est cloné dans le serveur de génération Jenkins. Une image conteneur est générée et envoyée dans le registre ACR. Enfin, l’application de vote Azure exécutée sur le cluster AKS est mise à jour afin d’utiliser la nouvelle image. L’application n’est pas différente car aucun changement n’a été effectué au niveau du code de l’application.

Une fois le processus terminé, vous pouvez cliquer sur **build #1** dans l’historique de génération et sélectionnez **Sortie de la console** pour voir toutes les sorties du processus de génération. La dernière ligne doit indiquer que la génération a été un succès. 

## <a name="create-github-webhook"></a>Créer un webhook GitHub

Ensuite, accrochez le référentiel de l’application au serveur de build Jenkins pour qu’une génération soit déclenchée à chaque validation.

1. Allez au référentiel GitHub dupliqué.
2. Sélectionnez **Paramètres**, puis **Intégrations et services** sur le côté gauche.
3. Choisissez **Ajouter un service**, entrez `Jenkins (GitHub plugin)` dans la zone de filtre puis sélectionnez le plug-in.
4. Pour l’URL du hook Jenkins, entrez `http://<publicIp:8080>/github-webhook/` où `publicIp` correspond à l’adresse IP du serveur Jenkins. Assurez-vous d’inclure la barre oblique (/) à la fin.
5. Sélectionnez Ajouter un service.
  
![webhook GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Test du processus d’intégration et de déploiement continus de bout en bout

Sur votre machine de développement, ouvrez l’application clonée avec un éditeur de code. 

Sous le répertoire **/azure-vote/azure-vote**, vous pouvez trouver un fichier nommé **config_file.cfg**. Mettez à jour les valeurs de vote dans ce fichier en quelque chose différent de chats et chiens. 

L’exemple suivant montre un fichier **config_file.cfg** mis à jour.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Lorsque vous avez terminé, enregistrez le fichier, validez les modifications et envoyez-les à la branche de votre référentiel GitHub. Une fois la validation terminée, le webhook GitHub déclenche un nouveau build Jenkins, ce qui met à jour l’image conteneur et le déploiement AKS. Surveillez le processus de génération sur la console d’administration Jenkins. 

Une fois la génération terminée, retournez au point de terminaison de l’application pour en observer les modifications.

![Vote Azure mis à jour](media/aks-jenkins/azure-vote-updated-safari.png)

À ce stade, un simple processus de déploiement continu a été réalisé. Les étapes et les configurations montrées dans cet exemple peuvent être utilisées pour générer une automatisation de build continue plus robuste et prête à la production.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli