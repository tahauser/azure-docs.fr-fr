---
title: "Ajouter une application mutualisée dans la galerie d’applications Azure AD | Microsoft Docs"
description: "Explique comment vous pouvez afficher votre application mutualisée personnalisée dans la galerie d’applications Azure AD"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Ajouter une application mutualisée dans la galerie d’applications Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Qu’est-ce que la galerie d’applications Azure AD ?

Azure AD est un service d’identité basé sur le cloud. [La galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) est une boutique commune dans laquelle tous les connecteurs d’application sont publiés pour l’authentification unique et l’approvisionnement des utilisateurs. Nos clients mutuels qui utilisent Azure AD comme fournisseur d’identité recherchent des connecteurs d’applications SaaS différents, publiés ici. L’administrateur informatique ajoute le connecteur à partir de la galerie d’applications, puis le configure et l’utilise pour l’authentification unique et l’approvisionnement. Azure AD prend en charge tous les principaux protocoles de fédération pour l’authentification unique, tels que SAML 2.0, OpenID Connect, OAuth et WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Si votre application prend en charge SAML ou OpenIDConnect
Si vous avez une application mutualisée à répertorier dans la galerie d’applications Azure Active Directory, vous devez d’abord vérifier qu’elle prend en charge l’une des technologies d’authentification unique suivantes :

1. **OpenID Connect** - Créez l’application multilocataire dans Azure AD et implémentez une [infrastructure de consentement d’Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) pour votre application. Envoyez la requête de connexion à un point de terminaison commun afin que n’importe quel client puisse donner son consentement à l’application. Vous pouvez contrôler l’accès utilisateur du client en fonction de l’ID de locataire et de l’UPN de l’utilisateur, reçus dans le jeton. Soumettez l’application en suivant la procédure décrite dans [cet article](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML** : si votre application prend en charge SAML 2.0, nous pouvons répertorier votre application dans la galerie, et les instructions sont énumérées [ici](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

Si votre application mutualisée prend en charge l’un de ces modes d’authentification unique et que vous souhaitez l’ajouter à la galerie d’applications Azure Active Directory, suivez la procédure décrite dans [cet article](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing). 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Si votre application ne prend pas en charge SAML ou OpenIDConnect
Même si votre application ne prend pas en charge un de ces modes, nous pouvons l’intégrer dans notre galerie à l’aide de notre technologie d’authentification unique par mot de passe.

**Authentification unique basée sur un mot de passe** - Créez une application web qui a une page de connexion HTML pour configurer [l’authentification unique basée sur un mot de passe](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). L'authentification unique basée sur un mot de passe, également appelée  archivage de mot de passe, vous permet de gérer l'accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d'identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d'applications de médiaux sociaux de votre organisation. 

Si vous souhaitez répertorier votre application avec cette technologie, faites-en la demande en suivant la procédure décrite dans [cet article](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalades

Pour faire remonter un problème, envoyez un message électronique à [l’équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) et nous vous répondrons dès que possible.

## <a name="next-steps"></a>Étapes suivantes
[Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
