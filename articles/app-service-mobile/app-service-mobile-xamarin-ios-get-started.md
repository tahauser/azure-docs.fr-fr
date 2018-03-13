---
title: "Prise en main d'Azure App Service Mobile Apps pour les applications Xamarin.iOS | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Mobile Apps pour le développement Xamarin.iOS."
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8b890630d352619da86c3017426e24f55ef016d9
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-xamarinios-app"></a>Création d’une application Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment ajouter un service de serveur principal basé sur le cloud à une application mobile Xamarin.iOS à l’aide d’un serveur principal d’application mobile Azure.  Vous allez créer un serveur principal d’application mobile et une simple application Xamarin.iOS *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous n'avez pas de compte, inscrivez-vous pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .
* Un Mac sur lequel sont installés Xcode v7.0 ou version ultérieure et Xamarin Studio Community. Consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [Configuration, installation et vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurer le projet de serveur
Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Téléchargez ensuite un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

Suivez les étapes suivantes pour configurer le projet de serveur de sorte qu’il utilise le serveur principal Node.js ou .NET.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Télécharger et exécuter l’application Xamarin.iOS
1. Ouvrez le [portail Azure] dans une fenêtre de navigateur.
2. Dans le panneau Paramètres de votre application mobile, cliquez sur **Get Started (sous Mobile)** > **Xamarin.iOS**. À l’Étape 3, cliquez sur **Create a new app** si cette option n’est pas déjà sélectionnée.  Cliquez ensuite sur le bouton **Download** .

      Une application cliente qui se connecte à votre serveur principal mobile est téléchargée. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.
3. Extrayez le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]
4. Appuyez sur la touche F5 pour générer le projet et démarrer l’application dans l’émulateur iPhone.
5. Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton **+**.

    ![][10]

    Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend d’application mobile et les données sont affichées dans la liste.

> [!NOTE]
> Vous pouvez vérifier le code (se trouvant dans le fichier C# QSTodoService.cs) qui accède à votre backend d’application mobile pour exécuter une requête et insérer des données.
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Ajout de la synchronisation hors connexion à votre application](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Ajout de l'authentification à votre application ](app-service-mobile-xamarin-ios-get-started-users.md)
* [Ajouter des notifications Push à votre application](app-service-mobile-xamarin-ios-get-started-push.md)
* [Comment utiliser le client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[portail Azure]: https://portal.azure.com/
