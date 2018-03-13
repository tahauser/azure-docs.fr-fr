---
title: "Créer une application de conteneur Microsoft Azure Service Fabric sur Linux | Microsoft Docs"
description: "Dans ce guide de démarrage rapide, créez votre première application de conteneur Linux sur Microsoft Azure Service Fabric.  Concevez une image Docker avec votre application, envoyez l’image vers un registre de conteneurs, créez et déployez une application de conteneur Service Fabric."
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: f568bdf6ce40ff2d437f3566b66f6dd1478a74fa
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>Démarrage rapide : déployer une application conteneur Azure Service Fabric Linux sur Azure
Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et conteneurs extensibles et fiables. 

Ce démarrage rapide montre comment déployer des conteneurs Linux sur un cluster Service Fabric. Une fois le déploiement terminé, vous disposez d’une application de vote composée d’un serveur web frontal Python et d’un serveur principal Redis en cours d’exécution dans un cluster Service Fabric. 

![quickstartpic][quickstartpic]

Dans ce guide de démarrage rapide, vous apprenez à :
> [!div class="checklist"]
> * Déployer des conteneurs sur un cluster Azure Service Fabric Linux
> * Échelle et conteneurs de basculement dans Service Fabric

## <a name="prerequisite"></a>Configuration requise
1. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

2. Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande (CLI) en local, assurez-vous d’exécuter la version 2.0.4 de l’interface de ligne de commande Azure ou une version ultérieure. Pour rechercher la version, exécutez az--version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="get-application-package"></a>Obtenir le package d’application
Pour déployer des conteneurs sur Service Fabric, vous avez besoin d’un ensemble de fichiers manifestes (la définition d’application), qui décrivent les conteneurs individuels et l’application.

Dans le Cloud Shell, utilisez git pour cloner une copie de la définition d’application.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```
## <a name="deploy-the-application-to-azure"></a>Déploiement de l'application dans Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurer un cluster Azure Service Fabric
Pour déployer l’application sur un cluster dans Azure, créez votre propre cluster.

Les clusters tiers sont des clusters Service Fabric gratuits, limités dans le temps et hébergés sur Azure. Ils sont gérés par l’équipe Service Fabric, au sein de laquelle chacun peut déployer des applications et en savoir plus sur la plateforme. Pour obtenir l’accès à un cluster tiers, [suivez ces instructions](http://aka.ms/tryservicefabric). 

Pour effectuer des opérations de gestion sur le cluster tiers sécurisé, vous pouvez utiliser Service Fabric Explorer, CLI ou Powershell. Pour utiliser Service Fabric Explorer, vous devez télécharger le fichier PFX sur le site du cluster tiers et importer le certificat dans votre magasin de certificats (Windows ou Mac) ou dans le navigateur lui-même (Ubuntu). Il n’existe aucun mot de passe pour les certificats auto-signés venant cluster tiers. 

Pour effectuer des opérations de gestion avec Powershell ou CLI, vous avez besoin du fichier PFX (Powershell) ou PEM (CLI). Pour convertir le fichier PFX en un fichier PEM, exécutez la commande suivante :  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

Pour plus d’informations sur la création de votre propre cluster, consultez [Créer un cluster Service Fabric dans Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> Le service frontal web est configuré pour écouter le trafic entrant sur le port 80. Assurez-vous que ce port est ouvert dans votre cluster. Si vous utilisez le cluster tiers, ce port est ouvert.
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>Installer l’interface de ligne de commande Service Fabric et la connecter à votre cluster

Connectez-vous au cluster Service Fabric dans Azure à l’aide d’Azure CLI. Le point de terminaison est le point de terminaison de gestion de votre cluster. Par exemple, `https://linh1x87d1d.westus.cloudapp.azure.com:19080`.

```bash
sfctl cluster select --endpoint https://linh1x87d1d.westus.cloudapp.azure.com:19080 --pem party-cluster-1277863181-client-cert.pem --no-verify
```

### <a name="deploy-the-service-fabric-application"></a>Déployer l’application Service Fabric 
Les applications de conteneur Service Fabric peuvent être déployées à l’aide du package d’application Service Fabric décrit ou de Docker Compose. 

