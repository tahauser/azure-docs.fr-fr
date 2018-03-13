---
title: "Gérer l’authentification unique pour le proxy d’application Azure AD | Microsoft Docs"
description: "Comprendre les bases de l’authentification unique avec le proxy d’application"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 60422b4c8256df024bbc870fc43ec6b491f674f1
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Comment le proxy d’application Azure AD fournit-il l’authentification unique ?

L’authentification unique est un élément clé du proxy d’application Azure AD.  Elle optimise l’expérience des utilisateurs, car ces derniers ne doivent se connecter à Azure Active Directory que dans le cloud. Après leur authentification dans Azure Active Directory, le connecteur du proxy d’application gère l’authentification à l’application locale. L’application principale ne fait pas la différence entre un utilisateur distant se connectant via le proxy d’application et une utilisation normale sur un appareil joint au domaine. 

Pour utiliser Azure Active Directory pour l’authentification unique à vos applications, vous devez sélectionner **Azure Active Directory** comme méthode de pré-authentification. Si vous sélectionnez **Passthrough**, vos utilisateurs ne s’authentifient pas du tout à Azure Active Directory, mais sont dirigés directement vers l’application. Vous pouvez configurer ce paramètre lors de la publication initiale d’une application, ou vous pouvez accéder à votre application dans le portail Azure et modifier les paramètres du proxy d’application. 

Pour afficher vos options d’authentification unique, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.
3. Sélectionnez l’application dont vous souhaitez gérer les options d’authentification unique.
4. Sélectionnez **Authentification unique**.

   ![Menu déroulant de l’authentification unique](./media/application-proxy-sso-overview/single-sign-on-mode.png)

Le menu déroulant contient cinq options d’authentification unique à votre application :

* Authentification unique Azure AD désactivée
* Authentification par mot de passe
* Authentification liée
* Authentification Windows intégrée
* Authentification basée sur l’en-tête

## <a name="azure-ad-single-sign-on-disabled"></a>Authentification unique Azure AD désactivée

Si vous ne souhaitez pas utiliser l’intégration d’Azure Active Directory pour l’authentification unique à votre application, choisissez **Authentification unique Azure AD désactivée**. Lorsque cette option est sélectionnée, vos utilisateurs pourraient devoir s’authentifier deux fois. Tout d’abord, ils s’authentifient à Azure Active Directory, puis ils se connectent à l’application elle-même. 

Cette option constitue un bon choix si les utilisateurs n’ont pas besoin de s’authentifier à votre application locale, mais que vous souhaitez ajouter Azure Active Directory comme couche de sécurité en cas d’accès à distance. 

## <a name="password-based-sign-on"></a>Authentification par mot de passe

Si vous souhaitez utiliser Azure Active Directory comme coffre de mots de passe pour vos applications locales, choisissez **Authentification par mot de passe**. Cette option constitue un bon choix si votre application s’authentifie à l’aide d’une combinaison nom d’utilisateur/mot de passe au lieu d’en-têtes ou de jetons d’accès. Avec l’authentification par mot de passe, vos utilisateurs doivent se connecter à l’application la première fois qu’ils y accèdent. Après cela, Azure Active Directory fournit le nom d’utilisateur et le mot de passe pour le compte de l’utilisateur. 

Pour plus d’informations sur la configuration de l’authentification par mot de passe, consultez la page [Authentification unique avec le proxy d’application](application-proxy-sso-azure-portal.md).

## <a name="linked-sign-on"></a>Authentification liée

Si vous avez déjà configuré une solution d’authentification unique pour vos identités locales, choisissez **Authentification liée**. Cette option permet à Azure Active Directory d’utiliser les solutions d’authentification unique existantes, tout en donnant aux utilisateurs un accès à distance à l’application. 

Pour en savoir plus sur l’authentification liée (antérieurement appelée authentification unique existante), consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="integrated-windows-authentication"></a>Authentification Windows intégrée

Si vos applications locales utilisent l’authentification Windows intégrée ou si vous souhaitez utiliser la délégation Kerberos contrainte (KCD, Kerberos Constrained Delegation) pour l’authentification unique, choisissez **Authentification Windows intégrée**. Avec cette option, vos utilisateurs doivent uniquement s’authentifier auprès d’Azure Active Directory, puis le connecteur de proxy d’application emprunte l’identité de l’utilisateur pour obtenir un jeton Kerberos et se connecter à l’application. 

Pour plus d’informations sur la configuration de l’authentification Windows intégrée, consultez la section [Délégation contrainte Kerberos pour l’authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md).

## <a name="header-based-sign-on"></a>Authentification basée sur l’en-tête 

Si vos applications utilisent des en-têtes pour l’authentification, choisissez **Authentification basée sur l’en-tête**. Avec cette option, vos utilisateurs doivent s’authentifier à Azure Active Directory. Cela concerne les partenaires Microsoft utilisant un service d’authentification tiers appelé PingAccess, qui convertit le jeton d’accès Azure Active Directory dans un format d’en-tête pour l’application. 

Pour plus d’informations sur la configuration de l’authentification basée sur l’en-tête, consultez la page [Authentification basée sur l’en-tête pour une authentification unique avec le Proxy d’application et PingAccess](application-proxy-ping-access.md).

## <a name="next-steps"></a>Étapes suivantes

- [Authentification unique avec le proxy d’application](application-proxy-sso-azure-portal.md)
- [Délégation contrainte Kerberos pour l’authentification unique à vos applications avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)
- [Authentification basée sur l’en-tête pour une authentification unique avec le Proxy d’application et PingAccess](application-proxy-ping-access.md) 
