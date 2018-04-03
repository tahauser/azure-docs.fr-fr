---
title: Didacticiel Kubernetes sur Azure - Préparer une application
description: Didacticiel ACS - Préparer une application
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 8a2c2e53ed04cf00cc02135c5e5f82ded18fc2bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-prepare-application-for-azure-container-service-aks"></a>Didacticiel : Préparer une application pour Azure Container Service (AKS)

Dans ce didacticiel (premier d’une série de huit), vous allez préparer une application à plusieurs conteneurs à son utilisation dans Kubernetes. Les étapes effectuées sont les suivantes :  

> [!div class="checklist"]
> * Clonage de la source de l’application à partir de GitHub  
> * Création d’une image conteneur à partir de la source de l’application
> * Test de l’application dans un environnement Docker local

Une fois ces étapes effectuées, l’application suivante est accessible dans votre environnement de développement local.

![Image du cluster Kubernetes sur Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

Dans les didacticiels suivants, l’image conteneur est chargée dans Azure Container Registry, puis exécutée dans un cluster ACS.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel suppose une compréhension élémentaire des concepts Docker principaux tels que les conteneurs, les images de conteneur et les commandes Docker de base. Si besoin, consultez [Bien démarrer avec Docker][docker-get-started] pour apprendre les principes de base des conteneurs. 

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-for-mac], [Windows][docker-for-windows] ou [Linux][docker-for-linux].

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Par conséquent, nous recommandons d’utiliser un environnement de développement Docker complet.

## <a name="get-application-code"></a>Obtenir le code d’application

L’exemple d’application utilisé dans ce didacticiel est une application de votes de base. L’application est constituée d’un composant web frontal et d’une instance Redis principale. Le composant web est empaqueté dans une image conteneur personnalisée. L’instance Redis utilise une image non modifiée de Docker Hub.  

Utilisez git pour télécharger une copie de l’application dans votre environnement de développement.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Modifiez les répertoires de manière à travailler à partir des répertoires clonés.

```console
cd azure-voting-app-redis
```

Dans le répertoire se trouvent le code source de l’application, un fichier Docker Compose précréé et un fichier manifeste Kubernetes. Ces fichiers sont utilisés tout au long de ce didacticiel. 

## <a name="create-container-images"></a>Créer des images de conteneur

Vous pouvez utiliser [Docker Compose][docker-compose] pour automatiser la génération à partir des images conteneur, ainsi que le déploiement des applications à plusieurs conteneurs.

Exécutez le fichier `docker-compose.yaml` pour créer l’image conteneur, téléchargez l’image Redis, puis démarrez l’application.

```console
docker-compose up -d
```

Une fois terminé, utilisez la commande [docker images][docker-images] pour afficher les images créées.

```console
docker images
```

Notez que les trois images ont été téléchargées ou créées. L’image `azure-vote-front` contient l’application et utilise l’image `nginx-flask` comme base. L’image `redis` est utilisée pour démarrer une instance Redis.

```
REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Exécutez la commande [docker ps][docker-ps] pour voir les conteneurs en cours d’exécution.

```console
docker ps
```

Output:

```
CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Tester l’application localement

Accédez à http://localhost:8080 pour afficher le conteneur en cours d’exécution.

![Image du cluster Kubernetes sur Azure](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Maintenant que la fonctionnalité de l’application a été validée, les conteneurs en cours d’exécution peuvent être arrêtés et supprimés. Ne supprimez pas les images de conteneur. L’image `azure-vote-front` est chargée sur une instance Azure Container Registry dans le didacticiel suivant.

Exécutez le code suivant pour arrêter les conteneurs en cours d’exécution.

```console
docker-compose stop
```

Supprimez les conteneurs et les ressources arrêtés avec la commande suivante.

```console
docker-compose down
```

Une fois terminé, vous disposez de deux images conteneur contenant l’application Azure Vote.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, une application a été testée et les images de conteneur créées pour l’application. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Le clonage de la source de l’application à partir de GitHub  
> * La création d’une image conteneur à partir de la source de l’application
> * Le test de l’application dans un environnement Docker local

Passez au didacticiel suivant pour en savoir plus sur le stockage d’images de conteneur dans un registre Azure Container Registry.

> [!div class="nextstepaction"]
> [Envoyer des images à Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md