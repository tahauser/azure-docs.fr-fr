---
title: "Forum aux questions sur l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Trouvez les réponses aux questions les plus fréquentes sur l’accès conditionnel dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e89550763209bc2d56dbc5dd374239ff404ccb11
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Forum aux questions sur l’accès conditionnel Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>A quelles applications les stratégies d’accès conditionnel s’appliquent-elles ?

Pour plus d’informations sur les applications qui fonctionnent avec les stratégies d’accès conditionnel, consultez [Applications et navigateurs qui utilisent des règles d’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Les stratégies d’accès conditionnel s’appliquent-elles à la collaboration B2B et aux utilisateurs invités ?

Les stratégies sont appliquées aux utilisateurs dans le cadre d’une collaboration business-to-business (B2B). Toutefois, dans certains cas, un utilisateur peut ne pas être en mesure de satisfaire aux exigences de la stratégie. Par exemple, il est possible que l’organisation d’un utilisateur invité ne prenne pas en charge l’authentification multifacteur. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>La stratégie SharePoint Online s’applique-t-elle également à OneDrive Entreprise ?

Oui. Une stratégie SharePoint Online s’applique également à OneDrive Entreprise.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Pourquoi ne puis-je pas définir une stratégie pour les applications clientes telles que Word ou Outlook ?

Une stratégie d’accès conditionnel définit les conditions requises pour accéder à un service. Elle est appliquée quand l’authentification à ce service a lieu. La stratégie n’est pas définie directement sur une application cliente. Au lieu de cela, elle est appliquée quand un client appelle un service. Par exemple, une stratégie définie sur SharePoint s’applique aux clients qui appellent SharePoint. Une stratégie définie sur Exchange s’applique à Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>La stratégie d’accès conditionnel s’applique-t-elle aux comptes de service ?

Les stratégies d’accès conditionnel s’appliquent à tous les comptes d’utilisateur. Cela inclut les comptes d’utilisateur utilisés comme comptes de service. Souvent, un compte de service qui s’exécute sans assistance ne peut pas répondre aux exigences d’une stratégie d’accès conditionnel. Par exemple, l’authentification multifacteur peut être obligatoire. Les comptes de service peuvent être exclus d’une stratégie à l’aide des paramètres de gestion de stratégie d’accès conditionnel. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Des API graphiques sont-elles disponibles pour la configuration des stratégies d’accès conditionnel ?

Actuellement, non. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Quelle est la stratégie d’exclusion par défaut pour les plateformes d’appareils non prises en charge ?

À l’heure actuelle, les stratégies d’accès conditionnel sont appliquées de manière sélective aux utilisateurs d’appareils iOS et Android. Les applications sur d’autres plateformes d’appareils ne sont pas, par défaut, affectées par la stratégie d’accès conditionnel pour appareils iOS et Android. Un administrateur de locataires peut choisir de remplacer la stratégie globale pour interdire l’accès aux utilisateurs sur des plateformes non prises en charge.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Comment les stratégies d’accès conditionnel fonctionnent-elles pour Microsoft Teams ?

Microsoft Teams s’appuie fortement sur Exchange Online et SharePoint Online pour les principaux scénarios de productivité, tels que les réunions, les calendriers et le partage de fichiers. Les stratégies d’accès conditionnel définies pour ces applications cloud s’appliquent à Microsoft Teams quand un utilisateur s’y connecte directement.

Microsoft Teams est également pris en charge séparément en tant qu’application cloud dans les stratégies d’accès conditionnel Azure Active Directory. Les stratégies d’accès conditionnel définies pour une application cloud s’appliquent à Microsoft Teams quand un utilisateur se connecte. Toutefois, même sans que les stratégies appropriées ne soient appliquées dans les autres applications comme Exchange Online et SharePoint Online, les utilisateurs peuvent toujours être en mesure d’accéder directement à ces ressources.

Les clients de bureau Microsoft Teams pour Windows et Mac prennent en charge l’authentification moderne. L’authentification moderne permet d’utiliser la connexion basée sur la bibliothèque ADAL (Azure Active Directory Authentication Library) pour les applications clientes Microsoft Office sur plusieurs plateformes.