---
title: Azure Active Directory B2B Collaboration pour les organisations hybrides | Microsoft Docs
description: "Donnez accès aux ressources cloud et locales aux partenaires avec Azure AD B2B Collaboration"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B Collaboration pour les organisations hybrides

Dans une organisation hybride, où vous disposez à la fois de ressources cloud et locales, vous souhaitez peut-être donner accès aux deux types de ressources aux partenaires externes. Pour l’accès aux ressources locales, vous pouvez gérer les comptes des partenaires localement dans votre environnement Active Directory local. Les partenaires se connectent à l’aide des informations d’identification de leur compte de partenaire pour accéder aux ressources de votre organisation. Pour permettre aux partenaires d’accéder aux ressources cloud à l’aide des mêmes informations d’identification, vous pouvez désormais utiliser Azure Active Directory (Azure AD) Connect pour synchroniser les comptes de partenaires dans le cloud en tant qu’utilisateurs Azure AD B2B (autrement dit, en tant qu’utilisateurs avec UserType = Invité).

## <a name="identify-unique-attributes-for-usertype"></a>Identifier les attributs uniques de UserType

Avant d’activer la synchronisation de l’attribut UserType, vous devez d’abord déterminer comment dériver l’attribut UserType de l’Active Directory local. En d’autres termes, vous devez définir les paramètres de votre environnement local qui doivent être uniques pour vos collaborateurs externes. Déterminez un paramètre qui différencie ces collaborateurs externes des membres de votre organisation.

Pour ce faire, les deux approches communes sont les suivantes :

- Vous pouvez désigner un attribut Active Directory local inutilisé (par exemple, extensionAttribute1) à utiliser en tant qu’attribut source. 
- Vous pouvez également dériver la valeur de l’attribut de UserType à partir d’autres propriétés. Par exemple, vous souhaitez synchroniser tous les utilisateurs en tant qu’Invité si leur attribut UserPrincipalName Active Directory local se termine par le domaine *@partners.fabrikam123.org*.
 
Pour plus d’informations sur les conditions requises pour les attributs, consultez [Activer la synchronisation de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurer Azure AD Connect pour synchroniser les utilisateurs avec le cloud

Après avoir identifié l’attribut unique, vous pouvez configurer Azure AD Connect pour synchroniser ces utilisateurs avec le cloud en tant qu’utilisateurs Azure AD B2B (autrement dit, en tant qu’utilisateurs avec UserType = Invité). D’un point de vue des autorisations, ces utilisateurs ne se distinguent pas des utilisateurs B2B créés via le processus d’invitation d’Azure AD B2B Collaboration.

Pour connaître les instructions d’implémentation, consultez [Activer la synchronisation de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble d’Azure AD B2B Collaboration, consultez [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Pour obtenir une vue d’ensemble d’Azure AD Connect, consultez [Intégrer des répertoires locaux à Azure Active Directory](connect/active-directory-aadconnect.md).

