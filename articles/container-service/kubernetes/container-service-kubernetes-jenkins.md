---
title: "Déploiement et intégration continus Jenkins avec Kubernetes dans Azure Container Service"
description: "Procédure d’automatisation d’un processus de déploiement et d’intégration avec Jenkins pour déployer et mettre à niveau une application en conteneur sur Kubernetes dans Azure Container Service"
services: container-service
author: chzbrgr71
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: cd8f7ae584b6b1afd357cc585df28dedd3c1f70e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Intégration Jenkins avec Azure Container Service et Kubernetes 

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Dans ce didacticiel, nous présentons le processus de configuration de l’intégration continue d’une application à conteneurs multiples dans des Kubernetes Azure Container Service à l’aide de la plate-forme Jenkins. Le flux de travail met à jour de l’image de conteneur dans Docker Hub et met à niveau les blocs Kubernetes à l’aide d’une procédure de déploiement. 

## <a name="high-level-process"></a>Processus de niveau supérieur
Les étapes de base décrites dans cet article sont : 
- Installation d’un cluster Kubernetes dans Container Service
- Configuration de Jenkins et de l’accès à Container Service
- Création d’un flux de travail Jenkins
- Test du processus d’intégration et de déploiement continus de bout en bout

## <a name="install-a-kubernetes-cluster"></a>Installation d’un cluster Kubernetes
    
Déployez le cluster Kubernetes dans Azure Container Service à l’aide des étapes suivantes. Une documentation complète est disponible [ici](container-service-kubernetes-walkthrough.md).

### <a name="step-1-create-a-resource-group"></a>Étape 1 : création d’un groupe de ressources
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>Étape 2 : déploiement du cluster
> [!NOTE]
> Les étapes suivantes requièrent une clé publique SSH locale stockée dans le dossier ~/.ssh.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Configuration de Jenkins et de l’accès à Container Service

