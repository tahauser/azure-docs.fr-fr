---
title: "Listing de votre application dans la galerie d’applications Azure Active Directory | Microsoft Docs"
description: "Comment lister une application qui prend en charge l’authentification unique dans la galerie d’applications Azure Active Directory"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Lister votre application dans la galerie d’applications Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>Qu’est-ce que la galerie d’applications Azure AD ?

Azure Active Directory (Azure AD) est un service d’identité basé sur le cloud. La [galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) se trouve dans l’app store de la Place de marché Microsoft Azure, où tous les connecteurs d’applications sont publiés pour l’authentification unique et le provisionnement des utilisateurs. Les clients qui utilisent Azure AD comme fournisseur d’identité y trouvent les connecteurs d’applications SaaS publiés. Les administrateurs informatiques ajoutent des connecteurs à partir de la galerie d’applications, puis configurent et utilisent ces connecteurs pour l’authentification unique et le provisionnement. Azure AD prend en charge tous les principaux protocoles de fédération pour l’authentification unique, notamment SAML 2.0, OpenID Connect, OAuth et WS-Fed. 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Quels sont les avantages du listing d’une application dans la galerie ?

*  Les clients bénéficient de l’expérience d’authentification unique la meilleure possible.

*  La configuration de l’application est simple et minime. 

*  Une recherche rapide permet de trouver votre application dans la galerie.

*  Les clients Azure AD des abonnements Gratuit, De base et Premium peuvent tous utiliser cette intégration. 

*  Les clients mutuels obtiennent un tutoriel de configuration pas à pas. 

*  Les clients qui utilisent SCIM peuvent utiliser le provisionnement pour la même application.


##  <a name="prerequisites-implement-federation-protocol"></a>Prérequis : implémenter un protocole de fédération

Pour lister une application dans la galerie d’applications Azure AD, vous devez tout d’abord implémenter l’un des protocoles de fédération suivants pris en charge par Azure AD. Lisez les conditions générales de la galerie d’applications Azure AD ici. 

*   **OpenID Connect** : Créez l’application multilocataire dans Azure AD et implémentez le [framework de consentement Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) pour votre application. Envoyez la demande de connexion à un point de terminaison courant afin que n’importe quel client puisse donner son consentement à l’application. Vous pouvez contrôler l’accès utilisateur en fonction de l’ID de locataire et de l’UPN de l’utilisateur, reçus dans le jeton. Pour intégrer votre application dans Azure AD, suivez les [instructions pour développeurs](active-directory-authentication-scenarios.md).

*   **SAML 2.0** ou **WS-Fed** : Votre application doit pouvoir effectuer l’intégration de l’authentification unique SAML/WS-Fed en mode SP ou IDP. Si votre application prend en charge SAML 2.0, vous pouvez l’intégrer directement dans un locataire Azure AD en suivant les [instructions pour ajouter une application personnalisée](../active-directory-saas-custom-apps.md).

*   **Authentification unique basée sur un mot de passe** : Créez une application web qui a une page de connexion HTML pour configurer [l’authentification unique basée sur un mot de passe](../active-directory-appssoaccess-whatis.md). L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de médias sociaux de votre organisation. 

## <a name="submit-the-request-in-the-portal"></a>Envoyer la demande dans le portail

Une fois que vous avez testé le bon fonctionnement de l’intégration de votre application dans Azure AD, envoyez votre demande d’accès à notre [portail Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Si vous avez un compte Office 365, utilisez-le pour vous connecter à ce portail. Dans le cas contraire, utilisez votre compte Microsoft (comme Outlook ou Hotmail) pour vous connecter.

Une fois connecté, la page suivante s’affiche. Justifiez votre besoin d’accès dans la zone de texte, puis sélectionnez **Demander l’accès**. Notre équipe examine ces informations et vous octroie l’accès en conséquence. Ensuite, vous pouvez vous connecter au portail et envoyer votre demande d’application détaillée.

En cas de problème d’accès, contactez l’[équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Demande d’accès sur le portail SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Délais
    
La durée du processus de listing d’une application SAML 2.0 ou WS-Fed dans la galerie est de 7 à 10 jours ouvrables.

   ![Délai du listing d’une application SAML dans la galerie](./media/active-directory-app-gallery-listing/timeline.png)

La durée du processus de listing d’une application OpenID Connect dans la galerie est de 2 à 5 jours ouvrables.

   ![Durée du listing d’une application SAML dans la galerie](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Remontées

Pour faire remonter un problème, envoyez un e-mail à l’[équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) et nous vous répondrons dès que possible.

