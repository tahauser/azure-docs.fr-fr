---
title: Authentification et autorisation dans Azure Mobile Apps | Microsoft Docs
description: "Référence et présentation conceptuelles de la fonctionnalité d’authentification/autorisation pour Azure Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: cfowler
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 90c11b09351f019c45f5f1b025d67947b69b3b0a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Authentification et autorisation dans Azure Mobile Apps
## <a name="what-is-app-service-authentication--authorization"></a>Qu’est-ce que l’authentification/autorisation App Service ?
> [!NOTE]
> Cette rubrique sera migrée vers une rubrique d’ [authentification/autorisation App Service](../app-service/app-service-authentication-overview.md) consolidée, qui couvre les applications web, mobile et les API Apps.
> 
> 

L’authentification/autorisation App Service est une fonctionnalité qui permet à votre application de connecter des utilisateurs sans aucune modification de code sur le serveur principal. Elle propose un moyen simple de protéger votre application et fonctionne avec des données par utilisateur.

App Service utilise l’identité fédérée, dans laquelle un **fournisseur d’identité** tiers (« IDP ») stocke des comptes et authentifie des utilisateurs. L’application utilise cette identité au lieu de la sienne. App Service prend en charge cinq fournisseurs d’identité prêts à l’emploi : *Azure Active Directory*, *Facebook*, *Google*, *Compte Microsoft* et *Twitter*. Vous pouvez également étendre cette prise en charge à vos applications en intégrant un autre fournisseur d’identité ou votre propre solution d’identité personnalisée.

Votre application peut exploiter plusieurs de ces fournisseurs d’identité, vous pouvez donc proposer à vos utilisateurs finaux plusieurs options de connexion.

Si vous voulez commencer tout de suite, consultez l’un des didacticiels suivants :

