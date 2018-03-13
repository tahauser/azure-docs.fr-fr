---
title: "Personnalisation des mappages d’attributs Azure AD | Microsoft Docs"
description: "Découvrez ce que sont les mappages d’attributs pour les applications SaaS dans Azure Active Directory et comment les modifier pour répondre aux besoins de votre entreprise."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed3558fd4ca0022389091edad9caa7686a5116a0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory
Microsoft Azure AD prend en charge l’approvisionnement d’utilisateurs pour les applications SaaS tierces telles que Salesforce, Google Apps et autres. Si vous avez activé l’approvisionnement d’utilisateurs pour une application SaaS tierce, le portail de gestion Azure contrôle ses valeurs d’attributs sous forme d’une configuration appelée « mappage d’attributs ».

Il existe un ensemble préconfiguré de mappages d’attributs entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. <br> 
 Vous pouvez personnaliser les mappages d’attributs par défaut en fonction des besoins de votre entreprise. Cela signifie que vous pouvez modifier ou supprimer des mappages d’attributs existants ou en créer de nouveaux.

Dans le portail Azure AD, vous pouvez accéder à cette fonctionnalité en cliquant sur une configuration **Mappages** sous **Approvisionnement**, dans la section **Gérer** d’une **Application d’entreprise**.


![Salesforce][5] 

Cliquer sur une configuration **Mappages** permet d’ouvrir le panneau **Mappage d’attributs**.  
Des applications SaaS nécessitent certains mappages d’attributs pour fonctionner correctement. Pour les attributs requis, la fonctionnalité **Supprimer** n’est pas disponible.


![Salesforce][6]  

Dans l’exemple ci-dessus, vous pouvez voir que l’attribut **Nom d’utilisateur** d’un objet géré dans Salesforce est renseigné avec la valeur **userPrincipalName** de l’objet Azure Active Directory lié.

Vous pouvez personnaliser des **mappages d’attributs** existants en cliquant sur un mappage. Cette opération ouvre le panneau **Modifier l’attribut**.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Présentation des types de mappage d’attributs
Avec les mappages d’attributs, vous contrôlez la façon dont les attributs sont renseignés dans une application SaaS tierce. Quatre différents types de mappages sont pris en charge :

* **Direct** : l’attribut cible est renseigné avec la valeur d’un attribut de l’objet lié dans Azure AD.
* **Constant** : l’attribut cible est renseigné avec une chaîne spécifique que vous avez spécifiée.
* **Expression** : l’attribut cible est renseigné en fonction du résultat d’une expression semblable à un script. 
  Pour plus d’informations, consultez l’article [Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Aucun** : l’attribut cible reste inchangé. Toutefois, si l’attribut cible est vide, il est renseigné avec la valeur par défaut que vous spécifiez.

Outre ces quatre types de mappage d’attributs de base, les mappages d’attributs personnalisés prennent en charge le concept d’affectation de valeur **par défaut** facultative. L’affectation de valeur par défaut garantit qu’un attribut cible est renseigné avec une valeur s’il n’existe aucune valeur ni dans Azure AD, ni sur l’objet cible. La configuration la plus courante consiste à laisser ce champ vide.


## <a name="understanding-attribute-mapping-properties"></a>Présentation des propriétés de mappage d’attributs

La section précédente vous a présenté la propriété de type de mappage attributs.
Outre cette propriété, les mappages d’attributs prennent en charge les attributs suivants :

- **Attribut source** : attribut utilisateur du système source (par exemple, Azure Active Directory).
- **Attribut cible** : attribut utilisateur dans le système cible (par exemple, ServiceNow).
- **Trouver les objets utilisant cet attribut** : indique si ce mappage doit être utilisé ou pas pour identifier les utilisateurs de manière unique entre les systèmes source et cible. Ce champ est généralement défini sur l’attribut userPrincipalName ou mail dans Azure AD, qui est généralement mappé à un champ de nom d’utilisateur dans une application cible.
- **Priorité de correspondance** : vous pouvez définir plusieurs attributs de correspondance. S’il en existe plusieurs, ils sont évalués dans l’ordre défini par ce champ. Dès qu’une correspondance est trouvée, aucun autre attribut correspondant n’est évalué.
- **Appliquer ce mappage**
    - **Toujours** : applique ce mappage à la création de l’utilisateur et des actions de mise à jour.
    - **Lors de la création uniquement** : applique ce mappage uniquement aux actions de création d’utilisateur.


## <a name="what-you-should-know"></a>Ce que vous devez savoir

Microsoft Azure AD fournit une implémentation efficace d’un processus de synchronisation. Dans un environnement initialisé, seuls les objets nécessitant des mises à jour sont traités pendant un cycle de synchronisation. La mise à jour des mappages d’attributs a un impact sur les performances d’un cycle de synchronisation. Une mise à jour de la configuration des mappages d’attributs nécessite une réévaluation de tous les objets gérés. Il est recommandé de limiter au minimum le nombre de modifications consécutives de vos mappages d’attributs.

## <a name="next-steps"></a>Étapes suivantes

* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatiser l’approvisionnement/annuler l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

