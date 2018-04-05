---
title: Intégration du Kit de développement logiciel (SDK) universel Windows pour Azure Mobile Engagement | Microsoft Docs
description: Intégration de Windows Universal pour le Kit de développement logiciel (SDK) pour Azure Engagement Mobile
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: 8c513d5b05afaee4c49d0d2e96a0dcda78761421
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Intégration du Kit de développement logiciel (SDK) Windows Universal pour Azure Engagement Mobile
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Ce document décrit toutes les options d’intégration et de configuration disponibles pour le Kit de développement logiciel (SDK) Windows Universal pour Azure Engagement Mobile.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez suivre notre [didacticiel de 15 minutes](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Fonctionnalités avancées
### <a name="reporting-features"></a>Fonctionnalités de génération de rapports
Vous pouvez ajouter les fonctionnalités suivantes :

1. [Options de génération de rapports avancés](mobile-engagement-windows-store-advanced-reporting.md)
2. [Options de configuration avancées](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notifications
[Comment intégrer Reach (Notifications) dans votre application Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implémentation du plan de balise :
[Comment utiliser l'API de balisage avancée Mobile Engagement dans votre application Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Notes de publication
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Améliorations de la stabilité.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procédures de mise à niveau
Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Si vous avez manqué plusieurs versions du kit SDK, vous devrez peut-être suivre quelques procédures. Consultez l’ensemble des [procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md). Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

### <a name="from-330-to-340"></a>Migration de 3.3.0 vers 3.4.0
#### <a name="test-logs"></a>Journaux des tests
Les journaux de console produits par le Kit de développement logiciel (SDK) peuvent maintenant être activés/désactivés/filtrés. Pour personnaliser ce résultat, mettez à jour la propriété `EngagementAgent.Instance.TestLogEnabled` avec une des valeurs disponibles à partir de l'énumération `EngagementTestLogLevel`, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Ressources
La superposition Reach a été améliorée. Elle fait partie des ressources du package NuGet du Kit de développement logiciel (SDK).

Lors de la mise à niveau vers la nouvelle version du Kit de développement logiciel (SDK), vous pouvez choisir de conserver ou non les fichiers existants dans le dossier de superposition de vos ressources :

* Si la superposition précédente vous convient ou que vous intégrez manuellement les éléments `WebView` , vous pouvez choisir de conserver vos fichiers existants car ils resteront opérationnels.
* Pour effectuer la mise à jour vers la nouvelle superposition, remplacez l’ensemble du dossier `overlay` contenant vos ressources par le nouveau dossier contenant le package du Kit de développement logiciel (applications UWP : après la mise à niveau, vous trouverez le nouveau dossier de superposition sous %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> L’utilisation de la nouvelle superposition remplace toutes les personnalisations apportées à la version précédente.
> 
> 

### <a name="upgrade-from-older-versions"></a>Mise à niveau à partir de versions antérieures
Consultez la rubrique [Procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)

