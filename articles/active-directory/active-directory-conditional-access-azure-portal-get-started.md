---
title: "Bien démarrer avec l’accès conditionnel dans Azure Active Directory | Microsoft Docs"
description: "Testez l’accès conditionnel à l’aide d’une condition d’emplacement."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c780efe92ec69ff692b42a586f55a1036d2ec952
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Bien démarrer avec l’accès conditionnel dans Azure Active Directory

L’accès conditionnel est une fonctionnalité d’Azure Active Directory, qui vous permet de définir les conditions dans lesquelles les utilisateurs autorisés peuvent accéder à vos applications. 

Cette rubrique fournit des instructions pour tester un accès conditionnel en fonction d’une condition d’emplacement dans votre environnement.  


## <a name="scenario-description"></a>Description du scénario

De nombreuses organisations veulent limiter l’utilisation de l’authentification multifacteur aux tentatives d’accès aux applications, qui n’émanent pas de l’intranet de l’entreprise. Azure Active Directory vous permet d’atteindre cet objectif facilement grâce à une stratégie d’accès conditionnel basée sur l’emplacement. Cette rubrique fournit des instructions détaillées pour configurer une stratégie de ce type. Celle-ci utilise des [adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) pour faire la distinction entre les tentatives d’accès provenant de l’intranet de l’entreprise et celles provenant de tous les autres emplacements.


## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans cette rubrique suppose que vous connaissiez les concepts abordés dans [Accès conditionnel Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Pour tester ce scénario, vous allez :

- Créer un utilisateur de test 

- Affecter une licence Azure AD Premium à cet utilisateur

- Configurer une application gérée et lui affecter votre utilisateur de test

- Configurer les adresses IP approuvées

Pour plus d’informations sur les adresses IP approuvées, consultez [Adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Configuration de la stratégie

**Pour configurer votre stratégie d’accès conditionnel, procédez comme suit :**

1. Dans la barre de navigation gauche du portail Azure, cliquez sur **Azure Active Directory**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. Dans le panneau **Azure Active Directory**, sous la section **Gérer**, cliquez sur **Accès conditionnel**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Dans le panneau **Accès conditionnel**, pour ouvrir le panneau **Nouveau**, cliquez sur **Ajouter** dans la barre d’outils située en haut.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Dans le champ **Nom** du panneau **Nouveau**, indiquez le nom de votre stratégie.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. Dans la section **Affectation**, cliquez sur **Utilisateurs et groupes**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Dans le panneau **Utilisateurs et groupes**, procédez comme suit :

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Cliquez sur **Sélectionner des utilisateurs et des groupes**.

    b. Cliquez sur **Sélectionner**.

    c. Dans le panneau **Sélectionner**, sélectionnez votre utilisateur de test, puis cliquez sur **Sélectionner**.

    d. Dans le panneau **Utilisateurs et groupes**, cliquez sur **Terminé**.

7. Dans le panneau **Nouveau**, pour ouvrir le panneau **Applications cloud**, cliquez sur **Applications cloud** dans la section **Affectation**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Dans le panneau **Applications cloud**, procédez comme suit :

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Cliquez sur **Sélectionner les applications**.

    b. Cliquez sur **Sélectionner**.

    c. Dans le panneau **Sélectionner**, sélectionnez votre application cloud, puis cliquez sur **Sélectionner**.

    d. Dans le panneau **Applications cloud**, cliquez sur **Terminé**.

9. Dans le panneau **Nouveau**, pour ouvrir le panneau **Conditions**, cliquez sur **Conditions** dans la section **Affectation**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. Dans le panneau **Conditions**, pour ouvrir le panneau **Emplacements**, cliquez sur **Emplacements**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Dans le panneau **Emplacements**, procédez comme suit :

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. Sous **Configurer**, cliquez sur **Oui**.

    b. Sous **Inclure**, cliquez sur **All locations (Tous les emplacements)**.

    c. Cliquez sur **Exclure**, puis sur **All trusted IPs (Toutes les adresses IP approuvées)**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Cliquez sur **Done**.

12. Dans le panneau **Conditions**, cliquez sur **Terminé**.

13. Dans le panneau **Nouveau**, pour ouvrir le panneau **Octroyer**, cliquez sur **Octroyer** dans la section **Contrôles**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Dans le panneau **Grant (Autoriser)**, procédez comme suit :

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Sélectionnez **Exiger une authentification multifacteur**.

    b. Cliquez sur **Sélectionner**.

15. Dans le panneau **Nouveau**, sous **Activer la stratégie**, cliquez sur **Activé**.

    ![Accès conditionnel](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Dans le panneau **Nouveau**, cliquez sur **Créer**.


## <a name="testing-the-policy"></a>Test de la stratégie

Pour tester votre stratégie, vous devez accéder à votre application à partir d’un appareil : 

1. dont l’adresse IP figure parmi les adresses IP approuvées que vous avez configurées ; 

1. dont l’adresse IP ne figure pas parmi les adresses IP approuvées que vous avez configurées.

L’authentification multifacteur ne doit être requise que si la tentative de connexion émane d’un appareil qui ne figure pas parmi les adresses IP approuvées que vous avez configurées. 


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’accès conditionnel, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access-azure-portal.md).

