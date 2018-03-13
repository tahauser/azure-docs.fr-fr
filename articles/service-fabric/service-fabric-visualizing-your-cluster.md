---
title: "Visualisation de votre cluster à l’aide de l’outil Azure Service Fabric Explorer | Microsoft Docs"
description: "Service Fabric Explorer est une application dédiée à l’inspection et à la gestion des applications cloud et des nœuds dans un cluster Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: dc71bf8045abf72953f678dc9295346dc9204e59
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer

Service Fabric Explorer (SFX) est un outil open source dédié à l’inspection et à la gestion des clusters Azure Service Fabric. Service Fabric Explorer est une application de bureau pour Windows et Linux. La prise en charge pour macOS sera bientôt disponible.

## <a name="service-fabric-explorer-download"></a>Télécharger Service Fabric Explorer

Utilisez les liens suivants pour télécharger Service Fabric Explorer en tant qu’application de bureau :

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Dans la version bureau de Service Fabric Explorer, certaines fonctionnalités peuvent ne pas être prises en charge par le cluster. Vous pouvez revenir à la version de Service Fabric Explorer déployée sur le cluster pour garantir une compatibilité totale de ces fonctionnalités.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Exécution de Service Fabric Explorer depuis le cluster

Service Fabric Explorer est également hébergé dans le point de terminaison de gestion HTTP d’un cluster Service Fabric. Pour lancer SFX dans un navigateur web, accédez au point de terminaison de gestion HTTP du cluster depuis n’importe quel navigateur, en saisissant par exemple l’adresse https://clusterFQDN:19080.

Pour configurer une station de travail de développeur, vous pouvez lancer Service Fabric Explorer sur votre cluster local en accédant à l’adresse https://localhost:19080/Explorer. Consultez cet article pour [préparer votre environnement de développement](service-fabric-get-started.md).

## <a name="connect-to-a-service-fabric-cluster"></a>Se connecter à un cluster Service Fabric
Pour vous connecter à un cluster Service Fabric, vous avez besoin du point de terminaison de gestion des clusters (nom de domaine complet/adresse IP) et le port du point de terminaison de gestion HTTP (19080 par défaut). Par exemple https://mysfcluster.westus.cloudapp.azure.com:19080. Cochez la case « Se connecter à localhost » pour vous connecter à un cluster local sur votre station de travail.

### <a name="connect-to-a-secure-cluster"></a>Se connecter à un cluster sécurisé
Vous pouvez contrôler l’accès client à votre cluster Service Fabric avec des certificats ou à l’aide d’Azure Active Directory (AAD).

Si vous essayez de vous connecter à un cluster sécurisé, selon la configuration du cluster, vous devrez présenter un certificat client ou vous connecter à l’aide d’AAD.

## <a name="video-tutorial"></a>Didacticiel vidéo

Pour découvrir comment utiliser Service Fabric Explorer, regardez la vidéo suivante de la Microsoft Virtual Academy :

> [!NOTE]
> Cette vidéo montre une instance Service Fabric Explorer hébergée dans un cluster Service Fabric, et non la version de bureau.
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Comprendre la disposition de Service Fabric Explorer
Vous pouvez naviguer dans Service Fabric Explorer à l’aide de l’arborescence située à gauche. À la racine de l'arborescence, le tableau de bord de cluster fournit une vue d'ensemble de votre cluster, y compris un résumé de l’intégrité de l'application et du nœud.

![Tableau de bord de cluster de Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Afficher la disposition du cluster
Les nœuds dans un cluster Service Fabric sont répartis dans une grille à deux dimensions composée de domaines d’erreur et de domaines de mise à niveau. Cette répartition garantit la disponibilité de vos applications en cas de défaillances matérielles et de mises à niveau de l’application. Vous pouvez examiner la disposition du cluster actif à l’aide de la carte du cluster.

![Mappage de cluster de Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Afficher les applications et les services
Le cluster contient deux sous-arborescences : une pour les applications et l’autre pour les nœuds.

Vous pouvez utiliser la vue des applications pour naviguer dans la hiérarchie logique de Service Fabric : applications, services, partitions et réplicas.

Dans l’exemple ci-dessous, l’application **MyApp** est constituée de deux services : **MyStatefulService** et **WebService**. Étant donné que **MyStatefulService** est une application avec état, elle inclut une partition avec un réplica principal et deux réplicas secondaires. En revanche, WebSvcService est une application sans état qui ne contient qu’une seule instance.

![Affichage des applications de Service Fabric Explorer][sfx-application-tree]

À chaque niveau de l'arborescence, le volet principal affiche des informations pertinentes sur l'élément. Par exemple, vous pouvez voir l’état d’intégrité et la version d’un service particulier.

![Volet des éléments essentiels de Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Afficher les nœuds du cluster
L’affichage des nœuds montre la disposition physique du cluster. Vous pouvez identifier les applications ayant déployé du code sur un nœud donné. Plus particulièrement, vous pouvez voir les réplicas en cours d’exécution sur ce nœud.

## <a name="actions"></a>Actions
Service Fabric Explorer offre un moyen rapide d’appeler des actions sur les nœuds, les applications et les services au sein de votre cluster.

Par exemple, pour supprimer une instance d’application, choisissez l’application dans l’arborescence à gauche, puis sélectionnez **Actions** > **Supprimer l’application**.

![Suppression d’une application dans Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Vous pouvez effectuer les mêmes actions en cliquant sur le bouton de sélection en regard de chaque élément.
>
> Toutes les actions accessibles par le biais de Service Fabric Explorer le sont également par l’intermédiaire de PowerShell ou d’une API REST, ce qui vous permet d’activer Automation.
>
>

Vous pouvez également utiliser Service Fabric Explorer pour créer des instances d’application pour une version et un type donnés. Choisissez le type d’application dans l’arborescence, puis cliquez sur le lien **Créer une instance d’application** en regard de la version que vous souhaitez dans le volet droit.

![Création d’une instance application dans Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer ne prend pas en charge les paramètres lors de la création d’instances application. Les instances application utilisent les valeurs de paramètres par défaut.
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Déploiement d’application Service Fabrix à l’aide de PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png