### <a name="step-1-install-jenkins"></a>Étape 1 : Installer Jenkins
1. Créez une machine virtuelle Azure avec Ubuntu 16.04 LTS.  Comme vous allez devoir vous connecter à cette machine virtuelle en utilisant Bash sur votre ordinateur local, définissez le type d’authentification sur « Clé publique SSH », puis collez la clé publique SSH stockée localement dans votre dossier ~/.ssh.  Notez également le nom d’utilisateur que vous spécifiez, car vous en aurez besoin pour afficher le tableau de bord Jenkins et pour vous connecter à la machine virtuelle Jenkins dans les étapes ultérieures.
2. Installez Jenkins en suivant ces [instructions](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu). Un didacticiel plus détaillé est disponible sur [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04).
3. Pour afficher le tableau de bord Jenkins sur votre ordinateur local, mettez à jour le groupe de sécurité réseau Azure pour autoriser le port 8080, en ajoutant une règle de trafic entrant autorisant l’accès au port 8080.  Vous pouvez également configurer un réacheminement de port en exécutant la commande suivante : `ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`.
4. Connectez-vous à votre serveur Jenkins à l’aide du navigateur en accédant à l’adresse IP publique (http://<votre_ip_publique_jenkins>:8080), puis déverrouillez le tableau de bord Jenkins pour la première fois avec le mot de passe d'administrateur initial.  Le mot de passe d’administrateur est stocké dans /var/lib/jenkins/secrets/initialAdminPassword sur la machine virtuelle Jenkins.  Un moyen simple pour obtenir ce mot de passe est d’accéder via SSH à la machine virtuelle Jenkins : `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Ensuite, exécutez la commande suivante : `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
5. Installez Docker sur l’ordinateur Jenkins via ces [instructions](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts). Ainsi, les commandes Docker peuvent être exécutées dans les travaux Jenkins.
6. Configurez les autorisations Docker pour permettre à Jenkins d’accéder au point de terminaison Docker.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Installez la CLI `kubectl` sur Jenkins. Des informations supplémentaires sont disponibles dans l’article [Installation et configuration de kubectl](https://kubernetes.io/docs/tasks/kubectl/install/).  Les travaux Jenkins utiliseront « kubectl » pour gérer et déployer vers le cluster Kubernetes.

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>Étape 2 : Configurer l’accès au cluster Kubernetes

> [!NOTE]
> Il existe plusieurs approches pour accomplir les étapes suivantes. Utilisez l’approche la plus simple pour vous.
>

1. Copiez le fichier de configuration Docker `kubectl` vers l’ordinateur Jenkins afin que les travaux Jenkins aient accès au cluster Kubernetes. Ces instructions supposent que vous utilisez Bash à partir d’un ordinateur autre que la machine virtuelle Jenkins, et qu’une clé publique SSH locale est stockée dans le dossier ~/.ssh de l’ordinateur.

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. Vérifiez via Jenkins que le cluster Kubernetes est accessible.  Pour ce faire, accédez via SSH à la machine virtuelle Jenkins : `ssh <your_jenkins_user>@<your_jenkins_public_ip>`.  Ensuite, vérifiez que Jenkins peut se connecter correctement à votre cluster : `kubectl cluster-info`.
    

## <a name="create-a-jenkins-workflow"></a>Création d’un flux de travail Jenkins

### <a name="prerequisites"></a>Composants requis

- Compte GitHub pour le référentiel de code.
- Compte Docker Hub pour stocker et mettre à jour des images.
- Applications en conteneur pouvant être reconstruites et mises à jour. Vous pouvez utiliser cet exemple d’application de conteneur écrite en Golang : https://github.com/chzbrgr71/go-web 

> [!NOTE]
> Les étapes suivantes doivent être effectuées dans votre compte GitHub. N’hésitez pas à cloner le référentiel ci-dessus, mais vous devez utiliser votre propre compte pour configurer les webhooks et l’accès à Jenkins.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>Étape 1 : Déployer la v1 initiale de l’application
1. Générez l’application à partir de l’ordinateur de développement avec les commandes suivantes. Remplacez `myrepo` par les vôtres.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. Transférez l’image vers Docker Hub.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Effectuez le déploiement vers le cluster Kubernetes.
    
    > [!NOTE] 
    > Modifiez le fichier `go-web.yaml` pour mettre à jour votre image de conteneur et le référentiel.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>Étape 2 : Configurer le système de Jenkins
1. Cliquez sur **Gérer Jenkins** > **Configurer le système**.
2. Sous **GitHub**, sélectionnez **Ajouter un serveur GitHub**.
3. Laissez **URL d’API** comme valeur par défaut.
4. Sous **Informations d’identification**, ajoutez des informations d’identification Jenkins à l’aide d’un **Texte secret**. Nous recommandons l’utilisation de jetons d’accès personnels GitHub, qui sont configurés dans vos paramètres de compte d’utilisateur GitHub. Plus d’informations, rendez-vous [ici](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).
5. Cliquez sur **Tester la connexion** pour vous assurer que cela est configuré correctement.
6. Sous **Propriétés globales**, ajoutez une variable d’environnement `DOCKER_HUB` et fournissez votre mot de passe Docker Hub. (Ceci est utile dans cette démonstration, mais un scénario de production nécessite une approche plus sécurisée.)
7. Enregistrez.

![Accès de Jenkins à GitHub](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>Étape 3 : Créer le flux de travail Jenkins
1. Créez un élément Jenkins.
2. Fournissez un nom (par exemple, « go-web ») et sélectionnez **Freestyle Project**. 
3. Vérifiez le champ **Projet GitHub** et fournissez l’URL de votre référentiel GitHub.
4. Dans **Gestion du code source**, fournissez les informations d’identification et l’URL du référentiel GitHub. 
5. Ajouter une **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant :

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. Ajouter une autre **Étape de génération** de type **Exécuter un script shell** et utilisez le texte suivant :

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Étapes de génération Jenkins](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Enregistrez l’élément Jenkins et testez-le avec l’option **Générer maintenant**.

### <a name="step-4-connect-github-webhook"></a>Étape 4 : Se connecter au webhook GitHub
1. Dans l’élément Jenkins que vous avez créé, cliquez sur **Configurer**.
2. Sous **Déclencheurs de génération**, sélectionnez **Déclencher un hook GitHub pour l’interrogation GITScm**, puis **Enregistrer**. Cette méthode configure automatiquement le webhook GitHub.
3. Dans votre référentiel GitHub pour « go-web », cliquez sur **Paramètres > Webhooks**.
4. Vérifiez que l’URL du webhook Jenkins a été ajoutée avec succès. L’URL doit se terminer par « github-webhook ».

![Configuration du webhook Jenkins](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>Test du processus d’intégration et de déploiement continus de bout en bout

1. Mettez à jour le code pour le référentiel et effectuez une opération de push/synchronisation avec le référentiel GitHub.
2. À partir de la console Jenkins, consultez l’**Historique de génération** et vérifiez que la tâche a été exécutée. Consultez la sortie de la console pour afficher les détails.
3. À partir de Kubernetes, affichez les détails du déploiement mis à niveau :

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>Étapes suivantes

- Déployez le registre de conteneurs Azure et stockez des images dans un référentiel sécurisé. Consultez les [documents du Registre de conteneurs Azure](https://docs.microsoft.com/azure/container-registry).
- Créez un flux de travail plus complexe qui inclut les déploiements côte à côte et les tests automatisés dans Jenkins.
- Pour plus d’informations sur l’intégration et le déploiement continus avec Jenkins et Kubernetes, consultez le [blog Jenkins](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/).
