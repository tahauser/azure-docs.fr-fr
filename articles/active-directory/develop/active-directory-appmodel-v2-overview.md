---
title: Point de terminaison Azure Active Directory v2.0 | Microsoft Docs
description: "Introduction à la création d’applications avec une connexion de compte Microsoft et Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: bd090450fad0be855240788c4cfa9dc58c1c4c6d
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Utilisateurs de compte de connexion Microsoft et Azure Active Directory dans une application unique
Auparavant, les développeurs d’application qui souhaitaient prendre en charge à la fois les comptes Microsoft personnels et les comptes professionnels d’Azure Active Directory devait opérer une intégration à deux systèmes distincts. Le point de terminaison Azure Active Directory (Azure AD) v2.0 introduit une nouvelle version d’API d’authentification qui simplifie ce processus. Le point de terminaison Azure AD v2.0 permet une connexion à partir de ces deux types de compte à l’aide d’une intégration unique. Les applications qui utilisent le point de terminaison Azure AD v2.0 peuvent également consommer les API REST à partir de [l’API Microsoft Graph](https://graph.microsoft.io) à l’aide d’un des deux types de compte.

## <a name="getting-started"></a>Prise en main
Choisissez votre plateforme préférée dans la liste ci-dessous pour créer une application à l’aide des bibliothèques et frameworks open source Microsoft. Vous pouvez également utiliser les protocoles OAuth 2.0 et OpenID Connect pour envoyer et recevoir des messages de protocole directement sans l’aide d’une bibliothèque d’authentification.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Découvrir plus en détail le point de terminaison Azure AD v2.0
Découvrez ce que vous pouvez faire avec le point de terminaison Azure AD v2.0 :

* Découvrez les [types d’applications que vous pouvez générer avec le point de terminaison Azure AD v2.0](active-directory-v2-flows.md).
* Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison Azure AD v2.0.
* Regardez cette vidéo pour avoir une vue d’ensemble du point de terminaison Azure AD v2.0 :

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Ressources supplémentaires
Explorez des informations détaillées sur la plateforme du point de terminaison Azure AD v2.0 :

* [Informations de référence sur les protocoles Azure AD v2.0](active-directory-v2-protocols.md)
* [Informations de référence sur les jetons Azure AD v2.0](active-directory-v2-tokens.md)
* [Informations de référence sur les bibliothèques d’authentification Azure AD v2.0](active-directory-v2-libraries.md)
* [Étendues et consentement dans le point de terminaison Azure AD v2.0](active-directory-v2-scopes.md)
* [API Microsoft Graph](https://graph.microsoft.io)

> [!NOTE]
> Si vous ne devez connecter que des comptes professionnels et scolaires à partir d’Azure Active Directory, commencez par le [Guide du développeur Azure AD](active-directory-developers-guide.md). Le point de terminaison Azure AD v2.0 est destiné aux développeurs qui doivent explicitement connecter des comptes personnels Microsoft.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
