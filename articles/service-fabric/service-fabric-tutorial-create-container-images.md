---
title: "Créer des images conteneurs pour Azure Service Fabric | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrez comment créer des images conteneurs pour une application Service Fabric à conteneurs multiples."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Conteneurs, Microservices, Service Fabric, Azure
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 13cf13ce4a1456731d08f356ca405119ce1a6480
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-create-container-images-for-service-fabric"></a>Didacticiel : Créer des images conteneurs pour Service Fabric

Ce didacticiel est la première partie d’une série qui montre comment utiliser des conteneurs dans un cluster Service Fabric Linux. Dans le premier didacticiel, vous allez préparer une application à conteneurs multiples afin de pouvoir l’utiliser avec Service Fabric. Dans les didacticiels suivants, ces images seront utilisées dans le cadre d’une application Service Fabric. Ce didacticiel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Cloner la source de l’application à partir de GitHub  
> * Créer une image conteneur à partir de la source de l’application
> * Déployer une instance Azure Container Registry (ACR)
> * Marquer une image conteneur pour ACR
> * Charger l’image dans ACR

Dans cette série de didacticiels, nous allons aborder les points suivants : 

> [!div class="checklist"]
> * Créer des images conteneurs pour Service Fabric
> * [Créer et exécuter une application Service Fabric avec des conteneurs](service-fabric-tutorial-package-containers.md)
> * [Gestion du basculement et de la mise à l’échelle dans Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>configuration requise

- Configuration de l’environnement de développement Linux pour Service Fabric. Suivez les instructions [ici](service-fabric-get-started-linux.md) pour configurer votre environnement Linux. 
- Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 
- Il exige également que vous disposiez d’un abonnement Azure. Pour plus d’informations sur la version d’évaluation gratuite, rendez-vous [ici](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple d’application utilisé dans ce didacticiel est une application de vote. L’application est constituée d’un composant web frontal et d’une instance Redis principale. Les composants sont empaquetés dans des images conteneurs. 

Utilisez git pour télécharger une copie de l’application dans votre environnement de développement.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

La solution contient deux dossiers et un fichier docker-compose.yml. Le dossier « azure-vote » contient le service frontal Python, ainsi que le fichier Dockerfile utilisé pour créer l’image. Le répertoire « Voting » contient le package d’application Service Fabric qui est déployé sur le cluster. Ces répertoires contiennent les ressources nécessaires à ce didacticiel.  

## <a name="create-container-images"></a>Créer des images de conteneur

Dans le répertoire **azure-vote**, exécutez la commande suivante pour créer l’image associée au composant web frontal. Cette commande utilise pour cela le fichier Dockerfile présent dans ce répertoire. 

```bash
docker build -t azure-vote-front .
```

Cette commande peut prendre un certain temps, car toutes les dépendances nécessaires doivent être extraites du Hub Docker. Une fois terminé, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/) pour afficher les images créées.

```bash
docker images
```

Notez que les deux images ont été téléchargées ou créées. L’image *azure-vote-front* contient l’application. Elle est dérivée d’une image *python* provenant de Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Déployer Azure Container Registry

Exécutez tout d’abord la commande **az login** pour vous connecter à votre compte Azure. 

```bash
az login
```

Ensuite, utilisez la commande **az account** afin de choisir votre abonnement pour créer le registre Azure Container Registry. Vous devez entrer votre ID d’abonnement Azure à la place de <id_abonnement>. 

```bash
az account set --subscription <subscription_id>
```

Lorsque vous déployez un registre de conteneurs Azure, il vous faut tout d’abord un groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande **az group create**. Dans cet exemple, un groupe de ressources nommé *myResourceGroup* est créé dans la région *westus*.

```bash
az group create --name <myResourceGroup> --location westus
```

Créez un registre de conteneurs Azure à l’aide de la commande **az acr create**. Remplacez \<acrName > par le nom du registre de conteneurs que vous voulez créer dans votre abonnement. Ce nom doit être alphanumérique et unique. 

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Dans le reste de ce didacticiel, nous utilisons « acrName ». Ce nom correspond au registre de conteneurs que vous avez choisi. Prenez note de cette valeur. 

## <a name="log-in-to-your-container-registry"></a>Se connecter au registre de conteneurs

Connectez-vous à votre instance ACR avant de lui transmettre des images. Utilisez la commande **az acr login** pour terminer l’opération. Fournissez le nom unique qui a été donné au registre de conteneurs au moment de sa création.

```bash
az acr login --name <acrName>
```

Après son exécution, la commande retourne le message « Connexion réussie ».

## <a name="tag-container-images"></a>Marquer les images de conteneur

Chaque image conteneur doit être marquée avec le nom de serveur de connexion du registre. Cette balise est utilisée pour l’acheminement lors de l’envoi des images de conteneur dans un registre d’images.

Pour afficher la liste des images actuelles, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Pour obtenir le nom de loginServer, exécutez la commande suivante :

```bash
az acr show --name <acrName> --query loginServer --output table
```

Une table avec les résultats suivants est générée. Ce résultat sera utilisé pour marquer votre image **azure-vote-front** avant de la placer dans le registre de conteneurs à l’étape suivante.

```bash
Result
------------------
<acrName>.azurecr.io
```

Maintenant, marquez l’image *azure-vote-front* avec le loginServer de votre registre de conteneurs. En outre, ajoutez `:v1` à la fin du nom de l’image. Cette balise indique la version de l’image.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Une fois le marquage effectué, exécutez « docker images » pour vérifier l’opération.


Output:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Envoyer des images au registre

Envoyez l’image *azure-vote-front* au registre. 

À l’aide de l’exemple suivant, remplacez le nom d’ACR loginServer par le loginServer à partir de votre environnement.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Les commandes Docker de type push prennent quelques minutes.

## <a name="list-images-in-registry"></a>Créer la liste des images du registre

Pour retourner une liste d’images qui ont été déplacées dans le registre de conteneurs Azure, utilisez la commande [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list). Mettez à jour la commande avec le nom d’instance ACR.

```bash
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
azure-vote-front
```

Au terme de ce didacticiel, l’image conteneur est stockée dans une instance privée Azure Container Registry. Dans les didacticiels suivants, l’image est déployée d’ACR vers un cluster Service Fabric.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, une application a été extraite de GitHub et des images conteneurs ont été créées et transmises à un registre. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Cloner la source de l’application à partir de GitHub  
> * Créer une image conteneur à partir de la source de l’application
> * Déployer une instance Azure Container Registry (ACR)
> * Marquer une image conteneur pour ACR
> * Charger l’image dans ACR

Passez au didacticiel suivant pour en savoir plus sur l’empaquetage de conteneurs dans une application Service Fabric avec Yeoman. 

> [!div class="nextstepaction"]
> [Empaqueter et déployer des conteneurs sous forme d’application Service Fabric](service-fabric-tutorial-package-containers.md)
