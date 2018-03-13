---
title: "Créer votre première application de microservices Azure sur Linux à l’aide de C# | Microsoft Docs"
description: "Créer et déployer une application Service Fabric à l’aide de C#"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 9a97a560034b288823d662d83d6366383c9e1706
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-azure-service-fabric-application"></a>Créer votre première application Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux (préversion)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux (préversion)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric fournit des Kits de développement logiciel (SDK) pour générer des services Linux dans .NET Core et Java. Dans ce didacticiel, nous apprenons à créer une application pour Linux et à générer un service à l’aide de C# sur .NET Core 2.0.

## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement Linux à boîtier unique sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

Il vous sera également utile d’installer l’[interface de ligne de commande Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Installer et configurer des générateurs pour C#
Service Fabric fournit des outils de génération de modèles automatique qui vous aideront à créer des applications Service Fabric à partir d’un terminal, à l’aide des générateurs de modèle Yeoman. Suivez ces étapes pour configurer les générateurs de modèle Service Fabric Yeoman pour C# :

1. Installer nodejs et NPM sur votre machine

   Ubuntu
   ```bash
   sudo apt-get install npm
   sudo apt install nodejs-legacy
   ```

   Red Hat Enterprise Linux 7.4 (prise en charge de la préversion de Service Fabric)
   ```bash
   sudo yum install nodejs
   sudo yum install npm
   ```

2. Installer le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installer le générateur d’applications Service Fabric Yeo Java à partir de NPM

  ```bash
  sudo npm install -g generator-azuresfcsharp
  ```

## <a name="create-the-application"></a>Création de l’application
Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le générateur Service Fabric [Yeoman](http://yeoman.io/) pour C#, que vous avez installé lors de la dernière étape, facilite la création de votre premier service et vous aide à ajouter de nouveaux services ultérieurement. Nous utilisons Yeoman pour créer une application avec un seul service.

1. Dans un terminal, tapez la commande suivante pour commencer la génération de modèles automatique : `yo azuresfcsharp`
2. Donnez un nom à votre application.
3. Choisissez le type de votre premier service et nommez-le. Pour les besoins de ce didacticiel, nous choisissons un service Reliable Actors.

   ![Générateur Yeoman Service Fabric pour C#][sf-yeoman]

> [!NOTE]
> Pour plus d’informations sur les options, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Création de l'application
Les modèles Yeoman Service Fabric incluent un script de build que vous pouvez utiliser pour générer l’application à partir du terminal (après avoir accédé au dossier l’application).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Déployer l’application

Une fois que l’application est générée, vous pouvez la déployer sur le cluster local.

1. Connectez-vous au cluster Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Exécutez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

L’application générée se déploie de la même manière qu’une autre application Service Fabric. Consultez la documentation sur [la gestion d’une application Service Fabric avec l’interface de ligne de commande Service Fabric](service-fabric-application-lifecycle-sfctl.md) pour obtenir des instructions détaillées.

Vous pourrez retrouver les paramètres de ces commandes dans les manifestes générés au sein du package d’application.

Une fois l’application déployée, ouvrez un navigateur et accédez à [Service Fabric Explorer ](service-fabric-visualizing-your-cluster.md), à l’adresse [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Ensuite, développez le nœud **Applications** et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

## <a name="start-the-test-client-and-perform-a-failover"></a>Démarrer le client de test et effectuer un basculement
Les projets d’acteur n’effectuent aucune opération automatiquement. Ils ont besoin d’un autre service ou client pour leur envoyer des messages. Le modèle d’acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service d’acteur.

1. Exécutez le script à l’aide de l’utilitaire watch pour afficher la sortie du service d’acteur.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Dans Service Fabric Explorer, recherchez le nœud qui héberge le réplica principal pour le service d’acteur. Dans la capture d’écran ci-dessous, il s’agit du nœud 3.

    ![Recherche du réplica principal dans Service Fabric Explorer][sfx-primary]
3. Cliquez sur le nœud trouvé à l’étape précédente, puis sélectionnez **Désactiver (redémarrer)** à partir du menu Actions. Cette action redémarre un nœud de votre cluster local et force un basculement sur un réplica secondaire s’exécutant sur un autre nœud. Dans le même temps, prêtez attention à la sortie du client de test et notez que le compteur continue à être incrémenté malgré le basculement.

## <a name="adding-more-services-to-an-existing-application"></a>Ajout d’autres services à une application existante

Pour ajouter un autre service à une application déjà créée à l’aide de `yo`, procédez comme suit :
1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfcsharp:AddService`.

## <a name="next-steps"></a>étapes suivantes

* [Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)
* [Prise en main de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
