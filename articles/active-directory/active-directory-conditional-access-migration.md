---
title: "Migrer les stratégies classiques dans le portail Azure | Microsoft Docs"
description: "Découvrez ce que vous devez savoir pour migrer les stratégies classiques dans le portail Azure."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrer les stratégies classiques dans le portail Azure 


[L’accès conditionnel](active-directory-conditional-access-azure-portal.md) est une fonctionnalité d’Azure Active Directory (Azure AD) qui vous permet de contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Si l’objectif est toujours le même, la publication du nouveau portail Azure a introduit des améliorations significatives pour le fonctionnement de l’accès conditionnel.

Envisagez de migrer les stratégies que vous n’avez pas créées dans le portail Azure, car :

- Vous pouvez maintenant résoudre des scénarios que vous ne pouviez pas gérer auparavant.

- Vous pouvez réduire le nombre de stratégies que vous devez gérer en les consolidant.   

- Vous pouvez gérer toutes vos stratégies d’accès conditionnel dans un emplacement central.

- Le portail Azure Classic va être mis hors service.   

Cet article explique ce que vous devez savoir pour migrer vos stratégies d’accès conditionnel existantes vers le nouveau framework.
 
## <a name="classic-policies"></a>Stratégies classiques

Dans le [portail Azure](https://portal.azure.com), la page [Accès conditionnel - Stratégies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) est votre point d’entrée aux stratégies d’accès conditionnel. Toutefois, dans votre environnement, vous pouvez également disposer de stratégies d’accès conditionnel que vous n’avez pas créées à l’aide de cette page. Ces stratégies sont appelées *stratégies classiques*. Les stratégies classiques sont des stratégies d’accès conditionnel, que vous avez créées dans :

- Le Portail Azure Classic
- Le Portail classique Intune
- Le portail Intune App Protection


Dans la page **Accès conditionnel**, vous pouvez accéder à vos stratégies classiques en cliquant sur [**Stratégies classiques (préversion)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) dans la section **Gérer**. 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


L’affichage **Stratégies classiques** vous permet d’effectuer les opérations suivantes :

- Filtrer vos stratégies classiques.
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- Désactiver des stratégies classiques.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- Passer en revue les paramètres d’une stratégie classique (et la désactiver).

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


Si vous désactivez une stratégie classique, vous ne pouvez plus annuler cette étape. C’est pourquoi vous pouvez modifier l’appartenance de groupe dans une stratégie classique à l’aide de l’affichage **Détails**. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

En modifiant les groupes sélectionnés ou en excluant des groupes spécifiques, vous pouvez tester l’effet d’une stratégie classique désactivée pour quelques utilisateurs de test avant de désactiver la stratégie pour tous les utilisateurs et groupes inclus. 



## <a name="azure-ad-conditional-access-policies"></a>Stratégies d’accès conditionnel Azure AD

Grâce à l’accès conditionnel dans le portail Azure, vous pouvez gérer toutes vos stratégies dans un emplacement central. Étant donné que l’implémentation de l’accès conditionnel a été considérablement modifiée, vous devez vous familiariser avec les concepts de base avant de migrer vos stratégies classiques.

Consultez l'article :

- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md) pour en savoir plus sur les concepts de base et la terminologie.

- [Bonnes pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md) pour obtenir des conseils sur le déploiement de l’accès conditionnel dans votre organisation.

- [Bien démarrer avec l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) pour vous familiariser avec l’interface utilisateur dans le portail Azure.


 
## <a name="migration-considerations"></a>Considérations relatives à la migration

Dans cet article, les stratégies d’accès conditionnel Azure AD sont également appelées *nouvelles stratégies*.
Vos stratégies classiques continuent de fonctionner aux côtés de vos nouvelles stratégies jusqu’à ce que vous les désactiviez ou supprimiez. 

Les aspects suivants sont importants dans le contexte d’une consolidation de stratégies :

- Alors que les stratégies classiques sont liées à une application cloud spécifique, vous pouvez sélectionner autant d’applications cloud que nécessaire dans une nouvelle stratégie.

- Tous les contrôles d’une stratégie classique et d’une nouvelle stratégie pour une application cloud doivent être satisfaits (*AND*). 


- Dans une nouvelle stratégie, vous pouvez :
 
    - Combiner plusieurs conditions si votre scénario l’exige. 

    - Sélectionner plusieurs exigences d’octroi en tant que contrôle d’accès et les combiner avec un opérateur logique *OR* (nécessitent un des contrôles sélectionnés) ou avec un opérateur logique *AND* (nécessitent tous les contrôles sélectionnés).

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Si vous souhaitez migrer des stratégies classiques pour **Office 365 Exchange Online** qui incluent **Exchange Active Sync** comme condition d’applications clientes, vous ne pourrez peut-être pas les consolider en une nouvelle stratégie. 

C’est, par exemple, le cas si vous souhaitez prendre en charge tous les types d’applications clientes. Dans une nouvelle stratégie qui a **Exchange Active Sync** comme condition d’applications clientes, vous ne pouvez pas sélectionner d’autres applications clientes.

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

En outre, une consolidation en une nouvelle stratégie n’est pas possible si vos stratégies classiques contiennent plusieurs conditions. Une nouvelle stratégie pour laquelle **Exchange Active Sync** est configuré comme condition d’applications clientes ne prend pas en charge d’autres conditions :   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

Si vous avez une nouvelle stratégie pour laquelle **Exchange Active Sync** est configuré comme condition d’applications clientes, vous devez vous assurer que toutes les autres conditions ne sont pas configurées. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

Les stratégies classiques [basées sur une application](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) pour Office 365 Exchange Online qui incluent **Exchange Active Sync** comme condition d’applications clientes autorisent les [plateformes d’appareils](active-directory-conditional-access-technical-reference.md#device-platform-condition) **prises en charge** et **non prises en charge**. Bien que vous ne puissiez pas configurer de plateformes d’appareils individuelles dans une nouvelle stratégie connexe, vous pouvez limiter la prise en charge aux [plateformes d’appareils prises en charge](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

Vous pouvez consolider plusieurs stratégies classiques qui incluent **Exchange Active Sync** comme condition d’applications clientes si elles ont :

- Uniquement **Exchange Active Sync** comme condition 

- Plusieurs exigences d’octroi d’accès configurées

Un scénario courant est la consolidation des éléments suivants :

- Une stratégie classique basée sur un appareil à partir du portail Azure Classic 
- Une stratégie classique basée sur une application dans le portail Intune App Protection 
 
Dans ce cas, vous pouvez consolider vos stratégies classiques en une nouvelle stratégie pour laquelle les deux exigences sont sélectionnées.

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>Plateformes d’appareils

Les stratégies classiques dotées de [contrôles basés sur une application](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) sont préconfigurées avec iOS et Android en tant que [condition de plateforme d’appareil](active-directory-conditional-access-technical-reference.md#device-platform-condition). 

Dans une nouvelle stratégie, vous devez sélectionner les [plateformes d’appareils](active-directory-conditional-access-technical-reference.md#device-platform-condition) à prendre en charge individuellement.

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
