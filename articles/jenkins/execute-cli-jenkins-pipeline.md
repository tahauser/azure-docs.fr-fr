---
title: "Exécuter l’interface de ligne de commande Azure avec Jenkins | Microsoft Docs"
description: "Découvrez comment utiliser l’interface CLI Azure pour déployer une application web Java vers Azure dans un pipeline Jenkins"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Déploiement dans Azure App Service avec Jenkins et l’interface CLI Azure
Pour déployer une application web Java dans Azure, vous pouvez utiliser l’interface CLI Azure dans le [pipeline Jenkins](https://jenkins.io/doc/book/pipeline/). Dans ce didacticiel, vous créez un pipeline CI/CD sur une machine virtuelle Azure et apprenez notamment comment :

> [!div class="checklist"]
> * Créer une machine virtuelle Jenkins
> * Configurer Jenkins
> * Créer une application web dans Azure
> * Préparer un dépôt GitHub
> * Créer un pipeline Jenkins
> * Exécuter le pipeline et vérifier l’application web

Ce didacticiel requiert Azure CLI version 2.0.4 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Créer et configurer l’instance Jenkins
Si vous ne disposez pas encore d’un serveur maître Jenkins, commencez par utiliser le [modèle de solution](install-jenkins-solution-template.md), qui inclut le plug-in requis [Informations d’identification Azure](https://plugins.jenkins.io/azure-credentials) par défaut. 

Le plug-in Informations d’identification Azure vous permet de stocker les informations d’identification du principal de service Microsoft Azure dans Jenkins. Dans la version 1.2, nous avons ajouté la prise en charge afin que le pipeline Jenkins puisse obtenir les informations d’identification Azure. 

Vérifiez que vous disposez de la version 1.2 ou d’une version ultérieure :
* Dans le tableau de bord Jenkins, cliquez sur **Gérer Jenkins -> Gestionnaire de plug-in ->** et recherchez **Informations d’identification Azure**. 
* Mettez à jour le plug-in si la version est antérieure à la version 1.2.

Java JDK et Maven sont également requis dans le serveur maître Jenkins. Pour les installer, connectez-vous au serveur maître Jenkins à l’aide de SSH et exécutez les commandes suivantes :
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Ajout du principal de service Azure aux informations d’identification Jenkins

Les informations d’identification Azure sont nécessaires pour exécuter l’interface CLI Azure.

* Dans le tableau de bord Jenkins, cliquez sur **Informations d’identification > Système**. Cliquez sur **Informations d’identification globales (sans restriction)**.
* Cliquez sur **Ajouter des informations d’identification** pour ajouter un [principal de service Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) en renseignant les valeurs suivantes : ID d’abonnement, ID de client, secret client et point de terminaison de jeton OAuth 2.0. Fournissez un ID qui sera utilisé dans une étape ultérieure.

![Ajout d’informations d'identification](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Créer un plan Azure App Service pour déployer l’application web Java

Créez un plan Azure App Service avec le niveau tarifaire **Gratuit** à l’aide de la commande d’interface de ligne de commande [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create). Le plan App Service définit les ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent ces ressources, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Lorsque le plan est prêt, l’interface de ligne de commande Azure affiche une sortie similaire à l’exemple suivant :

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Création d’une application web Azure

 Utilisez la commande d’interface de ligne de commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) pour créer une définition d’application web dans le plan App Service `myAppServicePlan`. La définition d’application web fournit une URL pour accéder à votre application et permet de configurer plusieurs options pour déployer votre code dans Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Remplacez l’espace réservé `<app_name>` avec votre propre nom d’application unique. Ce nom unique est utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez mapper une entrée de nom de domaine personnalisée vers l’application web avant de l’exposer à vos utilisateurs.

Une fois que la définition de l’application web est prête, l’interface de ligne de commande Azure affiche des informations similaires à celles de l’exemple suivant : 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurer Java 

Définissez la configuration d’exécution Java dont votre application a besoin avec la commande [az appservice web config update](/cli/azure/appservice/web/config#az_appservice_web_config_update).

La commande suivante configure l’application web de manière à ce qu’elle s’exécute sur un JDK Java 8 récent et sur [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Préparer un dépôt GitHub
Ouvrez le dépôt [Application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample). Pour répliquer le dépôt sur votre propre compte GitHub, cliquez sur le bouton **Bifurcation** en haut à droite.

* Dans l’interface utilisateur web de GitHub, ouvrez le fichier **Jenkinsfile**. Cliquez sur l’icône du crayon pour modifier ce fichier et mettre à jour le groupe de ressources, ainsi que le nom de votre application web sur les lignes 20 et 21, respectivement.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Modifiez la ligne 23 pour mettre à jour l’ID des informations d’identification dans votre instance Jenkins

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Créer un pipeline Jenkins
Ouvrez Jenkins dans un navigateur web, cliquez sur **Nouvel élément**. 

* Fournissez un nom pour le travail et sélectionnez **Pipeline**. Cliquez sur **OK**.
* Ensuite, cliquez sur l’onglet **Pipeline**. 
* Pour **Définition**, sélectionnez **Script de pipeline à partir de SCM**.
* Pour **SCM**, sélectionnez **Git**.
* Entrez l’URL GitHub de votre dépôt dupliqué : https:\<votre dépôt dupliqué\>.git
* Cliquez sur **Enregistrer**.

## <a name="test-your-pipeline"></a>Tester votre pipeline
* Accédez au pipeline que vous avez créé, cliquez sur **Générer maintenant**
* En quelques secondes, une build doit être générée. Vous pouvez y accéder et cliquer sur **Sortie de la console** pour afficher les détails

## <a name="verify-your-web-app"></a>Vérifier votre application web
Pour vérifier le déploiement du fichier WAR dans votre application web. Ouvrez un navigateur web :

* Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Vous voyez :

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (remplacez &lt;x> et &lt;y> par n’importe quels chiffres) pour obtenir la somme de x et de y

![Calculatrice : ajouter](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Déployer dans une application web Azure sous Linux
Maintenant que vous savez comment utiliser l’interface CLI Azure dans votre pipeline Jenkins, vous pouvez modifier le script pour déployer dans une application web Azure sous Linux.

L’application web sous Linux prend en charge une méthode différente pour le déploiement, à savoir l’utilisation de Docker. Pour déployer, vous devez fournir un fichier Docker qui intègre votre application web et le runtime du service dans une image Docker. Le plug-in crée ensuite l’image, l’insère dans un registre Docker et déploie l’image dans votre application web.

* Suivez les étapes décrites [ici](../app-service/containers/quickstart-nodejs.md) pour créer une application web Azure qui s’exécute sous Linux.
* Installez Docker sur votre instance Jenkins en suivant les instructions dans cet [article](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Créez un registre de conteneur dans le portail Azure à l’aide des étapes décrites [ici](/azure/container-registry/container-registry-get-started-azure-cli).
* Dans le même dépôt dupliqué [Application web Java simple pour Azure](https://github.com/azure-devops/javawebappsample), modifiez le fichier **Jenkinsfile2** :
    * Mettez à jour les lignes 18 à 21 à l’aide du nom de votre groupe de ressources, de votre application web et de l’enregistrement de contrôle d’accès, respectivement. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Mettez à jour \<azsrvprincipal\> sur la ligne 24 à l’aide de vos informations d’identification
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Créez un pipeline Jenkins comme lors du déploiement dans l’application web Azure sous Windows, en utilisant **Jenkinsfile2** à la place.
* Exécutez votre nouveau travail.
* Pour vérifier, dans l’interface CLI Azure, exécutez :

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Vous obtenez le résultat suivant :
    
    ```
    [
    "calculator"
    ]
    ```
    
    Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/ping. Le message suivant s’affiche : 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Accédez à http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (remplacez &lt;x> et &lt;y> par n’importe quels chiffres) pour obtenir la somme de x et de y
    
## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un pipeline Jenkins qui extrait le code source dans le dépôt GitHub. Il exécute Maven pour générer un fichier war et utilise ensuite l’interface CLI Azure pour le déploiement vers Azure App Service. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Jenkins
> * Configurer Jenkins
> * Créer une application web dans Azure
> * Préparer un dépôt GitHub
> * Créer un pipeline Jenkins
> * Exécuter le pipeline et vérifier l’application web
