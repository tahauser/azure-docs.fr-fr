---
title: "Créer une application iOS sur Azure App Service Mobile Apps | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser des backends Azure Mobile App pour le développement iOS en Objective-C ou Swift"
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 970ad90aa14d5778e8b232e32aa1dacf32789988
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-an-ios-app"></a>Créer une application iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel présente l’ajout d’ [Azure Mobile Apps](app-service-mobile-value-prop.md), un service principal cloud, à une application iOS. Nous créerons tout d’abord un serveur principal mobile. Ensuite, nous utiliserons une simple application iOS de *To do list* pour stocker des données dans Azure.

Pour suivre ce didacticiel, vous avez besoin d’un Mac et d’un [compte Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>Étape I : créer un serveur principal d’applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>Étape II : configurer le projet de serveur principal
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>Étape III : télécharger et exécuter l’application iOS
[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
