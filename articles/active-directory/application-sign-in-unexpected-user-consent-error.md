---
title: "Erreur inattendue lors du consentement à une application | Microsoft Docs"
description: "Aborde les erreurs qui peuvent se produire durant le processus de consentement à une application et ce que vous pouvez faire"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Erreur inattendue lors du consentement à une application

Cet article traite des erreurs qui peuvent se produire durant le processus de consentement à une application. Si vous rencontrez des invites de consentement inattendues qui ne contiennent aucun message d’erreur, consultez [Scénarios d’authentification pour Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Pour fonctionner, de nombreuses applications qui s’intègrent à Azure Active Directory nécessitent des autorisations à d’autres ressources. Quand ces ressources sont également intégrées à Azure Active Directory, vous devez souvent utiliser l’infrastructure de consentement commune pour demander les autorisations nécessaires pour y accéder. 

Une invite de consentement est alors affichée à la première utilisation d’une application, ce qui se produit généralement la première fois qu’une application est utilisée. Toutefois, l’invite peut apparaître lors d’une utilisation ultérieure de l’application.

Certaines conditions doivent être réunies pour qu’un utilisateur consente aux autorisations exigées par une application. Si ces conditions ne sont pas remplies, différentes erreurs peuvent se produire. Vous avez notamment vu les points suivants :

## <a name="requesting-not-authorized-permissions-error"></a>Erreur : demande d’autorisations dont l’octroi n’est pas autorisé
* **AADSTS90093 :**&lt;clientAppDisplayName&gt; demande une ou plusieurs autorisations que vous ne pouvez pas accorder. Contactez un administrateur qui peut donner son consentement à cette application en votre nom.

Cette erreur se produit quand un utilisateur, qui n’est pas un administrateur d’entreprise, tente d’utiliser une application qui demande des autorisations pouvant uniquement être accordées par un administrateur. Pour résoudre cette erreur, un administrateur peut accorder l’accès à l’application au nom de son organisation.

## <a name="policy-prevents-granting-permissions-error"></a>Erreur : stratégie empêchant l’octroi d’autorisations
* **AADSTS90093 :** Un administrateur de &lt;tenantDisplayName&gt; a défini une stratégie qui vous empêche d’octroyer à &lt;nom_application&gt; les autorisations qu’elle demande. Contactez un administrateur de &lt;tenantDisplayName&gt;, qui peut accorder des autorisations à cette application en votre nom.

Cette erreur se produit quand un utilisateur non-administrateur tente d’utiliser une application qui nécessite son consentement et que l’administrateur d’entreprise l’en empêche. Pour résoudre cette erreur, un administrateur peut accorder l’accès à l’application au nom de son organisation.

## <a name="intermittent-problem-error"></a>Erreur : problème intermittent
* **AADSTS90090 :** Un problème intermittent s’est apparemment produit lors de l’enregistrement des autorisations que vous avez tenté d’octroyer à &lt;clientAppDisplayName&gt;. Réessayez plus tard.

Cette erreur indique qu’un problème intermittent côté service s’est produit. Vous pouvez le résoudre en essayant de donner à nouveau votre consentement à l’application.

## <a name="resource-not-available-error"></a>Erreur : ressource non disponible
* **AADSTS65005 :** L’application &lt;clientAppDisplayName&gt; a demandé des autorisations pour accéder à une ressource &lt;resourceAppDisplayName&gt; qui n’est pas disponible. 

Contactez le développeur de l’application.

##  <a name="resource-not-available-in-tenant-error"></a>Erreur : ressource non disponible dans le locataire
* **AADSTS65005:**&lt;clientAppDisplayName&gt; demande l’accès à une ressource &lt;resourceAppDisplayName&gt; qui n’est pas disponible dans votre organisation &lt;tenantDisplayName&gt;. 

Vérifiez que cette ressource est disponible ou contactez un administrateur de &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Erreur : incompatibilité des autorisations
* **AADSTS65005 :** L’application a fait une demande de consentement pour accéder à la ressource &lt;resourceAppDisplayName&gt;. Cette demande a échoué car elle ne correspond pas à la façon dont l’application a été préconfigurée pendant son inscription. Contactez le fournisseur de l’application.**

Ces erreurs se produisent quand l’application à laquelle un utilisateur tente de donner son consentement demande des autorisations pour accéder à une application de ressources introuvable dans l’annuaire (locataire) de l’organisation. Cela peut se produire pour plusieurs raisons :

-   Le développeur de l’application cliente a configuré l’application de manière incorrecte, la forçant à demander l’accès à une ressource non valide. Dans ce cas, le développeur de l’application doit mettre à jour la configuration de l’application cliente pour résoudre ce problème.

-   Un principal de service représentant l’application de ressources cible n’existe pas dans l’organisation, ou il a existé mais a été supprimé. Pour résoudre ce problème, un principal de service représentant l’application de ressources doit être approvisionné dans l’organisation pour que l’application cliente puisse demander des autorisations d’accès à ce principal de service. Vous pouvez pour cela procéder de plusieurs façons, selon le type d’application :

    -   Acquisition d’un abonnement pour l’application de ressources (applications publiées par Microsoft)

    -   Consentement à l’application de ressources

    -   Octroi des autorisations de l’application par le biais du portail Azure

    -   Ajout de l’application à partir de la galerie d’applications Azure AD

## <a name="next-steps"></a>Étapes suivantes 

[Applications, autorisations et consentement dans Azure Active Directory (point de terminaison v1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Étendues, autorisations et consentement dans Azure Active Directory (point de terminaison v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


