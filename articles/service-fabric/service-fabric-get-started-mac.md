---
title: "Configurer votre environnement de développement sur Mac OS X afin de travailler avec Azure Service Fabric | Microsoft Docs"
description: "Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Vous pouvez générer des applications Service Fabric pour qu’elles s’exécutent sur les clusters Linux à l’aide de Mac OS X. Ce document explique comment configurer votre Mac pour le développement.

## <a name="prerequisites"></a>Composants requis
Service Fabric n’est pas exécuté en mode natif sur OS X. Pour exécuter un cluster Service Fabric local, une image de conteneur Docker préconfigurée est fournie. Avant de commencer, vous avez besoin des éléments suivants :

* Au moins 4 Go de RAM
* Dernière version de [Docker](https://www.docker.com/)
* Accès à [l’image](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) de conteneur Docker pour Service Fabric One-box

>[!TIP]
> * Vous pouvez suivre les étapes indiquées dans la [documentation](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) officielle de Docker pour installer Docker sur votre Mac. 
> * Une fois l’installation terminée, suivez les étapes indiquées [ici](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine) pour vérifier que l’installation s’est déroulée correctement.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Créer un conteneur local et configurer Service Fabric
Pour configurer un conteneur Docker local et y exécuter un cluster Service Fabric, procédez comme suit :

1. Extrayez l’image du référentiel Docker Hub :

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Mettez à jour la configuration du démon Docker sur votre ordinateur hôte avec les informations suivantes, puis redémarrez le démon Docker : 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Vous pouvez effectuer cette mise à jour directement sur daemon.json dans votre chemin d’installation du docker (emplacement qui peut varier d’un ordinateur à l’autre, par exemple ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json). La méthode conseillée pour la mise à jour est la suivante : accédez à l’icône Docker > Préférences > Démon > Avancé et effectuez la mise à jour.

3. Démarrez une instance de conteneur Service Fabric One-Box avec l’image :

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Le fait de nommer votre instance de conteneur vous permet de la gérer plus facilement. 
    > * Si votre application écoute sur certains ports, il doit être spécifié à l’aide de balises -p supplémentaires. Par exemple, si votre application écoute sur le port 8080, exécutez docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Connectez-vous au conteneur Docker en mode SSH interactif :

    ```bash
    docker exec -it sfonebox bash
    ```

5. Exécutez le script de configuration qui va extraire les dépendances requises, puis démarrer le cluster sur le conteneur.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Une fois l’étape 5 terminée, vous pouvez accéder à ``http://localhost:19080`` à partir de votre Mac. Vous devriez alors voir l’Explorateur Service Fabric.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurer la CLI Service Fabric (sfctl) sur votre ordinateur Mac

Suivez les instructions de [la CLI Service Fabric](service-fabric-cli.md#cli-mac) pour installer la CLI Service Fabric (`sfctl`) sur votre ordinateur Mac.
Il s’agit des commandes CLI permettant d’interagir avec des entités Service Fabric, y compris des clusters, des services et des applications.

## <a name="create-application-on-your-mac-using-yeoman"></a>Créer une application sur votre ordinateur Mac à l’aide de Yeoman

Service Fabric fournit des outils de génération de modèles automatique qui vous aident à créer une application Service Fabric depuis un terminal à l’aide du générateur de modèles Yeoman. Suivez les étapes ci-dessous pour vous assurer que le générateur de modèles Yeoman Service Fabric est en état de fonctionnement sur votre machine.

1. Vous devez installer Node.js et NPM sur votre Mac. Si ce n’est pas le cas, vous pouvez installer Node.js et NPM à l’aide de Homebrew en procédant comme suit :

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installez le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM.

    ```bash
    npm install -g yo
    ```
3. Installez le générateur Yeoman que vous souhaitez utiliser, en suivant les étapes indiquées dans la [documentation](service-fabric-get-started-linux.md) de prise en main. Pour créer des Applications Service Fabric à l’aide de Yeoman, suivez les étapes ci-dessous :

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Pour générer une application Java Service Fabric sur Mac, vous devez installer - JDK 1.8 et Gradle sur l’ordinateur hôte. S’il n’y figure pas déjà, vous pouvez l’installer à l’aide de [HomeBrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Déployer l’application sur votre Mac à partir de Terminal Server

Après avoir créé et généré votre application Service Fabric, vous pouvez déployer votre application à l’aide de la [CLI Service Fabric](service-fabric-cli.md#cli-mac), en suivant les étapes ci-dessous :

1. Connectez-vous au cluster Service Fabric en cours d’exécution à l’intérieur de l’instance de conteneur sur votre Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Accédez à votre répertoire de projet et exécutez le script d’installation.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurer le développement de .NET Core 2.0

Installez le [Kit de développement logiciel (SDK) .NET Core 2.0 pour Mac](https://www.microsoft.com/net/core#macos) afin de démarrer [la création d’applications Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md). Les packages pour les applications .NET Core 2.0 Service Fabric sont hébergés sur NuGet.org, actuellement en préversion.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Installer le plug-in Service Fabric pour Eclipse Neon sur votre Mac

Service Fabric fournit un plug-in pour **Eclipse Neon pour IDE Java** qui peut simplifier le processus de création, de génération et de déploiement des services Java. Vous pouvez suivre les étapes d’installation mentionnées dans cette [documentation](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) générale relative à l’installation ou à la mise à jour du plug-in Eclipse Service Fabric à la dernière version.

Toutes les autres étapes mentionnées dans la [documentation Eclipse Service Fabric](service-fabric-get-started-eclipse.md) relatives à la génération d’une application, à l’ajout d’un service à l’application, à l’installation ou la désinstallation de l’application etc. s’appliquent également à ce scénario.

Outre les étapes ci-dessus, pour que le plug-in Eclipse Service Fabric puisse fonctionner avec le conteneur Docker sur votre Mac, il est recommandé d’instancier le conteneur avec un chemin d’accès partagé avec votre hôte, comme suit :
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
où ``/Users/sayantan/work/workspaces/mySFWorkspace`` est le chemin d’accès complet de l’espace de travail sur Mac et ``/tmp/mySFWorkspace`` est le chemin d’accès à l’intérieur du conteneur auquel il est mappé.

> [!NOTE]
>1. Si le nom ou le chemin d’accès de votre espace de travail est différent, mettez-le à jour comme il convient dans la commande ``docker run`` ci-dessus.
>2. Si vous démarrez le conteneur avec un autre nom autre que ``sfonebox``, mettez-le à jour dans le fichier ``testclient.sh`` de votre application Java pour Service Fabric.

## <a name="next-steps"></a>Étapes suivantes
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Création d’un cluster Service Fabric dans Azure à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)
* [Préparer un environnement de développement Linux sur Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship