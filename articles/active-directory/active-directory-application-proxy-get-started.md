---
title: "Offrir un accès à distance sécurisé aux applications locales"
description: "Explique comment utiliser le Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 3ca7c7919f6cfcece38073520162dc44bbfd748e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Offrir un accès à distance sécurisé aux applications locales

Aujourd’hui, les employés veulent être productifs partout, tout le temps, depuis n’importe quel appareil. Ils veulent pouvoir travailler sur leurs propres appareils (tablettes, téléphones ou ordinateurs portables). Ils veulent aussi pouvoir accéder à toutes leurs applications, qu’il s’agisse d’applications SaaS dans le cloud ou d’applications métier sur le réseau local de l’entreprise. La fourniture d’un accès aux applications locales implique généralement le recours à des réseaux privés virtuels (VPN) ou à des zones démilitarisées (DMZ). Non seulement ces solutions sont complexes et difficiles à sécuriser, mais leur configuration et leur gestion ont un coût élevé.

Il y a une meilleure solution.

Le monde du travail mobile actuel axé sur le cloud exige une solution d’accès à distance moderne. Le Proxy d’application Azure AD est une fonctionnalité d’Azure Active Directory qui fournit un accès à distance en tant que service. Il est donc facile à déployer, à utiliser et à gérer.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Présentation du Proxy d’application Azure Active Directory
Le Proxy d’application Azure AD offre une authentification unique (SSO) et un accès à distance sécurisé pour les applications web hébergées en local. Parmi les applications que vous pourriez souhaiter publier se trouvent les sites SharePoint, Outlook Web Access ou toute autre application web LOB dont vous disposez. Ces applications web locales sont intégrées à Azure AD, la même plateforme d’identité et de contrôle que celle utilisée par O365. Les utilisateurs peuvent accéder aux applications locales de la même manière qu’ils accèdent à O365 et à d’autres applications SaaS intégrées à Azure AD. Il n’est pas nécessaire de modifier l’infrastructure réseau ou de disposer d’un VPN pour pouvoir proposer cette solution à vos utilisateurs.

## <a name="why-is-application-proxy-a-better-solution"></a>Pourquoi le proxy d’application est-il une meilleure solution ?
Le Proxy d’application Azure AD fournit une solution d’accès à distance simple, sécurisée et rentable à toutes vos applications locales.

Le Proxy d’application Azure AD est :

* **Simple**
   * Vous n’avez pas besoin de modifier ou de mettre à jour vos applications pour qu’elles fonctionnent avec le Proxy d’application. 
   * Vos utilisateurs bénéficient d’une expérience d’authentification cohérente. Ils peuvent utiliser le portail MyApps pour obtenir une authentification unique pour les applications SaaS du cloud et pour vos applications locales. 
* **Sécuriser**
   * Lorsque vous publiez vos applications à l’aide du Proxy d’application Azure AD, vous pouvez tirer parti des nombreux contrôles d’autorisation et des analyses de sécurité dans Azure. Vous obtenez une sécurité à l’échelle du cloud et des fonctionnalités de sécurité Azure, telles que l’accès conditionnel et la vérification en deux étapes.
   * Vous n’êtes pas obligé d’ouvrir une connexion entrante via le pare-feu pour fournir à vos utilisateurs un accès à distance. 
* **Économique**
   * Le Proxy d’application fonctionne dans le cloud, afin de vous permettre de gagner du temps et de faire des économies. Les solutions locales vous obligent généralement à définir et à gérer les zones démilitarisées, les serveurs Edge ou les autres infrastructures complexes.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Quels types d’applications fonctionnent avec le Proxy d’application ?
Avec le Proxy d’application Azure AD, vous pouvez accéder à différents types d’applications internes :

* Applications web qui utilisent [l’authentification Windows intégrée](active-directory-application-proxy-sso-using-kcd.md) pour l’authentification  
* Applications web qui utilisent l’accès [basé sur un en-tête](application-proxy-ping-access.md) ou sur un formulaire  
* API web que vous voulez exposer aux applications enrichies sur différents appareils  
* Applications hébergées derrière une [passerelle Bureau à distance](application-proxy-publish-remote-desktop.md)  
* Applications clientes complètes intégrées à Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Comment fonctionne le Proxy d’application ?
Vous devez configurer deux composants pour faire fonctionner le Proxy d’application : un connecteur et un point de terminaison externe. 

