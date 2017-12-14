---
title: "Ajouter une application mutualisée dans la galerie d’applications Azure AD | Microsoft Docs"
description: "Explique comment vous pouvez afficher votre application mutualisée personnalisée dans la galerie d’applications Azure AD"
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
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Ajouter une application mutualisée dans la galerie d’applications Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Qu’est-ce que la galerie d’applications Azure AD ?

La galerie d’applications Azure AD est un excellent moyen pour proposer votre application aux millions de clients Azure Active Directory et élargir l’impact et la portée de votre application sur le marché. Les étapes ci-dessous montrent comment vous pouvez ajouter votre application dans la galerie d’applications Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Si votre application prend en charge SAML ou OpenIDConnect
Si vous avez une application mutualisée à répertorier dans la galerie d’applications Azure Active Directory, vous devez d’abord vérifier qu’elle prend en charge l’une des technologies d’authentification unique suivantes :

1. **OpenID Connect** : intégration directe dans Azure AD à l'aide d'OpenID Connect pour l'authentification et l'API de consentement Azure AD pour la configuration. Si vous débutez une intégration et que votre application ne prend pas en charge SAML, il s'agit du mode de recommandé.
2. **SAML** : votre application a déjà la possibilité de configurer des fournisseurs d'identité tiers utilisant le protocole SAML.

Si votre application mutualisée prend en charge un de ces modes d’authentification unique et que vous souhaitez l’ajouter dans la galerie d’applications Azure Active Directory, suivez les étapes décrites dans le document ci-dessous. Pour bien démarrer rapidement, envoyez un e-mail à l’adresse **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Si votre application ne prend pas en charge SAML ou OpenIDConnect
Même si votre application ne prend pas en charge un de ces modes, nous pouvons l’intégrer dans notre galerie à l’aide de notre technologie d’authentification unique par mot de passe. Si vous souhaitez explorer cette option, vous pouvez envoyer un e-mail à **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Étapes suivantes
[Affichage de votre application dans la galerie d’applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
