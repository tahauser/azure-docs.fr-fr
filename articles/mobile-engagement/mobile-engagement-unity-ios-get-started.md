---
title: Prise en main d’Azure Mobile Engagement pour le déploiement de l’application Unity pour iOS
description: Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Unity déployées sur des appareils iOS.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8270a56daf8e69eede5b3d14b4ff88e0911da11a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Prise en main d’Azure Mobile Engagement pour le déploiement de l’application Unity pour iOS
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Unity lors de son déploiement sur un appareil iOS.
Ce didacticiel utilise le didacticiel classique Unity Roll-a-Ball comme point de départ. Vous devez suivre la procédure décrite dans ce [didacticiel](mobile-engagement-unity-roll-a-ball.md) avant de procéder à l’intégration de Mobile Engagement que nous présentons dans le didacticiel ci-après. 

Ce didacticiel requiert les éléments suivants :

* [Unity Editor](http://unity3d.com/get-unity)
* [Kit de développement logiciel (SDK) Mobile Engagement pour Unity](https://aka.ms/azmeunitysdk)
* Éditeur XCode

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement
### <a name="import-the-unity-package"></a>Importer le package Unity
1. Téléchargez le [package Unity pour Mobile Engagement](https://aka.ms/azmeunitysdk) et enregistrez-le sur votre ordinateur local. 
2. Accédez à **Assets -> Import Package -> Custom Package** (Ressources -> Importer le package -> Package personnalisé) et sélectionnez le package que vous avez téléchargé à l’étape précédente. 
   
    ![][70] 
3. Vérifiez que tous les fichiers sont sélectionnés, puis cliquez sur le bouton **Import** . 
   
    ![][71] 
4. Une fois l’importation effectuée, les fichiers du Kit de développement logiciel (SDK) importé dans votre projet s’affichent.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Mettre à jour le fichier EngagementConfiguration
1. Ouvrez le fichier de script **EngagementConfiguration** contenu dans le dossier SDK et mettez à jour la chaîne **IOS\_CONNECTION\_STRING** en la remplaçant par la chaîne de connexion obtenue plus tôt à partir du portail Azure.  
   
    ![][73]
2. Enregistrez le fichier . 

### <a name="configure-the-app-for-basic-tracking"></a>Configurer l’application pour un suivi de base
1. Ouvrez le script **PlayerController** joint à l’objet Player pour le modifier. 
2. Ajoutez les instructions using suivantes :
   
        using Microsoft.Azure.Engagement.Unity;
3. Ajoutez ce qui suit à la méthode `Start()` :
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application
1. Connectez un appareil iOS à votre ordinateur. 
2. Accédez à **File -> Build Settings** (Fichier -> Paramètres de build). 
   
    ![][40]
3. Sélectionnez **Android** (Android), puis cliquez sur **Switch Platform** (Changer de plateforme).
   
    ![][41]
   
    ![][42]
4. Cliquez sur **Player settings** et indiquez un identificateur Bundle Identifier valide. 
   
    ![][53]
5. Pour finir, cliquez sur **Build And Run**
   
    ![][54]
6. Vous pouvez être invité à fournir un nom de dossier pour y stocker le package iOS. 
   
    ![][43]
7. Si tout se passe bien, le projet est compilé, et vous devez l’ouvrir dans votre application XCode. 
8. Vérifiez que l’identificateur **Bundle identifier** est correct dans le projet.  
   
    ![][75]
9. Exécutez l’application en XCode afin que le package soit déployé sur votre appareil connecté. Votre jeu Unity doit apparaître sur votre téléphone. 

## <a id="monitor"></a>Connexion d’application avec l’analyse en temps réel
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Activation des notifications push et de la messagerie in-app
Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs et REACH à l’aide de notifications push et de la messagerie in-app, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Inutile d’effectuer d’autres tâches de configuration dans votre application pour recevoir des notifications, car elle est déjà configurée à cet effet.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
