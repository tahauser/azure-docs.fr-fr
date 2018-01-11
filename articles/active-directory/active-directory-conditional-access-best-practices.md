---
title: "Meilleures pratiques l’accès conditionnel dans Azure Active Directory | Microsoft Docs"
description: "Découvrez-en davantage sur les éléments à connaître ce que vous devez évite lors de la configuration des stratégies d’accès conditionnel."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8c6707505a6331b53e06b1de60575dd3637ea477
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Meilleures pratiques l’accès conditionnel dans Azure Active Directory

Cette rubrique vous fournit des informations sur les éléments à connaître ce que vous devez évite lors de la configuration des stratégies d’accès conditionnel. Avant de lire cette rubrique, vous devez vous familiariser avec les concepts et la terminologie décrits dans [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="whats-required-to-make-a-policy-work"></a>Qu’est-ce qui est nécessaire pour faire fonctionner une stratégie ?

Lorsque vous créez une stratégie, aucun utilisateur, groupe, application ou contrôle d’accès n’est sélectionné.

![Applications cloud](./media/active-directory-conditional-access-best-practices/02.png)


Pour que votre stratégie fonctionne, vous devez configurer ce qui suit :


|Quoi           | Comment                                  | Pourquoi|
|:--            | :--                                  | :-- |
|**Applications cloud** |Vous devez sélectionner une ou plusieurs applications.  | L’objectif d’une stratégie d’accès conditionnel est de vous permettre d’ajuster la manière dont les utilisateurs autorisés peuvent accéder à vos applications.|
| **Utilisateurs et groupes** | Vous devez sélectionner au moins un utilisateur ou groupe autorisé à accéder aux applications cloud sélectionnées. | Une stratégie d’accès conditionnel, à laquelle aucun utilisateur ou groupe n’est affecté, n’est jamais déclenchée. |
| **Contrôles d’accès** | Vous devez sélectionner au moins un contrôle d'accès. | Votre processeur de stratégies doit savoir que faire si vos conditions sont remplies.|


En plus de ces exigences de base, dans de nombreux cas, vous devez également configurer une condition. Si une stratégie peut fonctionner sans condition configurée, les conditions constituent un facteur déterminant pour l’ajustement précis de l’accès à vos applications.


![Applications cloud](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>Comment les affectations sont-elles évaluées ?

Toutes les attributions sont reliées par l’opérateur logique **AND**. Si vous configurez plusieurs affectations, elles doivent toutes être satisfaites pour que la stratégie soit déclenchée.  

Pour configurer une condition d’emplacement applicable à toutes les connexions non établies depuis le réseau de votre organisation, vous devez :

- inclure **tous les emplacements**,
- exclure **toutes les adresses IP approuvées**.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Que se passe-t-il si vous avez configuré des stratégies dans le portail Azure Classic et le portail Azure ?  
Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Que se passe-t-il si vous avez des stratégies dans le portail Intune Silverlight et le portail Azure ?
Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Que se passe-t-il si j’ai configuré plusieurs stratégies pour le même utilisateur ?  
À chaque connexion, Azure Active Directory évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L’accès conditionnel fonctionne-t-il avec Exchange ActiveSync ?

Oui, vous pouvez utiliser Exchange ActiveSync dans une stratégie d’accès conditionnel.


## <a name="what-you-should-avoid-doing"></a>Ce que vous devez éviter

L’infrastructure d’accès conditionnel vous offre une souplesse de configuration exceptionnelle. Toutefois, une grande souplesse signifie également que vous devez examiner chaque stratégie de configuration avant de la mettre en œuvre afin d’éviter des résultats indésirables. Dans ce contexte, prêtez une attention particulière à l’affectation de jeux complets comme par exemple **tous les utilisateurs / groupes / applications cloud**.

Dans votre environnement, vous devez éviter les configurations suivantes :


**Pour tous les utilisateurs, toutes les applications cloud :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.

- **Exiger un appareil conforme** : pour les utilisateurs qui n’ont pas encore inscrit leurs appareils, cette stratégie bloque tout accès, notamment l’accès au portail Intune. Si vous êtes un administrateur sans appareil inscrit, cette stratégie vous bloque et vous ne pouvez pas retourner dans le portail Azure pour modifier la stratégie.

- **Exiger la jonction de domaine** : ce blocage d’accès par stratégie peut également bloquer l’accès pour tous les utilisateurs de votre organisation si vous n’avez pas encore d’appareil joint à un domaine.


**Pour tous les utilisateurs, toutes les applications cloud, toutes les plates-formes d’appareils :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.



## <a name="policy-migration"></a>Migration des stratégies

Envisagez de migrer les stratégies que vous n’avez pas créées dans le portail Azure, car :

- Vous pouvez maintenant résoudre des scénarios que vous ne pouviez pas gérer auparavant.

- Vous pouvez réduire le nombre de stratégies que vous devez gérer en les consolidant.   

- Vous pouvez gérer toutes vos stratégies d’accès conditionnel dans un emplacement central.

- Le portail Azure Classic va être mis hors service.   


Pour plus d’informations, consultez [Migrer les stratégies classiques dans le portail Azure](active-directory-conditional-access-migration.md).


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).
