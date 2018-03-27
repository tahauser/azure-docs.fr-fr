---
title: Configurer votre environnement de développement sur Mac OS X afin de travailler avec Azure Service Fabric | Microsoft Docs
description: Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Mac OS X.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: bf0a03ace2f6b6e6b1c845785a452d0b75f35de8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Vous pouvez générer des applications Azure Service Fabric pour qu’elles s’exécutent sur les clusters Linux à l’aide de Mac OS X. Ce document explique comment configurer votre Mac pour le développement.

## <a name="prerequisites"></a>Prérequis

Azure Service Fabric n’est pas exécuté en mode natif sur Mac OS X. Pour exécuter un cluster Service Fabric local, une image de conteneur Docker préconfigurée est fournie. Avant de commencer, vous avez besoin des éléments suivants :

* Au moins 4 Go de RAM.
* La dernière version de [Docker](https://www.docker.com/).

>[!TIP]
>
>Pour installer Docker sur votre Mac, suivez les étapes indiquées dans la [documentation de Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Après l’installation, [vérifiez votre installation](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Créer un conteneur local et configurer Service Fabric
Pour configurer un conteneur Docker local et y exécuter un cluster Service Fabric, procédez comme suit :

1. Extrayez l’image de conteneur pour Service Fabric onebox du référentiel de hub Docker. Par défaut, cela extraira l’image avec la dernière version de Service Fabric. Pour des révisions particulières, visitez la page [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    ```bash
    docker pull microsoft/service-fabric-onebox
    ```

2. Mettez à jour la configuration du démon Docker sur votre ordinateur hôte avec les paramètres suivants, puis redémarrez le démon Docker : 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Vous pouvez mettre à jour ces paramètres directement dans le fichier daemon.json dans votre chemin d’installation de Docker.
    
    >[!NOTE]
    >
    >L’emplacement du fichier daemon.json peut varier d’une machine à une autre. Par exemple, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >L’approche recommandée consiste à modifier directement les paramètres de configuration du démon dans Docker. Sélectionnez l’**icône de Docker**, puis sélectionnez **Préférences** > **Démon** > **Avancé**.
    >

3. Démarrez une instance de conteneur pour Service Fabric onebox et utilisez l’image que vous avez extraite dans la première étape :

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    >Donnez un nom à votre instance de conteneur de sorte qu’elle puisse être gérée plus facilement. 
    >
    >Si votre application écoute sur certains ports, ceux-ci doivent être spécifiés à l’aide de balises `-p` supplémentaires. Par exemple, si votre application écoute sur le port 8080, ajoutez la balise `-p` suivante :
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

4. Connectez-vous au conteneur Docker en mode SSH interactif :

    ```bash
    docker exec -it sfonebox bash
    ```

5. Exécutez le script de configuration pour extraire les dépendances requises et démarrer le cluster sur le conteneur :

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Une fois l’étape 5 terminée, accédez à `http://localhost:19080` à partir de votre Mac. Vous devez voir l’explorateur de Service Fabric.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurer la CLI Service Fabric (sfctl) sur votre ordinateur Mac

Suivez les instructions de [la CLI Service Fabric](service-fabric-cli.md#cli-mac) pour installer la CLI Service Fabric (`sfctl`) sur votre ordinateur Mac.
Les commandes CLI prennent en charge l’interaction avec des entités Service Fabric, y compris des clusters, des applications et des services.

1. Pour vous connecter au cluster avant de déployer des applications, exécutez la commande ci-dessous. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Créer votre application sur votre Mac à l’aide de Yeoman

Service Fabric fournit des outils de génération de modèles automatique qui vous aident à créer une application Service Fabric depuis un terminal à l’aide du générateur de modèles Yeoman. Procédez comme suit pour vous assurer que le générateur de modèles Yeoman Service Fabric est en état de fonctionnement sur votre machine :

1. Node.js et le gestionnaire de package de nœud (NPM) doivent être installés sur votre Mac. Le logiciel peut être installé à l’aide de [HomeBrew](https://brew.sh/), comme suit :

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installez le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM :

    ```bash
    npm install -g yo
    ```
3. Installez le générateur Yeoman que vous préférez en suivant les étapes indiquées dans la [documentation](service-fabric-get-started-linux.md) de prise en main. Pour créer des applications Service Fabric à l’aide de Yeoman, procédez comme suit :

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Pour générer une application Java Service Fabric sur votre Mac, JDK version 1.8 et Gradle doivent être installés sur la machine hôte. Le logiciel peut être installé à l’aide de [HomeBrew](https://brew.sh/), comme suit : 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Déployer votre application sur votre Mac à partir du terminal

Lorsque vous avez créé et généré votre application Service Fabric, vous pouvez déployer votre application à l’aide de la [CLI Service Fabric](service-fabric-cli.md#cli-mac) :

1. Connectez-vous au cluster Service Fabric qui est en cours d’exécution à l’intérieur de l’instance de conteneur sur votre Mac :

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Depuis votre répertoire de projet, exécutez le script d’installation :

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurer le développement de .NET Core 2.0

Installez le [Kit de développement logiciel (SDK) .NET Core 2.0 pour Mac](https://www.microsoft.com/net/core#macos) afin de démarrer [la création d’applications Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md). Les packages pour les applications .NET Core 2.0 Service Fabric sont hébergés sur NuGet.org, qui est actuellement en préversion.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Installer le plug-in Service Fabric pour Eclipse Neon sur votre Mac

Azure Service Fabric fournit un plug-in pour Eclipse Neon pour l’environnement de développement intégré Java. Le plug-in simplifie le processus de création, de génération et de déploiement des services Java. Pour installer ou mettre à jour le plug-in Service Fabric pour Eclipse vers la dernière version, procédez comme [suit](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon). Les autres étapes de la [documentation de Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md) sont également applicables : générer une application, ajouter un service à une application, désinstaller une application, etc.

La dernière étape consiste à instancier le conteneur avec un chemin d’accès qui est partagé avec votre hôte. Le plug-in requiert ce type d’instanciation pour utiliser le conteneur Docker sur votre Mac. Par exemple : 

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Les attributs sont définis comme suit :
* `/Users/sayantan/work/workspaces/mySFWorkspace` est le chemin d’accès qualifié complet de l’espace de travail sur votre Mac.
* `/tmp/mySFWorkspace` est le chemin d’accès à l’intérieur du conteneur auquel l’espace de travail doit être mappé.

>[!NOTE]
> 
>Si vous avez un nom/chemin d’accès différent pour votre espace de travail, mettez à jour ces valeurs dans la commande `docker run`.
> 
>Si vous démarrez le conteneur avec un nom autre que `sfonebox`, mettez à jour la valeur du nom dans le fichier testclient.sh de votre application Java pour Service Fabric.
>

## <a name="next-steps"></a>Étapes suivantes
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md)
* [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)
* [Préparer un environnement de développement Linux sur Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