Le connecteur est un agent léger qui se trouve sur un serveur Windows au sein de votre réseau. Le connecteur fluidifie le flux de trafic du service de Proxy d’application dans le cloud vers votre application locale. Il n’utilise que des connexions sortantes ; vous n’êtes donc pas obligé d’ouvrir des ports entrants ou de placer quoi que ce soit dans la zone démilitarisée. Les connecteurs sont sans état et extraient les informations dont ils ont besoin sur le cloud. Pour savoir comment les connecteurs équilibrent la charge ou s’authentifient, voir [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md). 

C’est par le point de terminaison externe que vos utilisateurs atteignent les applications hors de votre réseau. Ils peuvent accéder directement à une URL externe que vous déterminez, ou accéder à l’application via le portail MyApps. Lorsque les utilisateurs passent par l’un de ces points de terminaison, ils s’authentifient dans Azure AD, puis sont acheminés via le connecteur vers l’application locale.

 ![Diagramme du Proxy d’application Azure AD](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. L’utilisateur accède à l’application par le biais du service de proxy d’application et il est dirigé vers la page de connexion Azure AD pour s’identifier.
2. Après la connexion, un jeton est généré et envoyé à l’appareil client.
3. Le client envoie le jeton au service de proxy d’application qui récupère le nom d’utilisateur principal (UPN) et le nom de sécurité principal (SPN) du jeton, puis dirige la requête vers le connecteur du proxy d’application.
4. Si vous avez configuré l’authentification unique, le connecteur effectue toute authentification supplémentaire requise pour le compte de l’utilisateur.
5. Le connecteur envoie la requête à l’application locale.  
6. La réponse est envoyée à l’utilisateur par le biais du service et du connecteur de proxy d’application.

### <a name="single-sign-on"></a>Authentification unique
Le Proxy d’application Azure AD fournit l’authentification unique (SSO) aux applications qui utilisent l’authentification Windows intégrée (IWA) ou aux applications prenant en charge les revendications. Si votre application utilise l’authentification IWA, le Proxy d’application emprunte l’identité de l’utilisateur à l’aide de la délégation Kerberos contrainte pour fournir l’authentification unique. Si vous avez une application prenant en charge les revendications qui fait confiance à Azure Active Directory, l’authentification unique fonctionne, car l’utilisateur a déjà été authentifié par Azure AD.

Pour plus d’informations sur Kerberos, consultez la page [All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd) (Tout ce que vous voulez savoir sur Kerberos Constrained Delegation (KCD)).

### <a name="managing-apps"></a>Gestion des applications
Une fois votre application publiée avec le Proxy d’application, la gestion se fait comme pour toute autre application d’entreprise dans le portail Azure. Vous pouvez utiliser les fonctionnalités de sécurité Azure Active Directory (accès conditionnel et vérification en deux étapes, par exemple), mais aussi contrôler les autorisations des utilisateurs et personnaliser la marque de votre application. 

## <a name="get-started"></a>Prise en main

Avant de configurer le Proxy d’application, assurez-vous d’être l’administrateur global d’une [édition d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) et d’un annuaire pris en charge.

Prise en main du Proxy d’application en deux étapes :

1. [Activer le proxy d’application et configurer le connecteur](active-directory-application-proxy-enable.md).    
2. [Publier des applications](active-directory-application-proxy-publish.md) : utilisez l’Assistant simple et rapide pour publier vos applications locales et les rendre accessibles à distance.

## <a name="whats-next"></a>Et ensuite ?
Une fois votre première application publiée, vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)
* [Utiliser des serveurs proxy locaux actuels](application-proxy-working-with-proxy-servers.md) 
* [Définir une page d’accueil personnalisée](application-proxy-office365-app-launcher.md)

Pour les dernières nouvelles et mises à jour, consultez le site [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

