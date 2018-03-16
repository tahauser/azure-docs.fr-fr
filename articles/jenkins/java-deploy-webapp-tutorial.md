---
title: "Utiliser Jenkins pour déployer vos applications web dans Azure | Microsoft Docs"
description: "Configurez l’intégration continue de GitHub dans Azure App Service pour vos applications web Java à l’aide de Jenkins et Docker."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Configurer l’intégration et le déploiement continus d’Azure App Service avec Jenkins

Ce didacticiel permet de configurer l’intégration et le déploiement continus (CI/CD) d’un exemple d’application web Java développée avec le framework [Spring Boot](http://projects.spring.io/spring-boot/) dans [Azure App Service Web App sur Linux](/azure/app-service/containers/app-service-linux-intro) à l’aide de Jenkins.

Dans ce didacticiel, vous allez effectuer les tâches suivantes :

> [!div class="checklist"]
> * Installer les plug-ins Jenkins nécessaires au déploiement dans Azure App Service.
> * Définir un travail Jenkins pour générer des images Docker à partir d’un dépôt GitHub quand une nouvelle validation est transmise.
> * Définir une nouvelle application Azure Web App pour Linux et la configurer pour déployer les images Docker transmises à Azure Container Registry.
> * Configurer le plug-in Azure App Service Jenkins.
> * Déployer l’exemple d’application dans Azure App Service avec une build manuelle.
> * Déclencher une build Jenkins et mettre à jour l’application web en transmettant les modifications à GitHub.

## <a name="before-you-begin"></a>Avant de commencer

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Jenkins](https://jenkins.io/) avec les outils JDK et Maven configurés. Si vous n’avez pas de système Jenkins, créez-en un maintenant dans Azure à partir du [modèle de solution Jenkins](/azure/jenkins/install-jenkins-solution-template).
* Un compte [GitHub](https://github.com).
* [Azure CLI 2.0](/cli/azure), à partir de votre ligne de commande locale ou dans [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Installer les plug-ins Jenkins

1. Dans un navigateur web, ouvrez votre console web Jenkins, sélectionnez **Manage Jenkins** (Gérer Jenkins) dans le menu de gauche, puis sélectionnez **Manage Plugins** (Gérer les plug-ins).
2. Sélectionnez l’onglet **Available** (Disponible).
3. Repérez et cochez la case en regard des plug-ins suivants :   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source)

    Si les plug-ins n’apparaissent pas, vérifiez sous l’onglet **Installed** (Installé) qu’ils ne sont pas déjà installés.

1. Sélectionnez **Download now and install after restart** (Télécharger maintenant et installer après le redémarrage) pour activer les plug-ins dans votre configuration Jenkins.

## <a name="configure-github-and-jenkins"></a>Configurer GitHub et Jenkins

Configurez Jenkins pour recevoir les [webhooks GitHub](https://developer.github.com/webhooks/) quand de nouvelles validations sont transmises à un dépôt de votre compte.

1. Sélectionnez **Manage Jenkins** (Gérer Jenkins), puis **Configure System** (Configurer le système). Dans la section **GitHub**, vérifiez que **Manage hooks** (Gérer les hooks) est sélectionné, puis choisissez **Manage additional GitHub actions** (Gérer des actions GitHub supplémentaires) et **Convert login and password to token** (Convertir le compte de connexion et le mot de passe en jeton).
2. Sélectionnez la case d’option **From login and password** (À partir du compte de connexion et du mot de passe), puis entrez vos nom d’utilisateur et mot de passe GitHub. Sélectionnez **Create token credentials** (Créer des informations d’identification de jeton) pour créer un nouveau jeton d’accès personnel GitHub ([GitHub Personal Access Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)).   
   ![Créer un jeton PAT GitHub à partir du compte de connexion et du mot de passe](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Sélectionnez le jeton nouvellement créé dans la liste déroulante **Credentials** (Informations d’identification) de la configuration de serveurs GitHub. Sélectionnez **Test connection** (Tester la connexion) pour vérifier que l’authentification fonctionne.   
   ![Vérifier la connexion à GitHub une fois que le jeton PAT est configuré](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Si l’authentification à deux facteurs est activée pour votre compte GitHub, créez le jeton sur GitHub et configurez Jenkins pour qu’il l’utilise. Pour plus d’informations, consultez la documentation du [plug-in Jenkins GitHub](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Dupliquer (fork) l’exemple de dépôt et créer un travail Jenkins 

1. Ouvrez le [dépôt de l’exemple d’application Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) et dupliquez-le dans votre propre compte GitHub en sélectionnant **Fork** (Dupliquer) en haut à droite.   
    ![Dupliquer (fork) à partir de GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. Dans la console web Jenkins, sélectionnez **New Item** (Nouvel élément), nommez-le **MyJavaApp**, sélectionnez **Freestyle project** (Projet Freestyle), puis **OK**.   
    ![Nouveau projet libre (Freestyle) Jenkins](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Dans la section **General** (Général), sélectionnez le projet **GitHub**, puis entrez l’URL de votre dépôt dupliqué (par exemple, https://github.com/raisa/gs-spring-boot-docker).
3. Dans la section **Source code management** (Gestion du code source), sélectionnez **Git**, puis entrez l’URL `.git` de votre dépôt dupliqué (par exemple, https://github.com/raisa/gs-spring-boot-docker).
4. Dans la section **Déclencheurs de génération**, sélectionnez **Déclencher un hook GitHub pour l’interrogation GITScm**.
5. Dans la section **Build** (Générer), sélectionnez **Add build step** (Ajouter une étape de génération), puis choisissez **Invoke top-level Maven targets** (Appeler des cibles Maven de niveau supérieur). Entrez `package` dans le champ **Goals** (Objectifs).
6. Sélectionnez **Enregistrer**. Vous pouvez tester votre travail en sélectionnant **Build Now** (Générer maintenant) dans la page du projet.

## <a name="configure-azure-app-service"></a>Configurer Azure App Service 

1. À partir d’Azure CLI ou de [Cloud Shell](/azure/cloud-shell/overview), créez une application [Web App sur Linux](/azure/app-service/containers/app-service-linux-intro). Dans ce didacticiel, l’application web s’appelle `myJavaApp`, mais vous devez utiliser un nom distinctif pour votre propre application.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Créez un registre de conteneurs [Azure Container Registry](/azure/container-registry/container-registry-intro) pour stocker les images Docker générées par Jenkins. Celui utilisé dans ce didacticiel s’appelle `jenkinsregistry`, mais vous devez utiliser un nom distinctif pour votre propre registre de conteneurs. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configurez l’application web de sorte qu’elle exécute les images Docker transmises au registre de conteneurs et indiquez que l’application s’exécutant dans le conteneur écoute les demandes sur le port 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Configurer le plug-in Azure App Service Jenkins

1. Dans la console web Jenkins, sélectionnez le travail **MyJavaApp** que vous avez créé, puis sélectionnez **Configure** (Configurer) dans la page de gauche.
2. Faites défiler l’écran vers le bas jusqu’à **Post-build Actions** (Actions post-build) et sélectionnez **Add post-build action** (Ajouter une action post-build), puis **Publish an Azure Web App** (Publier une application Azure Web App).
3. Sous **Azure Profile Configuration** (Configuration de profil Azure), sélectionnez **Add** (Ajouter) en regard de **Azure Credentials** (Informations d’identification Azure), puis choisissez **Jenkins**.
4. Dans la boîte de dialogue **Add Credentials** (Ajouter des informations d’identification), sélectionnez **Microsoft Azure Service Principal** (Principal du service Microsoft Azure) dans la liste déroulante **Kind** (Type).
5. Créez un principal du service Active Directory à partir d’Azure CLI ou de [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Entrez les informations d’identification du principal du service dans la boîte de dialogue **Add credentials** (Ajouter des informations d’identification). Si vous ne connaissez pas l’ID de votre abonnement Azure, vous pouvez le demander à partir de l’interface CLI :
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configurer le principal du service Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Vérifiez que le principal du service s’authentifie auprès d’Azure en sélectionnant **Verify Service Principal** (Vérifier le principal du service). 
7. Sélectionnez **Add** (Ajouter) pour enregistrer les informations d’identification.
8. Sélectionnez les informations d’identification du principal du service que vous venez d’ajouter dans la liste déroulante **Azure Credentials** (Informations d’identification Azure) quand vous revenez à l’étape de configuration **Publish an Azure Web App** (Publier une application Azure Web App).
9. Dans **App Configuration** (Configuration de l’application), choisissez votre groupe de ressources et le nom de l’application web dans la liste déroulante.
10. Sélectionnez la case d’option **Publish via Docker** (Publier via Docker).
11. Entrez `complete/Dockerfile` dans **Dockerfile path** (Chemin du fichier Docker).
12. Entrez `https://jenkinsregistry.azurecr.io` dans le champ **Docker registry URL** (URL du registre Docker).
13. Sélectionnez **Add** (Ajouter) en regard de **Registry Credentials** (Informations d’identification du registre). 
14. Dans **Username** (Nom d’utilisateur), entrez le nom d’utilisateur de l’administrateur du registre de conteneurs Azure Container Registry que vous avez créé.
15. Dans le champ **Password** (Mot de passe), entrez le mot de passe pour le registre de conteneurs Azure Container Registry. Vous pouvez obtenir le nom d’utilisateur et le mot de passe à partir du portail Azure ou via la commande CLI suivante :

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Ajouter les informations d’identification de votre registre de conteneurs](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Sélectionnez **Add** (Ajouter) pour enregistrer les informations d’identification.
16. Sélectionnez les informations d’identification nouvellement créées dans la liste déroulante **Registry credentials** (Informations d’identification du registre) du panneau **App Configuration** (Configuration de l’application) pour **Publish an Azure Web App** (Publier une application Azure Web App). Une fois terminée, l’action post-build doit ressembler à l’image suivante :   
    ![Configuration de l’action post-build pour le déploiement Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Sélectionnez **Save** (Enregistrer) pour enregistrer la configuration du travail.

## <a name="deploy-the-app-from-github"></a>Déployer l’application à partir de GitHub

1. Dans le projet Jenkins, sélectionnez **Build Now** (Générer maintenant) pour déployer l’exemple d’application dans Azure.
2. Une fois la génération terminée, votre application est en ligne dans Azure à son adresse URL de publication, par exemple http://myjavaapp.azurewebsites.net.   
   ![Afficher votre application déployée dans Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Envoyer (push) les modifications et redéployer

1. À partir de votre duplication (fork) Github, naviguez sur le web jusqu’à `complete/src/main/java/Hello/Application.java`. Sélectionnez le lien **Edit this file** (Modifier ce fichier) dans la partie droite de l’interface GitHub.
2. Apportez la modification suivante à la méthode `home()`, puis validez-la dans la branche principale du dépôt.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Une nouvelle build démarre dans Jenkins, déclenchée par la nouvelle validation dans la branche `master` du dépôt. À la fin de l’opération, rechargez votre application dans Azure.     
      ![Afficher votre application déployée dans Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Étapes suivantes

- [Utiliser des machines virtuelles Azure comme agents de build](/azure/jenkins/jenkins-azure-vm-agents)
- [Gérer les ressources dans les travaux et les pipelines avec Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline)
 
