---
title: Déployer la solution de surveillance à distance localement - Azure | Microsoft Docs
description: Ce didacticiel vous montre comment déployer la solution préconfigurée de surveillance à distance sur votre ordinateur local pour le test et le développement.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Déployer la solution préconfigurée de surveillance à distance localement

Cet article vous montre comment déployer la solution préconfigurée de surveillance à distance sur votre ordinateur local pour le test et le développement. Cette approche déploie les microservices vers un conteneur Docker local et utilise les services de stockage Azure, IoT Hub, et Cosmos DB dans le cloud. Vous utilisez les solutions préconfigurées CLI pour déployer les services cloud Azure.

## <a name="prerequisites"></a>Prérequis


Pour déployer les services Azure utilisés par la solution préconfigurée de surveillance à distance, vous avez besoin d’un abonnement Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

Pour terminer le déploiement local, vous devez avoir installé les outils suivants sur votre machine de développement local :

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) -ce logiciel est requis pour les solutions préconfigurées CLI.
* Solutions préconfigurées CLI

> [!NOTE]
> Ces outils sont disponibles sur de nombreuses plateformes, notamment Windows, Linux et iOS.

### <a name="install-the-pcs-cli"></a>Installez les solutions préconfigurées CLI

Pour installer l’interface CLI, exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
npm install iot-solutions -g
```

Pour plus d’informations sur l’interface CLI, consultez [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) (Comment utiliser l’interface CLI).

## <a name="deploy-the-azure-services"></a>Déployer des services Azure

Bien que cet article explique comment exécuter les microservices localement, ils dépendent de l’exécution de trois services Azure dans le cloud. Vous pouvez déployer ces services Azure manuellement via le portail Azure, ou utiliser les solutions préconfigurées CLI. Cet article explique comment utiliser l’outil `pcs`.

### <a name="sign-in-to-the-cli"></a>Se connecter à l’interface CLI

Avant de déployer la solution préconfigurée, vous devez vous connecter à votre abonnement Azure à l’aide de l’interface CLI comme suit :

```cmd/sh
pcs login
```

Suivez les instructions à l’écran pour effectuer le processus de connexion.

### <a name="run-a-local-deployment"></a>Exécuter un déploiement local

Utilisez la commande suivante pour démarrer le déploiement local :

```cmd/pcs
pcs -s local
```

Le script vous invite à entrer les informations suivantes :

* Le nom de la solution.
* Sélectionnez l’abonnement Azure à utiliser.
* L’emplacement du centre de données Azure à utiliser.

Le script crée une instance IoT Hub, une instance Cosmos DB et un compte de stockage Azure dans un groupe de ressources dans votre abonnement Azure. Le nom du groupe de ressources est le nom de la solution que vous avez choisi lorsque vous avez exécuté l’outil `pcs`.

L’exécution du script prend plusieurs minutes. Lorsqu’elle est terminée, vous voyez un message `Copy the following environment variables to /scripts/local/.env file:`. Copiez les définitions de variable d’environnement en suivant le message, vous les utiliserez dans une étape ultérieure.

## <a name="download-the-source-code"></a>Télécharger le code source

Le référentiel de code source de contrôle à distance inclut les fichiers de configuration de Docker dont vous avez besoin pour télécharger, configurer et exécuter les images Docker qui contiennent les microservices. Pour cloner le référentiel, accédez à un dossier approprié sur votre machine locale et exécutez une des commandes suivantes :

Pour installer les implémentations Java des microservices, exécutez :

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Pour installer les implémentations .Net des microservices, exécutez :

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Ces commandes téléchargent le code source pour tous les microservices. Bien que vous n’ayez pas besoin du code source pour exécuter les microservices dans Docker, le code source est utile si vous envisagez de modifier ultérieurement la solution préconfigurée et de tester vos modifications localement.

## <a name="run-the-microservices-in-docker"></a>Exécuter les microservices dans Docker

Pour exécuter les microservices dans Docker, modifiez d’abord le fichier **scripts\\local\\.env** dans votre copie locale de l’espace du référentiel. Remplacez tout le contenu du fichier avec les définitions de variable environnement que vous avez notées lorsque vous avez exécuté la commande `pcs` précédemment. Ces variables d’environnement permettent aux microservices dans le conteneur Docker de se connecter aux services Azure créés par l’outil `pcs`.

Pour exécuter la solution préconfigurée, accédez au dossier **scripts\local** dans votre environnement de ligne de commande et exécutez la commande suivante :

```cmd\sh
docker-compose up
```

La première fois que vous exécutez cette commande, Docker télécharge les images des microservices à partir de Docker Hub pour créer les conteneurs localement. Lors des exécutions suivantes, Docker exécute les conteneurs immédiatement.

Vous pouvez utiliser un interpréteur de commandes distinct pour afficher les journaux du conteneur. Tout d’abord, trouvez l’ID du conteneur à l’aide de la commande `docker ps -a`. Utilisez ensuite `docker logs {container-id} --tail 1000` pour afficher les 1 000 dernières entrées de journal pour le conteneur spécifié.

Pour accéder au tableau de bord de la solution de surveillance à distance, accédez à [http://localhost : 8080](http://localhost:8080) dans votre navigateur.

## <a name="tidy-up"></a>Nettoyer

Pour éviter les coûts inutiles, lorsque vous avez terminé votre test, supprimez les services cloud de votre abonnement Azure. Pour supprimer les services, le plus simple consiste à utiliser le portail Azure pour supprimer le groupe de ressources créé par l’outil `pcs`.

Utilisez la commande `docker-compose down --rmi all` pour supprimer les images Docker et libérer de l’espace sur votre machine locale. Vous pouvez également supprimer la copie locale du référentiel de contrôle à distance créé lorsque vous avez cloné le code source à partir de GitHub.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configuration de la solution préconfigurée
> * Déploiement de la solution préconfigurée
> * Connexion à la solution préconfigurée

La solution de surveillance à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->