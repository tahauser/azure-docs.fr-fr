---
title: Créer une fonction sur Linux à l’aide d’une image personnalisée (préversion) | Microsoft Docs
description: Découvrez comment créer une exécution d’Azure Functions sur une image Linux personnalisée.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 758906126b42c103853e0047bb19d2e96a84fae6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Créer une fonction sur Linux en utilisant une image personnalisée (préversion)

Azure Functions vous permet d’héberger vos fonctions sur Linux dans votre propre conteneur personnalisé. Vous pouvez également [héberger dans un conteneur Azure App Service par défaut](functions-create-first-azure-function-azure-cli-linux.md). Cette fonctionnalité est actuellement en version préliminaire et requiert le [runtime Functions 2.0](functions-versions.md), qui est également en version préliminaire.

Dans ce didacticiel, vous apprenez à déployer une application de fonction en tant qu’image personnalisée de Docker. Ce modèle est utile lorsque vous avez besoin de personnaliser l’image de conteneur intégrée d’App Service. Une image personnalisée peut vous être utile lorsque vos fonctions nécessitent une version de langue spécifique, une dépendance particulière ou une configuration qui n’est pas fournie dans l’image intégrée.

Ce didacticiel vous guide tout au long de l’utilisation d’Azure Functions pour créer et distribuer une image personnalisée vers Docker Hub. Utilisez ensuite cette image comme source de déploiement pour une application de fonction qui s’exécute sur Linux. Servez-vous de Docker pour générer et distribuer l’image. Azure CLI vous permet de créer une application de fonction et de déployer l’image à partir de Docker Hub. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une image personnalisée à l’aide de Docker
> * Publier une image personnalisée dans un registre de conteneurs 
> * Création d’un compte Azure Storage. 
> * Créer un plan App Service Linux 
> * Déployer une application de fonction à partir de Docker Hub
> * Ajouter des paramètres d’application à l’application de fonction 

Les étapes suivantes sont prises en charge sur un ordinateur Mac, Windows ou Linux.  

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Git](https://git-scm.com/downloads)
* Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) actif
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [compte Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Créer l’image à partir du fichier Docker

Dans ce dépôt Git, examinez le _Dockerfile_. Ce fichier décrit l’environnement exigé pour exécuter l’application de fonction sur Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> Lors de l’hébergement d’une image dans un registre de conteneurs privé, vous devez ajouter les paramètres de connexion à l’application de fonction en utilisant les variables **ENV** dans le Dockerfile. Étant donné que ce didacticiel ne peut pas garantir que vous utilisez un registre privé, en tant que bonne pratique de sécurité, les paramètres de connexion sont [ajoutés après le déploiement par le biais d’Azure CLI](#configure-the-function-app).   

### <a name="run-the-build-command"></a>Exécuter la commande Build
Pour créer l’image Docker, exécutez la commande `docker build`, puis indiquez un nom, `mydockerimage`, et une balise, `v1.0.0`. Remplacez `<docker-id>` par votre ID de compte Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

La commande génère des informations qui ressemblent à ce qui suit :

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Tester localement l’image
Vérifiez que l’image générée fonctionne en exécutant l’image Docker dans un conteneur local. Exécutez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) puis passez le nom et la balise de l’image. Veillez à spécifier le port à l’aide de l’argument `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Tandis que l’image personnalisée s’exécute dans un conteneur Docker local, vérifiez que l’application de fonction et le conteneur fonctionnent correctement en accédant à <http://localhost:8080>.

![Testez localement l’application de fonction.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Une fois que vous avez vérifié l’application de fonction dans le conteneur, arrêtez l’exécution. À présent, vous pouvez transmettre l’image personnalisée à votre compte Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Envoyer (push) l’image personnalisée à Docker Hub

Un registre est une application qui héberge des images et fournit des services d’image et de conteneur. Pour partager votre image, vous devez la transférer vers un registre. Docker Hub est un registre d’images Docker qui vous permet d’héberger vos propres référentiels publics ou privés. 

Avant de pousser une image, vous devez vous connecter à Docker Hub par le biais de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/). À l’invite de commandes dans la console, remplacez `<docker-id>` par votre nom de compte et tapez votre mot de passe. Pour les autres options de mot de passe Docker Hub, consultez la [documentation de la commande docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Un message « login succeeded » (connexion réussie) confirme que vous êtes connecté. Une fois connecté, vous pouvez transférer l’image à Docker Hub au moyen de la commande [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Vérifiez que le transfert a réussi en consultant les résultats de la commande.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Vous pouvez désormais utiliser cette image comme source de déploiement pour une nouvelle application de fonction dans Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, Azure CLI version 2.0.21 ou ultérieure est indispensable pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version qui est à votre disposition. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

L’hébergement Linux pour Functions n’est pas actuellement pris en charge dans les plans Consommation. Vous devez exécuter un plan App Service Linux. Pour en savoir plus sur l’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Créer et déployer l’image personnalisée

L’application de fonction héberge l’exécution de vos fonctions. Créez une application de fonction à partir de l’image Docker Hub à l’aide de la commande [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

Dans la commande suivante, indiquez un nom d’application de fonction unique là où se trouve l’espace réservé `<app_name>`, et le nom du compte de stockage pour `<storage_name>`. La valeur `<app_name>` est utilisée en tant que domaine DNS par défaut pour la Function App. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Comme précédemment, `<docker-id>` est le nom de votre compte Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
Une fois la Function App créée, Azure CLI affiche des informations semblables à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Le paramètre _deployment-container-image-name_ indique l’image hébergée sur Docker Hub à utiliser pour créer l’application de fonction. 


## <a name="configure-the-function-app"></a>Configurer l’application de fonction

La fonction a besoin que la chaîne de connexion se connecte au compte de stockage par défaut. Lorsque vous publiez votre image personnalisée sur un compte de conteneur privé, préférez plutôt définir ces paramètres d’application comme des variables d’environnement dans le Dockerfile au moyen de l’[instruction ENV](https://docs.docker.com/engine/reference/builder/#env), ou équivalent. 

Dans ce cas, `<storage_account>` est le nom du compte de stockage Blob que vous avez créé. Pour afficher la chaîne de connexion, exécutez la commande [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Ajoutez ces paramètres d’application dans l’application de fonction à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Vous pouvez désormais tester vos fonctions qui s’exécutent sur Linux dans Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une image personnalisée à l’aide de Docker
> * Publier une image personnalisée dans un registre de conteneurs 
> * Création d’un compte Azure Storage. 
> * Créer un plan App Service Linux 
> * Déployer une application de fonction à partir de Docker Hub
> * Ajouter des paramètres d’application à l’application de fonction

Découvrez plus en détail le développement local d’Azure Functions avec les principaux outils d’Azure Functions.

> [!div class="nextstepaction"] 
> [Coder et tester Azure Functions localement](functions-run-local.md)
