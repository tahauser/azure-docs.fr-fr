---
title: "Prise en main de Mobile Apps à l’aide de Xamarin.Forms"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement Xamarin.Forms"
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 01a8e356649c070a5cf36ae070195abce699f85e
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-xamarinforms-app"></a>Créer une application Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Forms en utilisant la fonctionnalité Azure Mobile Apps d’Azure App Service en tant que back end. Vous allez créer un serveur principal d’applications mobiles et une simple application Xamarin.Forms de liste de tâches qui stocke les données d’application dans Azure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Forms.

## <a name="prerequisites"></a>Conditions préalables
Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d’informations, consultez la page [Version d’évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour plus d’informations, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

* Un Mac sur lequel sont installés Xcode v7.0 ou version ultérieure et Xamarin Studio Community. Pour plus d’informations, consultez les articles [Configurer et installer Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [Configurer, installer et vérifier pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-a-new-mobile-apps-back-end"></a>Créer un back end Mobile Apps

Pour créer un nouveau back end Mobile Apps, procédez comme suit :

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un back end Mobile Apps que vos applications client mobiles peuvent utiliser. Vous allez ensuite télécharger un projet de serveur pour un simple back end de liste de tâches et le publier dans Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

Pour configurer le projet de serveur de sorte qu’il utilise le back end Node.js ou .NET, procédez comme suit :

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Télécharger et exécuter la solution Xamarin.Forms

Vous pouvez télécharger la solution de la manière de votre choix. Téléchargez la solution sur un Mac et ouvrez-la dans Xamarin Studio, ou bien téléchargez la solution sur un ordinateur Windows et ouvrez-la dans Visual Studio à l’aide d’un ordinateur Mac connecté au réseau pour la création de l’application iOS. Pour plus d’informations, consultez la page [Configuration et installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).

Sur un ordinateur Mac ou Windows, procédez comme suit :

1. Accédez au [portail Azure].

2. Dans le panneau Paramètres de votre application mobile, cliquez sur **Quickstart** (sous Deployment) > **Xamarin.Forms**. À l’Étape 3, cliquez sur **Create a new app** si cette option n’est pas déjà sélectionnée.  Cliquez ensuite sur le bouton **Download** .

   Cette action télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

3. Extrayez le projet que vous avez téléchargé, puis ouvrez-le dans Xamarin Studio (Mac) ou Visual Studio (Windows).

   ![Projet extrait dans Xamarin Studio][9]

   ![Projet extrait dans Visual Studio][8]

## <a name="optional-run-the-ios-project"></a>(Facultatif) Exécuter le projet iOS
Dans cette section, vous exécutez le projet iOS Xamarin pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

#### <a name="in-xamarin-studio"></a>Dans Xamarin Studio
1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.

2. Dans le menu **Exécuter**, cliquez sur **Démarrer le débogage** pour créer le projet et démarrer l’application dans l’émulateur iPhone.

#### <a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.

2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.

3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** situées à côté du projet iOS.

4. Pour générer le projet et démarrer l’application dans l’émulateur iPhone, appuyez sur la touche **F5**.

   > [!NOTE]
   > Si vous avez des problèmes pour générer le projet, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. La mise à jour des dernières versions des projets de démarrage rapide peut être lente.    
   >
   >

5. Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton « + » (**+**).

    ![][10]

    Cette action envoie une demande post vers le back end Mobile Apps qui est hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend Mobile Apps et les données sont affichées dans la liste.

    > [!NOTE]
    > Vous trouverez le code qui vous permet d’accéder à votre back end Mobile Apps dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
    >
    >

## <a name="optional-run-the-android-project"></a>(Facultatif) Exécuter le projet Android
Dans cette section, vous exécutez le projet droïde Xamarin pour Android. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Android.

#### <a name="in-xamarin-studio"></a>Dans Xamarin Studio

1. Cliquez avec le bouton droit sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.

2. Pour générer le projet et démarrer l’application dans l’émulateur Android, dans le menu **Exécuter**, cliquez sur **Démarrer le débogage**.

#### <a name="in-visual-studio"></a>Dans Visual Studio

1. Cliquez avec le bouton droit sur le projet Android (Droid), puis cliquez sur **Définir comme projet de démarrage**.

2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.

3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** situées à côté du projet Android.

4. Pour générer le projet et démarrer l’application dans l’émulateur Android, appuyez sur la touche **F5**.

   > [!NOTE]
   > Si vous avez des problèmes pour générer le projet, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. La mise à jour des dernières versions des projets de démarrage rapide peut être lente.    
   >
   >

5. Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton « + » (**+**).

    ![][11]
    
    Cette action envoie une demande post vers le back end Mobile Apps qui est hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend Mobile Apps et les données sont affichées dans la liste.
    
    > [!NOTE]
    > Vous trouverez le code qui vous permet d’accéder à votre back end Mobile Apps dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
    >
    >

## <a name="optional-run-the-windows-project"></a>(Facultatif) Exécuter le projet Windows

Dans cette section, vous exécutez le projet WinApp Xamarin pour les appareils Windows. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

#### <a name="in-visual-studio"></a>Dans Visual Studio

1. Cliquez avec le bouton droit sur les projets Windows, puis cliquez sur **Définir comme projet de démarrage**.

2. Dans le menu **Générer**, cliquez sur **Gestionnaire de configuration**.

3. Dans la boîte de dialogue **Gestionnaire de configuration**, cochez les cases **Générer** et **Déployer** situées à côté du projet Windows que vous avez choisi.

4. Pour générer le projet et démarrer l’application dans un émulateur Windows, appuyez sur la touche **F5**.

   > [!NOTE]
   > Si vous avez des problèmes pour générer le projet, exécutez le gestionnaire de package NuGet et mettez à jour vers la dernière version des packages de support Xamarin. La mise à jour des dernières versions des projets de démarrage rapide peut être lente.    
   >
   >

5. Dans l’application, tapez un texte explicite, tel que *Découvrir Xamarin*, puis cliquez sur le bouton « + » (**+**).

    Cette action envoie une demande post vers le back end Mobile Apps qui est hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le backend Mobile Apps et les données sont affichées dans la liste.
    
    ![][12]
    
    > [!NOTE]
    > Vous trouverez le code qui vous permet d’accéder à votre back end Mobile Apps dans le fichier C# TodoItemManager.cs du projet de bibliothèque de classes portables de votre solution.
    >
    >

## <a name="next-steps"></a>Étapes suivantes

* [Ajout de l’authentification à votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
  Apprenez à authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajouter des notifications Push à votre application](app-service-mobile-xamarin-forms-get-started-push.md)  
  Découvrez comment ajouter la prise en charge des notifications Push à votre application et configurer le back end Mobile Apps pour utiliser Azure Notification Hubs afin d’envoyer les notifications Push.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile Apps. La synchronisation hors connexion permet d’afficher, d’ajouter ou de modifier les données d’une application mobile même en l’absence de connexion au réseau.

* [Utilisation du client géré pour Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Découvrez comment utiliser le Kit de développement logiciel (SDK) dans votre application Xamarin.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portail Azure]: https://portal.azure.com/
