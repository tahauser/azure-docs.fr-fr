---
title: Dépanner votre configuration de cluster Azure Service Fabric locale | Documents Microsoft
description: Cet article aborde un ensemble de suggestions relatives à la résolution des problèmes de votre cluster de développement local
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 6879a24df434d5bf69c9ba14aa00cdc9cd67df57
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Résoudre les problèmes d'installation de votre cluster de développement local
Si vous rencontrez un problème en interagissant avec votre cluster de développement Azure Service Fabric local, examinez les suggestions suivantes de résolution.

## <a name="cluster-setup-failures"></a>Échecs de configuration du cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Impossible de nettoyer les journaux de Service Fabric
#### <a name="problem"></a>Problème
Lors de l’exécution du script DevClusterSetup, le message d’erreur suivant s’affiche :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous pouvez maintenant exécuter le script correctement.

## <a name="cluster-connection-failures"></a>Échecs de connexion au cluster

### <a name="type-initialization-exception"></a>Exception durant l’initialisation de type
#### <a name="problem"></a>Problème
Quand vous êtes connecté au cluster dans PowerShell, l’erreur TypeInitializationException apparaît pour System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Solution
Votre variable PATH n’a pas été correctement définie durant l’installation. Déconnectez-vous de Windows, puis reconnectez-vous. Le chemin d’accès est alors actualisé.

### <a name="cluster-connection-fails-with-object-is-closed"></a>La connexion au cluster est mise en échec avec une indication de fermeture de l’objet
#### <a name="problem"></a>Problème
Un appel à Connect-ServiceFabricCluster est mis en échec avec une erreur de ce type :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur.

### <a name="fabric-connection-denied-exception"></a>Exception Connexion Fabric refusée
#### <a name="problem"></a>Problème
Pendant le débogage à partir de Visual Studio, vous obtenez une erreur FabricConnectionDeniedException.

#### <a name="solution"></a>Solution
Cette erreur se produit généralement lorsque vous tentez de démarrer un processus hôte de service manuellement.

Assurez-vous de ne pas disposer de projets de service définis en tant que projets de démarrage dans votre solution. Seuls les projets d’application Service Fabric doivent être définis en tant que projets de démarrage.

> [!TIP]
> Si, après l’installation, votre cluster local commence à se comporter de manière anormale, vous pouvez le réinitialiser à l’aide de l’application de barre d’état système de gestionnaire de cluster local. Cela supprime le cluster existant et en installe un nouveau. Notez que toutes les applications déployées et les données associées sont supprimées.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Comprendre votre cluster et résoudre les problèmes à l’aide des rapports d’intégrité système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

