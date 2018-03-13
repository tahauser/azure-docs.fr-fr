---
title: "Azure Active Directory Identity Protection - Déblocage des utilisateurs | Microsoft Docs"
description: "Découvrez comment débloquer les utilisateurs bloqués par une stratégie Azure Active Directory Identity Protection."
services: active-directory
keywords: "azure active directory identity protection, déblocage des utilisateurs"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: ab11f27ceed716ba90e9027eee1775c190d2d93f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory Identity Protection - Déblocage des utilisateurs
Avec Azure Active Directory Identity Protection, vous pouvez configurer des stratégies pour bloquer les utilisateurs si les conditions configurées sont remplies. En règle générale, un utilisateur bloqué contacte le support technique pour pouvoir être débloqué. Cette rubrique explique les étapes à suivre pour débloquer un utilisateur bloqué.

## <a name="determine-the-reason-for-blocking"></a>Déterminer la raison du blocage
Dans un premier temps, pour débloquer un utilisateur, vous devez déterminer le type de stratégie qui l’a bloqué, car les étapes suivantes en dépendent.
Avec Azure Active Directory Identity Protection, un utilisateur peut être bloqué par une stratégie en matière de risque à la connexion ou par une stratégie de risque d’utilisateur.

Vous pouvez déterminer le type de stratégie qui a bloqué l’utilisateur à partir de l’en-tête dans la boîte de dialogue présentée à l’utilisateur lors d’une tentative de connexion :

| Stratégie | Boîte de dialogue utilisateur |
| --- | --- |
| Risque à la connexion |![Connexion bloquée](./media/active-directory-identityprotection-unblock-howto/02.png) |
| Risque de l’utilisateur |![Compte bloqué](./media/active-directory-identityprotection-unblock-howto/104.png) |

Un utilisateur qui est bloqué par :

* Une stratégie en matière de risque à la connexion, également appelée connexion suspecte
* Une stratégie de risque d’utilisateur, également appelé compte à risque

## <a name="unblocking-suspicious-sign-ins"></a>Déblocage des connexions suspectes
Pour débloquer une connexion suspecte, vous disposez des options suivantes :

1. **Connexion à partir d’un emplacement ou d’un appareil connu** : les connexions suspectes bloquées sont généralement des tentatives de connexion effectuées à partir d’un emplacement ou d’un appareil inconnu. Vos utilisateurs peuvent déterminer rapidement s’il s’agit bien de la raison du blocage en essayant de se connecter depuis un appareil ou un emplacement connu.
2. **Exclure de la stratégie** : si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour certains utilisateurs, vous pouvez les exclure de cette dernière. Consultez [Azure Active Directory Identity Protection](active-directory-identityprotection.md) pour plus de détails.
3. **Désactiver la stratégie** : si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Consultez [Azure Active Directory Identity Protection](active-directory-identityprotection.md) pour plus de détails.

## <a name="unblocking-accounts-at-risk"></a>Déblocage des comptes à risque
Pour débloquer un compte à risque, vous disposez des options suivantes :

1. **Réinitialiser le mot de passe réinitialisé** : vous pouvez réinitialiser le mot de passe de l’utilisateur. Pour plus d’informations, consultez la page [Réinitialisation manuelle et sécurisée du mot de passe](active-directory-identityprotection.md#manual-secure-password-reset) .
2. **Ignorer tous les événements risque** : la stratégie de risque de l’utilisateur bloque un utilisateur si le niveau de risque d’un utilisateur configuré a été atteint. Vous pouvez réduire le niveau de risque d’un utilisateur en fermant manuellement les événements à risque signalés. Pour plus d’informations, consultez la page [Fermeture manuelle des événements à risque](active-directory-identityprotection.md#closing-risk-events-manually).
3. **Exclure de la stratégie** : si vous pensez que la configuration actuelle de votre stratégie d’authentification est à l’origine de problèmes pour certains utilisateurs, vous pouvez les exclure de cette dernière. Consultez [Azure Active Directory Identity Protection](active-directory-identityprotection.md) pour plus de détails.
4. **Désactiver la stratégie** : si vous pensez que votre configuration de la stratégie est à l’origine des problèmes pour tous vos utilisateurs, vous pouvez désactiver la stratégie. Consultez [Azure Active Directory Identity Protection](active-directory-identityprotection.md) pour plus de détails.

## <a name="next-steps"></a>Étapes suivantes
 Vous souhaitez en savoir plus sur Azure AD Identity Protection ? Consultez la rubrique [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