* [Ajout de l'authentification à votre application iOS]
* [Ajout de l'authentification à votre application Xamarin.iOS]
* [Ajout de l'authentification à votre application Xamarin.Android]
* [Ajout de l’authentification à votre application Windows]

## <a name="how-authentication-works"></a>Fonctionnement de l’authentification
Pour s’authentifier à l’aide d’un des fournisseurs d’identité, vous devez d’abord configurer le fournisseur d’identité pour qu’il reconnaisse votre application. Le fournisseur d’identité vous indique alors des ID et des clés secrètes que vous devez fournir à votre tour à l’application. Ceux-ci établissent la relation d’approbation et permettent à App Service de valider les identités qui lui sont fournies.

Ces étapes sont détaillées dans les rubriques suivantes :

* [Comment configurer votre application pour utiliser une connexion Azure Active Directory]
* [Comment configurer votre application pour utiliser une connexion Facebook]
* [Comment configurer votre application pour utiliser une connexion Google]
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application pour utiliser une connexion Twitter]

Une fois que tout est configuré sur le serveur principal, vous pouvez modifier votre client pour la connexion. Il existe deux approches :

* À l’aide d’une seule ligne de code, laissez le Kit de développement logiciel (SDK) client Mobile Apps connecter des utilisateurs.
* Exploitez un Kit de développement logiciel (SDK) publié par un fournisseur d’identité donnée pour établir l’identité, puis accédez à App Service.

> [!TIP]
> La plupart des applications doivent utiliser un Kit de développement logiciel (SDK) de fournisseur pour obtenir une expérience de connexion plus native et tirer parti de la prise en charge de l’actualisation et d’autres avantages propres au fournisseur.
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>Fonctionnement de l’authentification sans Kit de développement logiciel (SDK) de fournisseur
Si vous ne voulez pas configurer un SDK de fournisseur, vous pouvez autoriser Mobile Apps à effectuer la connexion pour vous. Le SDK client Mobile Apps ouvre un affichage web du fournisseur de votre choix et procède à la connexion. Dans les blogs et sur les forums, cette opération est de temps en temps appelée « flux serveur » ou « flux dirigé vers le serveur », car le serveur gère la connexion et le Kit de développement logiciel (SDK) client ne reçoit jamais le jeton du fournisseur.

Le code nécessaire pour démarrer ce flux est couvert dans le didacticiel sur l’authentification pour chaque plateforme. À la fin du flux, le SDK client dispose d’un jeton App Service et le jeton est automatiquement joint à toutes les requêtes adressées au serveur principal.

### <a name="how-authentication-with-a-provider-sdk-works"></a>Fonctionnement de l’authentification avec un Kit de développement logiciel (SDK) de fournisseur
L’utilisation d’un SDK de fournisseur permet à l’expérience de connexion d’interagir plus étroitement avec le système d’exploitation sur lequel l’application s’exécute. Elle permet également d’obtenir un jeton de fournisseur et certaines informations utilisateur sur le client, ce qui facilite beaucoup la consommation d’API graphiques et la personnalisation de l’expérience utilisateur. Dans les blogs et sur les forums, cette opération est de temps en temps appelée « flux client » ou « flux dirigé vers le client » car le code sur le client gère la connexion et a accès à un jeton de fournisseur.

Une fois qu’un jeton de fournisseur est obtenu, il doit être envoyé à App Service à des fins de validation. À la fin du flux, le SDK client dispose d’un jeton App Service et le jeton est automatiquement joint à toutes les requêtes adressées au serveur principal. Le développeur peut également conserver une référence au jeton de fournisseur s’il le souhaite.

## <a name="how-authorization-works"></a>Fonctionnement de l’autorisation
L’authentification/autorisation App Service expose plusieurs possibilités d’ **action à réaliser quand la requête n’est pas authentifiée**. Avant que votre code ne reçoive une demande donnée, App Service peut vérifier si la requête est authentifiée et si elle ne l’est pas, la rejeter et tenter d’inviter l’utilisateur à se connecter avant d’effectuer une nouvelle tentative.

Une option consiste à rediriger les requêtes non authentifiées vers l’un des fournisseurs d’identité. Dans un navigateur web, l’utilisateur serait redirigé vers une nouvelle page. En revanche, votre client mobile ne peut pas être redirigé de cette façon et les réponses non authentifiées reçoivent une erreur HTTP *401 non autorisé*. C’est pour cela que la première requête de votre client doit toujours être adressée au point de terminaison de connexion. Vous pouvez alors effectuer des appels à d’autres API. Si vous essayez d’appeler une autre API avant de vous connecter, votre client reçoit une erreur.

Si vous voulez un contrôle plus granulaire des points de terminaison qui exigent une authentification, vous pouvez également choisir « Aucune action (autoriser la requête) » pour les requêtes non authentifiées. Dans ce cas, toutes les décisions d’authentification sont reportées dans le code de votre application. Cela vous permet également d’autoriser l’accès à des utilisateurs spécifiques selon des règles d’autorisation personnalisées.

## <a name="documentation"></a>Documentation
Les didacticiels suivants montrent comment ajouter l’authentification à vos clients mobiles à l’aide d’App Service :

* [Ajout de l'authentification à votre application iOS]
* [Ajout de l'authentification à votre application Xamarin.iOS]
* [Ajout de l'authentification à votre application Xamarin.Android]
* [Ajout de l’authentification à votre application Windows]

Les didacticiels suivants montrent comment configurer App Service pour exploiter différents fournisseurs d’authentification :

* [Comment configurer votre application pour utiliser une connexion Azure Active Directory]
* [Comment configurer votre application pour utiliser une connexion Facebook]
* [Comment configurer votre application pour utiliser une connexion Google]
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application pour utiliser une connexion Twitter]

Si vous voulez utiliser un système d’identité différent de ceux fournis ici, vous pouvez également exploiter la [prise en charge de l’authentification personnalisée en version préliminaire dans le Kit de développement logiciel (SDK) serveur .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth).

[Ajout de l'authentification à votre application iOS]: app-service-mobile-ios-get-started-users.md
[Ajout de l'authentification à votre application Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started-users.md
[Ajout de l'authentification à votre application Xamarin.Android]: app-service-mobile-xamarin-android-get-started-users.md
[Ajout de l’authentification à votre application Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Comment configurer votre application pour utiliser une connexion Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment configurer votre application pour utiliser une connexion Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Comment configurer votre application pour utiliser une connexion Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Comment configurer votre application pour utiliser une connexion par compte Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment configurer votre application pour utiliser une connexion Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
