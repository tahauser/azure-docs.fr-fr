---
title: "Affichage de votre application dans la galerie d’applications Azure AD"
description: "Comment répertorier une application qui prend en charge l'authentification unique dans la galerie Azure Active Directory | Microsoft Azure"
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
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Affichage de votre application dans la galerie d’applications Azure AD


##  <a name="what-is-azure-ad-app-gallery"></a>Qu’est que la galerie d’applications Azure AD ?

Azure AD est un service d’identité basé sur le cloud. [La galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) est une boutique commune dans laquelle tous les connecteurs d’application sont publiés pour l’authentification unique et l’approvisionnement des utilisateurs. Nos clients mutuels qui utilisent Azure AD comme fournisseur d’identité recherchent des connecteurs d’applications SaaS différents, publiés ici. L’administrateur informatique ajoute le connecteur à partir de la galerie d’applications, puis le configure et l’utilise pour l’authentification unique et l’approvisionnement. Azure AD prend en charge tous les principaux protocoles de fédération pour l’authentification unique, tels que SAML 2.0, OpenID Connect, OAuth et WS-Fed. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>Quels sont les avantages de l’affichage de l’application dans la galerie ?

*  Proposer la meilleure expérience d’authentification unique possible aux clients.

*  Configuration minimale et simple de l’application.

*  Les clients peuvent rechercher l’application dans la galerie. 

*  Tous les clients peuvent utiliser cette intégration, quelle que soit la référence SKU Azure AD : Gratuit, De base ou Premium.

*  Didacticiel de configuration pas à pas pour les clients mutuels.

*  Autorisez l’approvisionnement des utilisateurs pour la même application si vous utilisez SCIM.


##  <a name="what-are-the-pre-requisites"></a>Quelles sont les conditions préalables requises ?

Pour répertorier une application dans la galerie Azure AD, l’application doit tout d’abord utiliser l’un des protocoles de fédération pris en charge par Azure AD. Lisez les conditions générales de la galerie d’applications Azure AD ici. Si vous utilisez : 

*   **OpenID Connect** - Créez l’application multilocataire dans Azure AD et implémentez une [infrastructure de consentement d’Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) pour votre application. Envoyez la requête de connexion à un point de terminaison commun afin que n’importe quel client puisse donner son consentement à l’application. Vous pouvez contrôler l’accès utilisateur du client en fonction de l’ID de locataire et de l’UPN de l’utilisateur, reçus dans le jeton. Pour intégrer votre application dans Azure AD, vous pouvez suivre les [instructions pour développeurs](active-directory-authentication-scenarios.md).

*   **SAML 2.0 ou WS-Fed** - Votre application doit avoir la possibilité d’effectuer l’intégration de l’authentification unique SAML/WS-Fed en mode SP ou IDP. Toute application prenant en charge SAML 2.0 peut être intégrée directement dans un locataire Azure AD à l’aide des [instructions pour ajouter une application personnalisée](../active-directory-saas-custom-apps.md).

*   **Authentification unique basée sur un mot de passe** - Créez une application web qui a une page de connexion HTML pour configurer [l’authentification unique basée sur un mot de passe](../active-directory-appssoaccess-whatis.md). L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d'applications de médiaux sociaux de votre organisation. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>Processus d’envoi de la requête dans le portail

Une fois que vous avez testé le bon fonctionnement de l’intégration de votre application avec Azure AD, vous devez envoyer votre requête d’accès à notre [portail Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Si vous disposez d’un compte Office 365, vous pouvez l’utiliser pour vous connecter à ce portail. Dans le cas contraire, utilisez votre ID Microsoft (Live ID, Outlook, Hotmail, etc.) pour vous connecter. La page suivante s’affiche pour demander l’accès. Fournissez une justification dans la zone de texte, puis cliquez sur **Demander l’accès**. Notre équipe étudiera toutes les informations fournies et vous accordera l’accès en conséquence. Après cela, vous pouvez ouvrir une session sur le portail et envoyer votre requête détaillée pour l’application.

Si vous rencontrez un problème lié à l’accès, contactez l’[équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Requête d’accès sur le portail SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Chronologies
    
*   Processus d’énumération des applications SAML 2.0 ou WS-Fed dans la galerie - **7 à 10 jours ouvrés**

   ![Chronologie de l’énumération des applications SAML dans la galerie](./media/active-directory-app-gallery-listing/timeline.png)

*   Processus d’énumération des applications OpenID Connect dans la galerie - **2 à 5 jours ouvrés**

   ![Chronologie de l’énumération des applications SAML dans la galerie](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalades

Pour faire remonter un problème, envoyez un message électronique à [l’équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) et nous vous répondrons dès que possible.

