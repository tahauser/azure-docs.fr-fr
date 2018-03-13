---
title: "Utiliser une image Docker personnalisée pour Web App for Containers - Azure | Microsoft Docs"
description: "Comment utiliser une image Docker personnalisée pour Web App pour conteneurs."
keywords: azure app service, application web, linux, docker, conteneur
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b7f05f02249f86e6bee7f89c6ecf8016ede9c4fb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Utiliser une image Docker personnalisée pour Web App for Containers

[Web App for Containers](app-service-linux-intro.md) fournit des images Docker intégrées sur Linux avec prise en charge de versions spécifiques, comme PHP 7.0 et Node.js 4.5. Web App pour conteneurs utilise la technologie de conteneur Docker pour héberger à la fois des images intégrées et des images personnalisées en tant que service PaaS (platform as a service). Dans ce didacticiel, vous allez apprendre à générer une image Docker personnalisée et à la déployer sur Web App pour conteneurs. Ce modèle est utile quand les images intégrées n’incluent pas la langue de votre choix ou quand votre application nécessite une configuration spécifique qui n’est pas fournie dans les images intégrées.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer une image Docker personnalisée vers Azure
> * Configurer les variables d’environnement pour exécuter le conteneur
> * Mettre à jour l’image Docker et la redéployer
> * Se connecter au conteneur avec SSH
> * Déployer une image Docker privée vers Azure

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* [Git](https://git-scm.com/downloads)
* Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) actif
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [compte Docker Hub](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>Téléchargez l’exemple

Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner le référentiel de l’exemple d’application sur votre ordinateur local, puis accédez au répertoire contenant l’exemple de code.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Créer l’image à partir du fichier Docker

Dans le dépôt Git, examinez _Dockerfile_. Ce fichier décrit l’environnement Python exigé pour exécuter votre application. En outre, l’image définit un serveur [SSH](https://www.ssh.com/ssh/protocol/) pour sécuriser la communication entre le conteneur et l’hôte.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Pour créer l’image Docker, exécutez la commande `docker build`, puis indiquez un nom, _mydockerimage_ et une balise, _v1.0.0_. Remplacez la valeur _\<docker-id>_ par votre ID de compte Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

La commande génère des informations qui ressemblent à ce qui suit :

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Vérifiez que la build fonctionne en exécutant le conteneur Docker. Exécutez la commande [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) puis passez le nom et la balise de l’image. Veillez à spécifier le port à l’aide de l’argument `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Accédez à `http://localhost:2222` pour vérifier que l’application web et le conteneur fonctionnent correctement.

![Tester l’application web localement](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Envoyer (push) l’image Docker à Docker Hub

Un registre est une application qui héberge des images et fournit des services d’image et de conteneur. Pour partager votre image, vous devez la transférer vers un registre. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Transfert vers un registre Docker privé Consultez les instructions facultatives pour [Utiliser une image Docker depuis n’importe quel registre privé](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub est un registre d’images Docker qui vous permet d’héberger vos propres référentiels publics ou privés. Pour transférer une image Docker personnalisée vers le registre Docker Hub public, utilisez la commande [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) et indiquez le nom complet et la balise de l’image. L’exemple suivant montre un nom complet et une balise d’image :

```
<docker-id>/image-name:tag
```

Avant de transférer une image, vous devez vous connecter à Docker Hub par le biais de la commande [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). À l’invite de commandes dans la console, remplacez _\<docker-id>_ par votre nom de compte et tapez votre mot de passe.

```bash
docker login --username <docker-id>
```

Un message « login succeeded » (connexion réussie) confirme que vous êtes connecté. Une fois connecté, vous pouvez transférer l’image vers le registre Docker Hub à l’aide de la commande [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Vérifiez que le transfert a réussi en consultant les résultats de la commande.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Déployer des applications dans Azure

Vous pouvez héberger des applications Linux natives dans le cloud en utilisant Azure Web Apps. Pour créer une application Web App for Containers, vous devez exécuter les commandes CLI d’Azure qui créent un groupe, un plan de service puis l’application web elle-même. 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Créer un plan App Service Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Créer une application web

Dans Cloud Shell, créez une [application web](app-service-linux-intro.md) dans le plan App Service `myAppServicePlan` avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). N’oubliez pas de remplacer _<appname>_ par un nom d’application unique et _\<docker-ID>_ par votre ID Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

La plupart des images Docker ont des variables d’environnement qui doivent être configurées. Si vous utilisez une image Docker existante qui a été créée par une autre personne, cette image peut utiliser un port autre que le port 80. Utilisez le paramètre d’application `WEBSITES_PORT` pour indiquer à Azure le port utilisé par votre image. La page GitHub de l’[exemple Python dans ce didacticiel](https://github.com/Azure-Samples/docker-django-webapp-linux) montre que vous devez définir `WEBSITES_PORT` avec la valeur _8000_.

Pour définir les paramètres de l’application, utilisez la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Vous déployez à partir d’un registre Docker privé ? Consultez les instructions facultatives pour [Utiliser une image Docker depuis n’importe quel registre privé](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Tester l’application web

Accédez à `http://<app_name>azurewebsites.net` pour vérifier que l’application web fonctionne. 

![Test de la configuration du port de l’application web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Modification de l’application web et redéploiement

Dans votre dépôt Git local, ouvrez app/templates/app/index.html. Localisez le premier élément HTML et changez-le.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

Après avoir modifié et enregistré le fichier Python, vous devez reconstruire et transmettre la nouvelle image Docker. Redémarrez ensuite l’application web pour que les modifications prennent effet. Utilisez les mêmes commandes que vous avez utilisées précédemment dans ce didacticiel. Si vous le souhaitez, consultez [Créer l’image à partir du fichier Docker](#build-the-image-from-the-docker-file) et [Envoyer (push) l’image Docker à Docker Hub](#push-the-docker-image-to-docker-hub). Testez l’application web en suivant les instructions dans [Tester l’application web](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Se connecter à Web App for Containers à l’aide de SSH

SSH permet d’établir une communication sécurisée entre un conteneur et un client. Pour qu’une image Docker personnalisée prenne en charge SSH, vous devez l’intégrer à un fichier Docker. Vous activez SSH dans le fichier Docker lui-même. Les instructions SSH ayant déjà été ajoutées à l’exemple de fichier docker, vous pouvez appliquer ces instructions à votre propre image personnalisée :

* Une instruction [RUN](https://docs.docker.com/engine/reference/builder/#run) qui appelle `apt-get`, puis définit le mot de passe du compte racine sur `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Cette configuration n’autorise pas les connexions externes avec le conteneur. SSH est disponible uniquement via le site Kudu/SCM. Le site Kudu/SCM est authentifié avec des informations d’identification de publication.

* Une instruction [COPY](https://docs.docker.com/engine/reference/builder/#copy) qui indique au moteur Docker de copier le fichier [sshd_config](http://man.openbsd.org/sshd_config) dans le répertoire */etc/ssh/*. Votre fichier de configuration doit être basé sur [ce fichier sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Le fichier *sshd_config* doit inclure les éléments suivants : 
    > * `Ciphers` doit inclure au moins un élément dans cette liste : `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` doit inclure au moins un élément dans cette liste : `hmac-sha1,hmac-sha1-96`.

* Une instruction [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) qui expose le port 2222 dans le conteneur. Bien que le mot de passe racine soit connu, le port 2222 n’est pas accessible à partir d’Internet. Il s’agit d’un port interne accessible uniquement par les conteneurs au sein du réseau de pont d’un réseau privé virtuel. Après cela, les commandes copient les détails de la configuration SSH et démarrent le service `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* Veillez à [démarrer le service ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) à l’aide d’un script d’interpréteur de commandes dans le répertoire /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Ouvrir la connexion SSH au conteneur

Web App pour conteneurs n’autorise pas les connexions externes au conteneur. SSH est disponible uniquement par le biais du site Kudu, qui est accessible à l’adresse `https://<app_name>.scm.azurewebsites.net`.

Pour vous connecter, accédez à `https://<app_name>.scm.azurewebsites.net/webssh/host` et connectez-vous avec votre compte Azure.

Vous êtes ensuite redirigé vers une page affichant une console interactive. 

Vous pouvez vérifier que certaines applications sont en cours d’exécution dans le conteneur. Pour inspecter le conteneur et vérifier les processus en cours d’exécution, exécutez `top` à l’invite de commandes.

```bash
top
```

La commande `top` expose tous les processus en cours d’exécution dans un conteneur.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Félicitations ! Vous avez configuré une image Docker personnalisée destinée à Web App pour conteneurs.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Utiliser une image privée à partir de Docker Hub (facultatif)

Dans [Créer une application web](#create-a-web-app), vous avez spécifié une image sur Docker Hub dans la commande `az webapp create`. Cette procédure convient à une image publique. Pour utiliser une image privée, vous devez configurer l’ID et le mot de passe de votre compte Docker dans votre application web Azure.

Dans Cloud Shell, à la suite de la commande `az webapp create`, écrivez la commande [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Remplacez *\<app_name>*, mais aussi _\<<docker-id>_ et _\<password>_ par votre ID et votre mot de passe Docker.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

La commande fournit un résultat similaire à la chaîne JSON suivante, indiquant que la modification de configuration a réussi :

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Utiliser une image Docker à partir d’un registre privé (facultatif)

Dans cette section, vous allez apprendre à utiliser une image Docker à partir d’un registre privé dans Web App pour conteneurs. Le registre Azure Container Registry est utilisé comme exemple. Les étapes à suivre pour utiliser d’autres registres privés sont similaires. 

Azure Container Registry est un service Docker géré à partir d’Azure pour l’hébergement d’images privées. Les déploiements peuvent être de tout type, notamment [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) et Web App pour conteneurs. 

### <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Dans Cloud Shell, utilisez la commande [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az_acr_create) pour créer un registre de conteneurs Azure Container Registry. Transmettez le nom, le groupe de ressources et `Basic` pour la référence SKU. Les références disponibles sont `Classic`, `Basic`, `Standard`, et `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

La création d’un conteneur génère les résultats suivants :

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Connexion à Azure Container Registry

Pour transférer une image vers le registre, vous devez fournir les informations d’identification afin que le registre accepte le transfert. Vous pouvez récupérer ces informations d’identification à l’aide de la commande [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az_acr_show) dans Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

La commande fournit deux mots de passe qui peuvent être utilisés avec le nom d’utilisateur.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Dans la fenêtre de terminal locale, connectez-vous à Azure Container Registry à l’aide de la commande `docker login`. Le nom du serveur est obligatoire pour se connecter. Utilisez le format `{azure-container-registry-name>.azurecr.io`. À l’invite, entrez votre mot de passe dans la console.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Vérifiez que la connexion a réussi. 

### <a name="push-an-image-to-azure-container-registry"></a>Envoyer (push) une image à Azure Container Registry

> [!NOTE]
> Si vous utilisez votre propre image, marquer d’une balise l’image comme suit :
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Envoyez l’image à l’aide de la commande `docker push`. Identifiez l’image avec le nom du registre, suivi du nom et de la balise de l’image.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Vérifiez que le transfert a correctement ajouté un conteneur au registre en répertoriant les référentiels ACR. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

L’affichage des images dans le registre confirme que `mydockerimage` figure dans le registre.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configurer Web App pour utiliser l’image à partir d’Azure Container Registry (ou tout autre registre privé)

Vous pouvez configurer Web App pour conteneurs pour qu’il exécute un conteneur stocké dans Azure Container Registry. Azure Container Registry s’utilise comme n’importe quel registre privé. Si vous devez utiliser votre propre registre privé, les étapes pour effectuer cette tâche sont donc similaires.

Dans Cloud Shell, exécutez [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) pour afficher le nom d’utilisateur et le mot de passe pour Azure Container Registry. Copiez le nom d’utilisateur et l’un des mots de passe afin de les utiliser pour configurer l’application web à l’étape suivante.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

Dans Cloud Shell, exécutez la commande [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) pour affecter l’image Docker personnalisée à l’application web. Remplacez *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ et _\<password>_. Pour Azure Container Registry, *\<docker-registry-server-url>* est au format `https://<azure-container-registry-name>.azurecr.io`. Si vous utilisez un registre autre que Docker Hub, le nom de l’image doit commencer par le nom de domaine complet (FQDN) de votre registre. Dans Azure Container Registry, cela ressemble à `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` est obligatoire dans *\<docker-registry-server-url>*.
>

La commande fournit un résultat similaire à la chaîne JSON suivante, indiquant que la modification de configuration a réussi :

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web Docker Python et PostgreSQL dans Azure](tutorial-docker-python-postgresql-app.md)
