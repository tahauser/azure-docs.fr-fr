---
title: "Outil de simulation d’accès conditionnel Azure Active Directory (préversion) | Microsoft Docs"
description: "Découvrez comment vous pouvez tester la configuration de vos stratégies d’accès conditionnel Azure Active Directory."
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
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 19ebb30164eee8e03a3cd8f18b6d575c6eee5438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Outil de simulation d’accès conditionnel Azure Active Directory (préversion)

[L’accès conditionnel](active-directory-conditional-access-azure-portal.md) est une fonctionnalité d’Azure Active Directory (Azure AD) qui vous permet de contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Comment savoir ce que vous pouvez attendre des stratégies d’accès conditionnel dans votre environnement ? Pour répondre à cette question, vous pouvez utiliser **l’outil de simulation d’accès conditionnel**.

Cet article explique comment vous pouvez utiliser cet outil pour tester vos stratégies d’accès conditionnel.

## <a name="what-it-is"></a>Présentation

**L’outil de simulation d’accès conditionnel** vous permet de comprendre l’impact de vos stratégies d’accès conditionnel sur votre environnement. Au lieu de tester vos stratégies en effectuant manuellement plusieurs connexions, cet outil vous permet d’évaluer une simulation de connexion d’un utilisateur. La simulation évalue l’impact cette connexion sur vos stratégies et génère un rapport de simulation. Le rapport répertorie non seulement les stratégies d’accès conditionnel appliquées, mais également les [stratégies classiques](active-directory-conditional-access-migration.md#classic-policies) si elles existent.    

L’outil de simulation fournit également un moyen de rapidement déterminer les stratégies qui s’appliquent à un utilisateur spécifique. Vous pouvez utiliser ces informations, par exemple, si vous avez besoin de résoudre un problème.  

## <a name="how-it-works"></a>Fonctionnement

Dans **l’outil de simulation d’accès conditionnel**, vous devez tout d’abord configurer les paramètres du scénario utilisateur que vous souhaitez simuler. Ces paramètres comprennent ce qui suit :

- L’utilisateur que vous souhaitez tester 

- Les applications cloud auxquelles l’utilisateur tente d’accéder

- Les conditions dans lesquelles un accès aux applications cloud configurées est effectué
     
Pour la prochaine étape, vous pouvez lancer une simulation qui évalue vos paramètres. Seules les stratégies qui sont activées font partie de l’évaluation.


Une fois l’évaluation terminée, l’outil génère un rapport sur les stratégies affectées.


## <a name="running-the-tool"></a>Exécution de l’outil

Vous pouvez trouver **l’outil de simulation** sur la page **[Accès conditionnel - Stratégies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** dans le portail Azure.

Pour démarrer l’outil, cliquez sur **Scénarios** dans la barre d’outils au-dessus de la liste des stratégies.

![Scénarios](./media/active-directory-conditional-access-whatif/01.png)

Avant de pouvoir exécuter une évaluation, vous devez configurer les paramètres.

## <a name="settings"></a>Paramètres

Cette section fournit des informations sur les paramètres d’une simulation.

![Scénarios](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Utilisateur

Vous ne pouvez sélectionner qu’un seul utilisateur. Il s’agit du seul champ obligatoire.

### <a name="cloud-apps"></a>Applications cloud

La valeur par défaut pour ce paramètre est **Toutes les applications cloud**. Le paramètre par défaut effectue une évaluation de toutes les stratégies disponibles dans votre environnement. Vous pouvez limiter l’étendue aux stratégies qui affectent des applications cloud spécifiques.


### <a name="ip-address"></a>Adresse IP

L’adresse IP est une adresse IPv4 unique pour reproduire la [condition d’emplacement](active-directory-conditional-access-locations.md). L’adresse représente l’adresse accessible sur Internet de l’appareil utilisé par vos utilisateurs pour se connecter. Vous pouvez vérifier l’adresse IP d’un appareil, en accédant par exemple à [Quelle est mon adresse IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plateformes d’appareils

Ce paramètre reproduit la [condition des plateformes d’appareil](active-directory-conditional-access-conditions.md#device-platforms) et représente l’équivalent de **Toutes les plateformes (y compris celles non prises en charge)**. 
### <a name="client-apps"></a>Applications clientes

Ce paramètre reproduit la [condition des applications client](active-directory-conditional-access-conditions.md#client-apps).
Par défaut, ce paramètre entraîne une évaluation de toutes les stratégies ayant **Navigateur** et/ou **Applications mobiles et clients de bureau** sélectionnés. Il détecte également les stratégies qui appliquent **Exchange ActiveSync (EAS)**. Vous pouvez réduire la portée de ce paramètre en sélectionnant :

- **Navigateur** pour évaluer toutes les stratégies ayant au moins **Navigateur** sélectionné. 

- **Applications mobiles et clients de bureau** pour évaluer toutes les stratégies ayant au moins **Applications mobiles et clients de bureau** sélectionné. 


### <a name="sign-in-risk"></a>Risque à la connexion

Ce paramètre reproduit la [condition de risque de connexion](active-directory-conditional-access-conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Évaluation 

Vous démarrez une évaluation en cliquant sur **Scénarios**. Le résultat d’évaluation vous fournit un rapport qui se compose de ce qui suit : 

![Scénarios](./media/active-directory-conditional-access-whatif/03.png)

- Un indicateur précisant si des stratégies classiques existent dans votre environnement
- Les stratégies qui s’appliquent à votre utilisateur
- Les stratégies qui ne s’appliquent pas à votre utilisateur


Si des [stratégies classiques](active-directory-conditional-access-migration.md#classic-policies) existent pour les applications cloud sélectionnées, un indicateur est présenté. En cliquant sur l’indicateur, vous êtes redirigé vers la page des stratégies classiques. Dans la page des stratégies classiques, vous pouvez migrer une stratégie classique ou simplement la désactiver. Vous pouvez retourner aux résultats de l’évaluation en fermant cette page.

Dans la liste des stratégies qui s’appliquent à votre utilisateur sélectionné, vous trouverez également une liste de [contrôles d’octroi](active-directory-conditional-access-controls.md#grant-controls) et de [session](active-directory-conditional-access-controls.md#session-controls) que l’utilisateur doit satisfaire.

Sur la liste des stratégies qui s’appliquent à votre utilisateur, vous pouvez aussi déterminer les raisons pour lesquelles ces stratégies ne s’appliquent pas. Pour chaque stratégie répertoriée, la raison représente la première condition qui n’est pas remplie. Une raison possible de stratégie qui n’est pas appliquée est le cas d’une stratégie désactivée, car ces stratégies ne sont pas évaluées.   



## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Si vous souhaitez migrer des stratégies classiques, consultez [Migrer les stratégies classiques dans le portail Azure](active-directory-conditional-access-migration.md)  
