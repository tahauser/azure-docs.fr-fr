---
title: "Conception d’identités hybrides : tâches de gestion Azure | Microsoft Docs"
description: "Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: ed683f1b96172b71cc3dfae2511607e50071739f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planifier le cycle de vie des identités hybrides
L'identité est un des fondements de la stratégie de mobilité et d'accès aux applications de votre entreprise. Que vous vous connectiez à votre appareil mobile ou à une application SaaS, votre identité est la clé d'accès à tous les éléments. À son niveau le plus élevé, une solution de gestion des identités englobe l'unification et la synchronisation entre vos référentiels d'identités, ce qui inclut l'automatisation et la centralisation du processus d’approvisionnement des ressources. La solution d'identité doit être une identité centralisée en local et dans le cloud et, également, utiliser une forme de fédération d'identité, afin de gérer une authentification centralisée et partager ainsi que collaborer de manière sécurisée avec des utilisateurs et entreprises externes. Les ressources vont de systèmes d'exploitation et d’applications à des personnes dans ou affiliés à une organisation. La structure organisationnelle peut être modifiée pour prendre en compte les stratégies et les procédures d’approvisionnement.

Il est également important de disposer d'une solution d'identité conçue pour responsabiliser les utilisateurs en leur fournissant des expériences de libre-service pour qu’ils restent productifs. Votre solution d'identité est plus robuste si elle permet une authentification unique des utilisateurs pour toutes les ressources dont ils ont besoin. Les administrateurs d'accès à tous les niveaux peuvent utiliser des procédures standardisées pour la gestion des informations d'identification des utilisateurs. Certains niveaux d'administration peuvent être réduits ou éliminés, en fonction de l'étendue de la solution de gestion de l'approvisionnement. En outre, vous pouvez en toute sécurité distribuer les capacités d'administration, manuellement ou automatiquement, entre différentes organisations. Par exemple, un administrateur de domaine peut servir uniquement les personnes et les ressources de ce domaine. Cet utilisateur peut exécuter des tâches d'administration et d'approvisionnement, mais il n'est pas autorisé à effectuer des tâches de configuration, telles que la création de flux de travail.

## <a name="determine-hybrid-identity-management-tasks"></a>Déterminer les tâches de gestion des identités hybrides
La distribution de tâches administratives dans votre organisation améliore la précision et l'efficacité de l'administration, ainsi que l'équilibre de la charge de travail de l'organisation. Voici les tableaux croisés dynamiques qui définissent un système de gestion d'identité robuste.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Pour définir les tâches de gestion d'identité hybride, vous devez comprendre certaines caractéristiques essentielles de l'organisation qui va adopter l'identité hybride. Il est important de comprendre les référentiels actuels utilisés pour les sources d'identité. En connaissant ces principaux éléments, vous aurez les exigences fondamentales et, sur cette base, vous devrez poser des questions plus précises qui vous permettront de prendre une meilleure décision de conception pour votre solution d'identité.  

Lors de la définition de ces exigences, assurez-vous qu'au moins les questions suivantes soient traitées.

* Options d’approvisionnement : 
  
  * La solution d'identité hybride prend-elle en charge un système robuste de gestion des accès et de l'approvisionnement pour les comptes ?
  * Comment les utilisateurs, les groupes et les mots de passe seront-ils gérés ?
  * La gestion du cycle de vie des identités est-elle réactive ? 
    * Combien de temps prend la suspension du compte de mises à jour du mot de passe ?
* Gestion des licences : 
  
  * La solution d'identité hybride gère-t-elle les licences ?
    * Si oui, quelles sont les fonctionnalités disponibles ?
* La solution gère-t-elle des licences basées sur des groupes ? 
  
      - Si oui, est-il possible de lui affecter un groupe de sécurité ? 
       - Si oui, l'annuaire de cloud affecte-t-il automatiquement des licences à tous les membres du groupe ? 
        - Que se passera-t-il si un utilisateur est ultérieurement ajouté au groupe ou supprimé ? Une licence sera-t-elle automatiquement affectée ou supprimée selon le cas ? 
* Intégration avec des fournisseurs d'identité tiers :
* Cette solution hybride peut-elle être intégrée avec des fournisseurs d'identité tiers pour implémenter l'authentification unique ?
* Est-il possible d'unifier tous les fournisseurs d'identité dans un système d'identité cohérent ?
* Si oui, comment, qui sont-ils et quelles fonctionnalités sont disponibles ?

## <a name="synchronization-management"></a>Gestion de la synchronisation
Un des objectifs d'un gestionnaire d'identités pour être en mesure de mettre tous les fournisseurs d'identité et de les conserver synchronisés. Vous conservez les données synchronisées sur la base d'un fournisseur d'identité principale faisant autorité. Dans un scénario d'identité hybride, avec un modèle de gestion synchronisée vous gérez toutes les identités d'utilisateurs et d'appareils sur un serveur local et synchronisez les comptes et, éventuellement, les mots de passe pour le cloud. L'utilisateur entre le même mot de passe, en local comme dans le cloud et, lors de la connexion, le mot de passe est vérifié par la solution d'identité. Ce modèle utilise un outil de synchronisation d'annuaire.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Pour une conception appropriée la synchronisation de votre solution d'identité hybride, vérifiez que les questions suivantes sont traitées: • Quelles sont les solutions de synchronisation disponibles pour la solution d'identité hybride ?
• Quelles sont les fonctionnalités d'authentification unique disponibles ?
•    Quelles sont les options pour la fédération d'identité entre B2B et B2C ?

## <a name="next-steps"></a>Étapes suivantes
[Déterminer la stratégie d’adoption de la gestion des identités hybrides](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Voir aussi
[Présentation des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

