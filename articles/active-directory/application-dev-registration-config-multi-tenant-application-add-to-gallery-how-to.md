---
title: "Ajouter une application mutualisée dans la galerie d’applications Azure AD | Microsoft Docs"
description: "Explique comment vous pouvez afficher votre application mutualisée personnalisée dans la galerie d’applications Azure AD."
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
ms.openlocfilehash: 82f7abbe5814f9b154b6888d5b599e7706eb879b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Ajouter une application mutualisée dans la galerie d’applications Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Qu’est-ce que la galerie d’applications Azure AD ?

Azure Active Directory (Azure AD) est un service d’identité basé sur le cloud. La [galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) se trouve dans l’app store de la Place de marché Microsoft Azure, où tous les connecteurs d’applications sont publiés pour l’authentification unique et le provisionnement des utilisateurs. Les clients qui utilisent Azure AD comme fournisseur d’identité y trouvent les connecteurs d’applications SaaS publiés. Les administrateurs informatiques ajoutent des connecteurs à partir de la galerie d’applications, puis configurent et utilisent ces connecteurs pour l’authentification unique et le provisionnement. Azure AD prend en charge tous les principaux protocoles de fédération, notamment SAML 2.0, OpenID Connect, OAuth et WS-Fed pour l’authentification unique. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Si votre application prend en charge SAML ou OpenIDConnect
Si vous avez une application mutualisée à répertorier dans la galerie d’applications Azure Active Directory, vous devez d’abord vérifier qu’elle prend en charge l’une des technologies d’authentification unique suivantes :

- **OpenID Connect** : Pour répertorier votre application, créez l’application multilocataire dans Azure AD et implémentez le [framework de consentement Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) pour votre application. Envoyez la demande de connexion à un point de terminaison courant afin que n’importe quel client puisse donner son consentement à l’application. Vous pouvez contrôler un accès utilisateur en fonction de l’ID de locataire et de l’UPN de l’utilisateur reçus dans le jeton. Soumettez l’application à l’aide de la procédure décrite dans [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

- **SAML**: Si votre application prend en charge SAML 2.0, l’application peut être répertoriée dans la galerie. Suivez les instructions dans [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Si votre application ne prend pas en charge SAML ou OpenIDConnect
Les applications ne prenant pas en charge SAML ou OpenIDConnect peuvent toujours être intégrées dans la galerie d’applications via une technologie d’authentification unique par mot de passe.

L’authentification unique par mot de passe, également appelée archivage de mot de passe, vous permet de gérer l’accès utilisateur et les mots de passe pour les applications Web qui ne prennent pas en charge la fédération d’identité. Elle est également utile pour les scénarios où plusieurs utilisateurs doivent partager un seul compte, par exemple les comptes d’applications de médias sociaux de votre organisation. 

Si vous souhaitez répertorier votre application avec cette technologie :
1. Créez une application web qui a une page de connexion HTML pour configurer [l’authentification unique par mot de passe](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). 
2. Soumettez la demande comme indiqué dans [Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="escalations"></a>Escalades

Pour faire remonter un problème, envoyez un e-mail à l’[équipe d’intégration de l’authentification unique Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) et nous reviendrons vers vous dès que possible.

## <a name="next-steps"></a>étapes suivantes
Apprenez comment [afficher votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
