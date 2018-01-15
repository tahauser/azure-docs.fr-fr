---
title: "Créer une application Cordova sur Azure App Service Mobile Apps | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser des backends d’applications mobiles Azure pour le développement Apache Cordova"
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
tags: 
keywords: cordova, javascript, mobile, client
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 223e9e35fcab347f9b5b8db01a9fd667b9f5d55d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-an-apache-cordova-app"></a>Créer une application Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel montre comment ajouter un backend cloud à une application mobile Apache Cordova à l’aide d’un backend d’application mobile Azure.  Vous créez un backend d’application mobile et une simple application Apache Cordova *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un PC équipé de [Visual Studio Community 2017] ou d’une version plus récente.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également contourner Visual Studio et utiliser directement la ligne de commande Apache Cordova.  L’utilisation de la ligne de commande est utile lorsque vous terminez le didacticiel sur un ordinateur Mac.  La compilation d’applications client Apache Cordova à l'aide de la ligne de commande n'est pas couverte par ce didacticiel.

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurer le projet de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Télécharger et exécuter l'application Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel de démarrage rapide, passez à l’un des didacticiels suivants :

* [Ajoutez des données hors connexion](app-service-mobile-cordova-get-started-offline-data.md) à votre application Apache Cordova.
* [Ajout de l’authentification](app-service-mobile-cordova-get-started-users.md) à votre application Apache Cordova.
* [Ajout de notifications Push](app-service-mobile-cordova-get-started-push.md) à votre application Apache Cordova.

En savoir plus sur les concepts clés avec Azure App Service.

* [Données hors connexion]
* [Authentification]
* [Notifications Push]

Découvrez comment utiliser les Kits de développement logiciel.

* [Kit de développement logiciel (SDK) Apache Cordova]
* [Kit de développement logiciel du serveur ASP.NET]
* [Kit de développement logiciel du serveur Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Données hors connexion]: app-service-mobile-offline-data-sync.md
[Authentification]: app-service-mobile-auth.md
[Notifications Push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Kit de développement logiciel (SDK) Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Kit de développement logiciel du serveur ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Kit de développement logiciel du serveur Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
