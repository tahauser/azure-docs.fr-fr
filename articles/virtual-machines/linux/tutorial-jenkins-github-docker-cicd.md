---
title: Créer un pipeline de développement dans Azure avec Jenkins | Microsoft Docs
description: Découvrez comment créer une machine virtuelle Jenkins dans Azure, qui extrait de GitHub à chaque validation de code et génère un nouveau conteneur Docker pour exécuter votre application
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9250e40c491257b554333f4606cbf0b476d8db21
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Comment créer une infrastructure de développement sur une machine virtuelle Linux dans Azure avec Jenkins, GitHub et Docker
Pour automatiser les phases de création et de test du développement de l’application, vous pouvez utiliser un pipeline d’intégration et de déploiement continus (CI/CD). Dans ce didacticiel, vous créez un pipeline CI/CD sur une machine virtuelle Azure et apprenez notamment comment :

> [!div class="checklist"]
> * Créer une machine virtuelle Jenkins
> * Installer et configurer Jenkins
> * Créer une intégration webhook entre GitHub et Jenkins
> * Créer et déclencher des tâches de génération Jenkins à partir de validations GitHub
> * Créer une image Docker pour votre application
> * Vérifier les validations GitHub, générer une nouvelle image Docker et mettre à jour l’application en cours d’exécution


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.22 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-jenkins-instance"></a>Création d’une instance Jenkins
Dans le didacticiel précédent [How to customize a Linux virtual machine on first boot (Personnalisation d’une machine virtuelle Linux au premier démarrage)](tutorial-automate-vm-deployment.md), vous avez appris à automatiser la personnalisation des machines virtuelles avec cloud-init. Ce didacticiel utilise un fichier cloud-init pour installer Jenkins et Docker sur une machine virtuelle. Jenkins est un serveur d’automatisation open source courant qui s’intègre aisément à Azure pour activer l’intégration continue (CI, Continuous Integration) et la livraison continue (CD, Continous Delivery). Pour plus de didacticiels sur l’utilisation de Jenkins, consultez le [hub Jenkins dans Azure](https://docs.microsoft.com/azure/jenkins/).

Dans l’interpréteur de commandes actuel, créez un fichier nommé *cloud-init-jenkins.txt* et collez la configuration suivante. Par exemple, créez le fichier dans l’interpréteur de commandes Cloud et non sur votre ordinateur local. Entrez `sensible-editor cloud-init-jenkins.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne :

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Pour pouvoir créer une machine virtuelle, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupJenkins* à l’emplacement *eastus* :

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Créez maintenant une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). Utilisez le paramètre `--custom-data` à transmettre dans votre fichier de configuration cloud-init. Indiquez le chemin complet vers *cloud-init-jenkins.txt* si vous avez enregistré le fichier en dehors de votre répertoire de travail actuel.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Il faut quelques minutes pour que la machine virtuelle soit créée et configurée.

Pour que le trafic web puisse atteindre votre machine virtuelle, utilisez [az vm open-port](/cli/azure/vm#az_vm_open_port) pour ouvrir le port *8080* pour le trafic Jenkins et le port *1337* pour l’application Node.js qui sert à exécuter un exemple d’application :

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configurer Jenkins
Pour accéder à votre instance Jenkins, obtenez l’adresse IP publique de votre machine virtuelle :

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Pour des raisons de sécurité, vous devez entrer le mot de passe d’administrateur initial qui est stocké dans un fichier texte sur votre machine virtuelle pour démarrer l’installation Jenkins. Utilisez l’adresse IP publique obtenue à l’étape précédente pour exécuter SSH sur votre machine virtuelle :

```bash
ssh azureuser@<publicIps>
```

Affichez le `initialAdminPassword` pour votre installation Jenkins et copiez-le :

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Si le fichier n’est pas encore disponible, attendez quelques minutes le temps que cloud-init termine l’installation de Jenkins et Docker.

Ouvrez un navigateur web et accédez à `http://<publicIps>:8080`. Terminez la configuration initiale de Jenkins comme suit :

- Cliquez sur **Sélectionner les plug-ins à installer**.
- Recherchez *GitHub* dans la zone de texte de la partie supérieure. Cochez la case pour *GitHub*, puis sélectionnez **Installer**.
- Créez le premier utilisateur administrateur. Entrez un nom d’utilisateur, tel que **admin**, puis fournissez votre propre mot de passe sécurisé. Enfin, tapez un nom complet et une adresse e-mail.
- Sélectionnez **Save and Finish** (Enregistrer et terminer).
- Une fois que Jenkins est prêt, sélectionnez **Start using Jenkins** (Commencer à utiliser Jenkins).
  - Si votre navigateur web affiche une page vierge lorsque vous commencez à utiliser Jenkins, redémarrez le service Jenkins. Dans votre session SSH, tapez `sudo service jenkins restart`, puis actualisez votre navigateur web.
- Connectez-vous à Jenkins avec le nom d’utilisateur et le mot de passe créés.


## <a name="create-github-webhook"></a>Créer un webhook GitHub
Pour configurer l’intégration avec GitHub, ouvrez l’[exemple d’application Hello World basé sur Node.js](https://github.com/Azure-Samples/nodejs-docs-hello-world) à partir du référentiel d’exemples Azure. Pour répliquer le référentiel dans votre propre compte GitHub, cliquez sur le bouton **Bifurcation** en haut à droite.

Créez un webhook à l’intérieur de la bifurcation que vous avez créée :

- Sélectionnez **Paramètres**, puis **Intégrations et services** sur le côté gauche.
- Sélectionnez **Ajouter un service**, puis entrez *Jenkins* dans la zone de filtre.
- Sélectionnez *Jenkins (plug-in GitHub)*
- Pour l’**URL du hook Jenkins**, entrez `http://<publicIps>:8080/github-webhook/`. Assurez-vous d'inclure la barre oblique (/) à la fin
- Sélectionnez **Ajouter un service**.

![Ajouter un webhook GitHub à votre référentiel bifurqué](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Créer une tâche Jenkins
Pour que Jenkins réponde à un événement dans GitHub (un code de validation par ex.), créez une tâche Jenkins. Utilisez les URL de votre duplication GitHub.

Dans votre site web Jenkins, sélectionnez **Créer de nouvelles tâches** dans la page d’accueil :

- Entrez *HelloWorld* comme nom de la tâche. Sélectionnez **Projet libre**, puis cliquez sur **OK**.
- Dans la section **Général**, sélectionnez le projet **GitHub**, puis entrez l’URL de votre référentiel dupliqué (par exemple, *https://github.com/iainfoulds/nodejs-docs-hello-world*).
- Dans la section **Gestion du code source**, sélectionnez **Git** et entrez l’URL de votre référentiel dupliqué *.git* (par exemple, *https://github.com/iainfoulds/nodejs-docs-hello-world.git*).
- Dans la section **Déclencheurs de génération**, sélectionnez **Déclencher un hook GitHub pour l’interrogation GITScm**.
- Dans la section **Build**, cliquez sur **Ajouter une étape de build**. Sélectionnez **Exécuter l’interpréteur de commandes**, puis entrez `echo "Testing"` dans la fenêtre de commande.
- Sélectionnez **Enregistrer** en bas de la fenêtre des tâches.


## <a name="test-github-integration"></a>Test de l’intégration de GitHub
Pour tester l’intégration de GitHub avec Jenkins, validez une modification dans votre bifurcation. 

Dans l’interface utilisateur web de GitHub, sélectionnez votre référentiel bifurqué, puis le fichier **index.js**. Sélectionnez l’icône de crayon pour modifier ce fichier de manière à ce que la ligne 6 affiche :

```nodejs
response.end("Hello World!");
```

Pour valider vos modifications, sélectionnez le bouton **Valider les modifications** en bas.

Dans Jenkins, une nouvelle génération démarre sous la section **Générer l’historique** dans le coin inférieur gauche de la page de votre tâche. Cliquez sur le lien du numéro de build et sélectionnez **Sortie de console** à gauche. Vous pouvez afficher les étapes que Jenkins entreprend lorsque votre code est extrait de GitHub et l’action de génération entraîne l’affichage du message `Testing` sur la console. À chaque fois qu’une validation est effectuée dans GitHub, le webhook contacte Jenkins et déclenche une nouvelle génération de cette façon.


## <a name="define-docker-build-image"></a>Définir l’image de génération Docker
Pour que l’application Node.js soit exécutée en fonction de vos validations GitHub, il faut générer une image Docker servant à exécuter l’application. L’image est créée à partir d’un fichier Dockerfile qui définit comment configurer le conteneur qui exécute l’application. 

À partir de la connexion SSH à votre machine virtuelle, accédez au répertoire d’espace de travail Jenkins nommé d’après la tâche que vous avez créée à l’étape précédente. Dans cet exemple, il était nommé *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Créez un fichier dans ce répertoire d’espace de travail avec `sudo sensible-editor Dockerfile` et collez le contenu suivant : Vérifiez que l’intégralité du fichier Docker est copiée, en particulier la première ligne :

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Ce fichier Dockerfile utilise l’image Node.js de base avec Linux Alpine, expose le port 1337 sur lequel l’application Hello World est exécutée, puis copie les fichiers d’application et l’initialise.


## <a name="create-jenkins-build-rules"></a>Créer des règles de génération Jenkins
À l’étape précédente, vous avez créé une règle de génération Jenkins de base qui affiche un message sur la console. Nous allons à présent créer une étape de génération pour utiliser notre fichier Dockerfile et exécuter l’application.

Dans votre instance Jenkins, sélectionnez la tâche que vous avez créée à l’étape précédente. Sélectionnez **Configurer** sur le côté gauche et faites défiler la page jusqu’à la section **Génération** :

- Supprimez votre étape de génération `echo "Test"` existante. Cliquez sur la croix rouge en haut à droite de la zone de l’étape de génération existante.
- Cliquez sur **Ajouter une étape de génération**, puis sélectionnez **Exécuter l’interpréteur de commandes**.
- Dans la zone **Commande**, entrez les commandes Docker suivantes, puis sélectionnez **Enregistrer** :

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Les étapes de génération Docker créent une image et l’identifient par le numéro de génération Jenkins afin de pouvoir tenir à jour un historique des images. Les conteneurs existants qui exécutent l’application sont arrêtés puis supprimés. Un nouveau conteneur est ensuite démarré à l’aide de l’image et exécute votre application Node.js sur la base des dernières validations dans GitHub.


## <a name="test-your-pipeline"></a>Tester votre pipeline
Pour voir l’intégralité du pipeline en action, modifiez à nouveau le fichier *index.js* dans votre référentiel GitHub bifurqué, puis sélectionnez **Valider la modification**. Une nouvelle tâche démarre dans Jenkins sur la base du webhook pour GitHub. Il faut quelques secondes pour créer l’image Docker et lancer votre application dans un nouveau conteneur.

Si nécessaire, obtenez à nouveau l’adresse IP publique de votre machine virtuelle :

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Ouvrez un navigateur web et entrez `http://<publicIps>:1337`. Votre application Node.js s’affiche et reflète les dernières validations dans votre bifurcation GitHub comme suit :

![Exécution de l’application Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

À présent, apportez une autre modification au fichier *index.js* dans GitHub et validez la modification. Attendez quelques secondes la fin de la tâche dans Jenkins, puis actualisez votre navigateur web pour afficher la version mise à jour de votre application exécutée dans un nouveau conteneur comme suit :

![Exécution de l’application Node.js après une autre validation GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré GitHub pour qu’il exécute une tâche de génération Jenkins à chaque validation de code et déploie ensuite un conteneur Docker pour tester votre application. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Jenkins
> * Installer et configurer Jenkins
> * Créer une intégration webhook entre GitHub et Jenkins
> * Créer et déclencher des tâches de génération Jenkins à partir de validations GitHub
> * Créer une image Docker pour votre application
> * Vérifier les validations GitHub, générer une nouvelle image Docker et mettre à jour l’application en cours d’exécution

Passez au didacticiel suivant pour en savoir plus sur l’intégration de Jenkins à Visual Studio Team Services.

> [!div class="nextstepaction"]
> [Déployer des applications avec Jenkins et Team Services](tutorial-build-deploy-jenkins.md)