#### <a name="deploy-using-service-fabric-application-package"></a>Déployer à l’aide du package d’application Service Fabric
Utilisez le script d’installation fourni pour copier la définition d’application de vote dans le cluster, inscrivez le type d’application et créez une instance de l’application.

```bash
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>Déployer l’application à l’aide de Docker Compose
Déployez et installez l’application sur le cluster Service Fabric à l’aide de Docker Compose avec la commande suivante.
```bash
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://\<my-azure-service-fabric-cluster-url>:19080/Explorer. Par exemple, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`. Développez le nœud Applications pour vous assurer qu’une entrée s’affiche pour le type d’application de vote et l’instance que vous avez créée.

![Service Fabric Explorer][sfx]

Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web pointant vers l’URL de votre cluster. Par exemple, `http://linh1x87d1d.westus.cloudapp.azure.com:80`. L’application de vote doit s’afficher dans le navigateur.

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>Basculer un conteneur dans un cluster
Service Fabric s’assure que vos instances de conteneur se déplacent automatiquement vers d’autres nœuds du cluster, en cas d’échec. Il vous est également possible de drainer manuellement un nœud pour les conteneurs, puis de le déplacer normalement vers d’autres nœuds du cluster. Il existe plusieurs façons de mettre à l’échelle vos services. Dans cet exemple, nous utilisons Service Fabric Explorer.

Pour basculer le conteneur frontal, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`.
2. Cliquez sur le nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID). Notez le nom du nœud dans l’arborescence, qui affiche les nœuds en cours d’exécution dans le conteneur. Par exemple, `_nodetype_4`.
3. Développez le nœud **Nœuds** dans l’arborescence. Cliquez sur les points de suspension (trois points) à côté du nœud qui exécute le conteneur.
4. Choisissez **Redémarrer** pour redémarrer ce nœud et confirmer l’action de redémarrage. Le redémarrage entraîne le basculement du conteneur vers un autre nœud du cluster.

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Mettre à l’échelle les applications et services dans un cluster
Les services Service Fabric peuvent être facilement mis à l’échelle dans un cluster pour s’adapter à la charge sur les services. Pour mettre à l’échelle un service, vous modifiez le nombre d’instances s’exécutant dans le cluster.

Pour mettre à l’échelle le service frontal web, procédez comme suit :

1. Ouvrez Service Fabric Explorer dans votre cluster. Par exemple, `http://linh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Cliquez sur les points de suspension (trois points) à côté du nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis choisissez **Mettre le service à l’échelle**.

    ![containersquickstartscale][containersquickstartscale]

  Vous pouvez maintenant choisir de mettre à l’échelle le nombre d’instances du service frontal web.

3. Définissez le nombre sur **2**, puis cliquez sur **Scale Service** (Mettre à l’échelle le service).
4. Cliquez sur le nœud **fabric:/Voting/azurevotefront** dans l’arborescence, puis développez le nœud de la partition (représenté par un GUID).

    ![containersquickstartscaledone][containersquickstartscaledone]

    Comme vous pouvez le constater, le service possède désormais deux instances. Les nœuds exécutés par les instances sont visibles dans l’arborescence.

Par cette tâche de gestion simple, nous avons doublé les ressources disponibles pour permettre à notre service frontal de traiter la charge utilisateur. Il est important de comprendre que vous n’avez pas besoin de plusieurs instances d’un service pour que celui-ci s’exécute de manière fiable. En cas de défaillance d’un service, Service Fabric veille à ce qu'une nouvelle instance du service s’exécute dans le cluster.

## <a name="clean-up"></a>Nettoyer
Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance de l’application du cluster et annuler l’inscription du type d’application. Cette commande prend un certain temps pour nettoyer l’instance, et la commande « install'sh » ne doit pas être exécutée immédiatement après ce script. 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris comment :
> [!div class="checklist"]
> * Déployer une application de conteneur Linux vers Azure
> * Faire basculer un conteneur dans un cluster Service Fabric
> * Mettre à l’échelle un conteneur dans un cluster Service Fabric

* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-containers-overview.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
* Consultez les [exemples de code de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) sur GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
