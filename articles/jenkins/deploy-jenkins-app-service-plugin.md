---
title: "Déployer sur Azure App Service à l’aide du plug-in Jenkins | Microsoft Docs"
description: "Découvrez comment utiliser le plug-in Azure App Service Jenkins pour déployer une application web Java sur Azure dans Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 0e5916b2f8f901ff549ef74fca57cf09dc9fec21
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Déployer sur Azure App Service à l’aide du plug-in Jenkins 

Pour déployer une application web Java sur Azure, vous pouvez utiliser Azure CLI dans [Jenkins Pipeline](/azure/jenkins/execute-cli-jenkins-pipeline) ou vous pouvez utiliser le [plug-in Azure App Service Jenkins](https://plugins.jenkins.io/azure-app-service). Le plug-in Jenkins version 1.0 prend en charge le déploiement continu à l’aide de la fonctionnalité Web Apps d’Azure App Service par le biais de :
* Git ou FTP.
* Docker pour Web Apps sur Linux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configurer Jenkins pour déployer Web Apps via Git ou FTP.
> * Configurer Jenkins pour déployer Web App pour conteneurs.

## <a name="create-and-configure-a-jenkins-instance"></a>Créer et configurer une instance Jenkins

Si vous ne disposez pas encore d’un serveur maître Jenkins, commencez par utiliser le [modèle de solution](install-jenkins-solution-template.md), qui inclut le Kit de développement Java (JDK) version 8 et les plug-ins Jenkins requis suivants :

* [Plug-in du client Git Jenkins](https://plugins.jenkins.io/git-client) version 2.4.6 
* [Plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) version 1.4.0
* [Informations d’identification Azure](https://plugins.jenkins.io/azure-credentials) version 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-server) version 0.1

Vous pouvez utiliser le plug-in Jenkins pour déployer une application web dans n’importe quel langage pris en charge par Web Apps, comme C#, PHP, Java et Node.js. Dans ce didacticiel, nous utilisons une [simple application web Java pour Azure](https://github.com/azure-devops/javawebappsample). Pour dupliquer (fork) le référentiel dans votre propre compte GitHub, cliquez sur le bouton **Fork** dans l’angle supérieur droit de l’interface GitHub.  
> [!NOTE]
> Java JDK et Maven sont obligatoires pour la génération du projet Java. Installez ces composants sur le serveur maître Jenkins ou l’agent de la machine virtuelle si vous utilisez l’agent à des fins d’intégration continue. 

Pour installer les composants, connectez-vous à l’instance Jenkins avec le protocole SSH et exécutez les commandes suivantes :

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Pour déployer sur Web App pour conteneurs, installez Docker sur le serveur maître Jenkins ou l’agent de machine virtuelle qui est utilisé pour la génération. Pour obtenir des instructions, consultez [Installer Docker sur Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Ajouter un principal de service Azure aux informations d’identification Jenkins

Un principal de service Azure est nécessaire pour les déploiements sur Azure. 


1. Pour créer un principal de service Azure, utilisez [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou le [portail Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Dans le tableau de bord Jenkins, sélectionnez **Informations d’identification** > **Système**. Puis, sélectionnez **Informations d’identification globales (sans restriction)**.
3. Pour ajouter un principal de service Microsoft Azure, sélectionnez **Ajouter les informations d’identification**. Entrez des valeurs pour les champs **ID d’abonnement**, **ID client**, **Secret client** et **Point de terminaison de jeton OAuth 2.0**. Définissez le champ **ID** sur **mySp**. Nous utilisons cet ID dans les étapes suivantes de cet article.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Configurer Jenkins pour déployer Web Apps en chargeant des fichiers

Pour déployer votre projet sur Web App, vous pouvez charger vos artefacts de build (par exemple, un fichier WAR en Java) à l’aide de Git ou FTP.

Avant de configurer le travail dans Jenkins, vous avez besoin d’un plan Azure App Service et d’une application web pour l’exécution de l’application Java.


1. Créez un plan Azure App Service avec le niveau tarifaire **GRATUIT** à l’aide de la [commande Azure CLI](/cli/azure/appservice/plan#az_appservice_plan_create) `az appservice plan create`. Le plan App Service définit les ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent ces ressources. Les ressources partagées vous aident à réduire les coûts lorsque vous hébergez plusieurs applications.
2. Créez une application web. Vous pouvez utiliser le [portail Azure](/azure/app-service-web/web-sites-configure) ou la commande Azure CLI `az` suivante :
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Définissez la configuration du runtime Java dont votre application a besoin. La commande Azure CLI suivante configure l’application web de manière à ce qu’elle s’exécute sur un JDK 8 récent et sur [Apache Tomcat](http://tomcat.apache.org/) version 8.0 :
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Configurer la tâche Jenkins

1. Créez un nouveau projet **freestyle** dans le tableau de bord Jenkins.
2. Configurez le champ **Gestion du code source** de sorte à utiliser votre duplication (fork) locale de [l’application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample). Renseignez la valeur **URL du référentiel**. Par exemple : http://github.com/&lt;your_ID>/javawebappsample.
3. Ajoutez une étape pour générer le projet à l’aide de Maven en ajoutant la commande **Execute shell**. Pour cet exemple, nous avons besoin d’une commande supplémentaire pour renommer le fichier \*.war dans le dossier cible et lui attribuer le nom **ROOT.war** :   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Ajoutez une action post-build en sélectionnant **Publier une application web Azure**.
5. Entrez **mySp** en tant que principal de service Azure. Ce principal a été stocké en tant que [Informations d’identification Azure](#service-principal) à l’étape précédente.
6. Dans **Configuration de l’application**, choisissez le groupe de ressources et l’application web compris dans votre abonnement. Le plug-in Jenkins détecte automatiquement si l’application web est une application Windows ou Linux. Pour une application web Windows, l’option **Publier les fichiers** est présentée.
7. Renseignez les fichiers que vous souhaitez déployer. Par exemple, spécifiez le package WAR si vous utilisez Java. Utilisez les paramètres facultatifs **Répertoire source** et **Répertoire cible** pour spécifier les dossiers source et cible à utiliser pour le chargement du fichier. L’application web Java sur Azure est exécutée sur un serveur Tomcat. Par conséquent, pour Java, vous chargez votre package WAR dans le dossier webapps. Pour cet exemple, définissez la valeur **Répertoire source** sur **target** et la valeur **Répertoire cible** sur **webapps**.
8. Pour déployer sur un emplacement autre qu’un emplacement de production, vous pouvez également définir le nom **Slot**.
9. Enregistrez le projet et générez-le. Votre application web est déployée sur Azure quand la build est terminée.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Déployer Web Apps en chargeant des fichiers à l’aide du pipeline Jenkins

Le plug-in Jenkins Azure App Service est compatible avec un pipeline. Vous pouvez vous référer à l’exemple suivant dans le référentiel GitHub.

1. Dans l’interface GitHub, ouvrez le fichier **Jenkinsfile_ftp_plugin**. Pour modifier le fichier, sélectionnez l’icône en forme de crayon. Mettez à jour les définitions **resourceGroup** et **webAppName** pour votre application web sur les lignes 11 et 12, respectivement :
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Configurez la définition **withCredentials** sur la ligne 14 sur l’ID des informations d’identification de votre instance Jenkins :
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Créer un pipeline Jenkins

1. Ouvrez Jenkins dans un navigateur web. Sélectionnez **Nouvel élément**.
2. Fournissez un nom pour le travail et sélectionnez **Pipeline**. Sélectionnez **OK**.
3. Sélectionnez l’onglet **Pipeline**.
4. Pour la valeur **Définition**, sélectionnez **Script de pipeline à partir de SCM**.
5. Pour la valeur **SCM**, sélectionnez **Git**. Entrez l’URL GitHub de votre référentiel dupliqué. Par exemple : https://&lt;your_forked_repo>.git.
6. Mettez à jour la valeur **Chemin du script** sur **Jenkinsfile_ftp_plugin**.
7. Sélectionnez **Enregistrer** et exécutez le travail.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configurer Jenkins pour déployer Web App pour conteneurs

Web Apps sur Linux prend en charge le déploiement à l’aide de Docker. Pour déployer votre application web à l’aide de Docker, vous devez fournir un fichier Docker qui intègre votre application web et un runtime du service dans une image Docker. Le plug-in Jenkins crée ensuite l’image, l’insère dans un registre Docker et déploie l’image sur votre application web.

Web Apps sur Linux prend également en charge les méthodes de déploiement traditionnelles telles que Git et FTP, mais uniquement pour les langages intégrés (.NET Core, Node.js, PHP et Ruby). Pour les autres langages, vous devez empaqueter le code de votre application et le runtime du service dans une image Docker et utiliser Docker pour le déploiement.

Avant de configurer le travail dans Jenkins, vous avez besoin d’une application web sur Linux. Vous avez également besoin d’un registre de conteneurs pour stocker et gérer vos images conteneurs Docker privées. Vous pouvez utiliser DockerHub pour créer le registre de conteneurs. Dans cet exemple, nous utilisons Azure Container Registry.

* [Créez votre application web sur Linux](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry est un service de [registre Docker](https://docs.docker.com/registry/) géré basé sur le Registre open source Docker version 2.0. [Créez un Azure Container Registry](/azure/container-registry/container-registry-get-started-azure-cli). Vous pouvez également utiliser DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Configurer le travail Jenkins pour Docker

1. Créez un nouveau projet **freestyle** dans le tableau de bord Jenkins.
2. Configurez le champ **Gestion du code source** de sorte à utiliser votre duplication (fork) locale de [l’application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample). Renseignez la valeur **URL du référentiel**. Par exemple : http://github.com/&lt;your_ID>/javawebappsample.
3. Ajoutez une étape pour générer le projet à l’aide de Maven en ajoutant une commande **Execute shell**. Incluez la ligne suivante dans la commande :
    ```bash
    mvn clean package
    ```

4. Ajoutez une action post-build en sélectionnant **Publier une application web Azure**.
5. Entrez **mySp** en tant que principal de service Azure. Ce principal a été stocké en tant que [Informations d’identification Azure](#service-principal) à l’étape précédente.
6. Dans la section **Configuration de l’application**, choisissez le groupe de ressources et une application web Linux compris dans votre abonnement.
7. Choisissez **Publier via Docker**.
8. Renseignez la valeur du chemin **Dockerfile**. Vous pouvez conserver la valeur /Dockerfile par défaut.
Pour la valeur **URL de Registre Docker**, entrez l’URL à l’aide du format https://&lt;yourRegistry>.azurecr.io si vous utilisez Azure Container Registry. Si vous utilisez DockerHub, laissez la valeur vide.
9. Pour la valeur **Informations d’identification du registre**, ajoutez celles du registre de conteneurs. Vous pouvez obtenir l’ID utilisateur et un mot de passe en exécutant les commandes suivantes dans Azure CLI. La première commande active le compte d’administrateur :
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Le nom et la balise de l’image Docker sous l’onglet **Avancé** sont facultatifs. Par défaut, la valeur du nom de l’image est obtenue à partir du nom de l’image que vous avez configuré dans le portail Azure dans le paramètre **Conteneur Docker**. La balise est générée à partir de $BUILD_NUMBER.
    > [!NOTE]
    > Veillez à spécifier le nom de l’image dans le portail Azure ou fournissez une valeur pour **Image Docker** sous l’onglet **Avancé**. Pour cet exemple, définissez la valeur **Image Docker** sur &lt;your_Registry>.azurecr.io/calculator et laissez la valeur **Balise d’image Docker** vide.

11. Le déploiement échoue si vous utilisez le paramètre Image Docker prédéfini. Modifiez la configuration Docker pour utiliser une image personnalisée dans le paramètre **Conteneur Docker** dans le portail Azure. Pour une image prédéfinie, utilisez l’approche de déploiement par chargement de fichier.
12. À l’instar de l’approche par chargement de fichier, vous pouvez choisir un nom **Emplacement** autre que **production**.
13. Enregistrez et générez le projet. Votre image conteneur est placée dans votre registre et l’application web est déployée.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Déployer Web App pour conteneurs à l’aide du pipeline Jenkins

1. Dans l’interface GitHub, ouvrez le fichier **Jenkinsfile_container_plugin**. Pour modifier le fichier, sélectionnez l’icône en forme de crayon. Mettez à jour les définitions **resourceGroup** et **webAppName** pour votre application web sur les lignes 11 et 12, respectivement :
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Remplacez la ligne 13 par votre serveur de registre de conteneurs :
    ```java
    def registryServer = '<registryURL>'
    ```

3. Modifiez la ligne 16 pour utiliser l’ID des informations d’identification dans votre instance Jenkins :
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Créer un pipeline Jenkins    

1. Ouvrez Jenkins dans un navigateur web. Sélectionnez **Nouvel élément**.
2. Fournissez un nom pour le travail et sélectionnez **Pipeline**. Sélectionnez **OK**.
3. Sélectionnez l’onglet **Pipeline**.
4. Pour la valeur **Définition**, sélectionnez **Script de pipeline à partir de SCM**.
5. Pour la valeur **SCM**, sélectionnez **Git**. Entrez l’URL GitHub de votre référentiel dupliqué. Par exemple : https://&lt;your_forked_repo>.git.
7. Mettez à jour la valeur **Chemin du script** sur **Jenkinsfile_container_plugin**.
8. Sélectionnez **Enregistrer** et exécutez le travail.

## <a name="verify-your-web-app"></a>Vérifier votre application web

1. Pour vérifier le déploiement du fichier WAR dans votre application web, ouvrez un navigateur web.
2. Accédez à http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Remplacez &lt;your_app_name> par le nom de votre application web. Le message suivant s’affiche :
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Accédez à http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Remplacez &lt;x> et &lt;y> par n’importe quels chiffres pour obtenir la somme de x + y. La calculatrice affiche la somme : ![Calculatrice : ajouter](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Pour Azure App Service sur Linux

1. Pour vérifier votre application web, exécutez la commande suivante dans Azure CLI :
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Le message suivant s’affiche :
    ```CLI
    ["calculator"]
    ```
    
2. Accédez à http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Remplacez &lt;your_app_name> par le nom de votre application web. Le message suivant s’affiche : 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Accédez à http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Remplacez &lt;x> et &lt;y> par n’importe quels chiffres pour obtenir la somme de x + y.
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé le plug-in Jenkins Azure App Service pour effectuer le déploiement sur Azure.

Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer Jenkins pour déployer Azure App Service via FTP 
> * Configurer Jenkins pour déployer sur Web App pour conteneurs 
