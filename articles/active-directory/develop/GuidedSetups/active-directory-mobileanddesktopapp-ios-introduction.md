---
title: "Bien démarré avec Azure AD v2 iOS - Intro | Documents Microsoft"
description: "Cet article explique comment les applications iOS (Swift) peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 5fcd78eaa0c5b09b70aa973466a34556fff56071
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Appeler l’API Microsoft Graph à partir d’une application iOS

Ce guide explique comment une application iOS native (Swift) peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

À la fin de ce guide, votre application pourra appeler une API protégée à l’aide de comptes personnels (y compris outlook.com et live.com), ainsi que de comptes professionnels et scolaires de n’importe quelle société ou organisation qui possède Azure Active Directory.

> ### <a name="pre-requisites"></a>Conditions préalables
> - XCode 8.x est nécessaire pour ce guide. Vous pouvez télécharger XCode [ici](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de téléchargement de XCode").
> - [Carthage](https://github.com/Carthage/Carthage) pour la gestion des packages

### <a name="how-this-guide-works"></a>Fonctionnement de ce guide

![Fonctionnement de ce guide](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

L’exemple d’application créé dans le cadre de ce guide permet à une application iOS d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons en provenance d’un point de terminaison Azure Active Directory v2. Pour ce scénario, un jeton est ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestion de l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton qui peut être utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par Microsoft Azure Active Directory v2.

Les API telles que Microsoft Graph requièrent un jeton d’accès pour autoriser l’accès à des ressources spécifiques, par exemple pour lire le profil d’un utilisateur, accéder au calendrier de l’utilisateur ou envoyer un courrier électronique. Votre application peut demander un jeton d’accès à l’aide de la bibliothèque MSAL en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée.

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.


### <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Préversion de la bibliothèque d’authentification Microsoft pour iOS|

