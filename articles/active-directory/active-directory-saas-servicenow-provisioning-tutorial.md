---
title: "Didacticiel : configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment approvisionner et annuler l’approvisionnement automatiquement des comptes utilisateur d’Azure AD vers ServiceNow."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 50d5ecd0542d236d4d68656af7808c329728aa39
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Didacticiel : configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory

L’objectif de ce didacticiel est de vous montrer la procédure à suivre dans ServiceNow et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur Azure AD vers ServiceNow.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Vous devez posséder un abonné valide pour les éditions ServiceNow for Work ou ServiceNow for Education. Vous pouvez utiliser un compte d’essai gratuit pour chaque service.
*   Un compte d’utilisateur dans ServiceNow avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-servicenow"></a>Affectation d’utilisateurs à ServiceNow

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application ServiceNow. Une fois choisi, vous pouvez attribuer ces utilisateurs à votre application ServiceNow en suivant les instructions fournies ici : [Attribuer un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à ServiceNow afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.
>*   Lorsque vous assignez un utilisateur à ServiceNow, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide à travers la connexion de votre Azure AD au compte d’utilisateur de ServiceNow fournissant l’API et la configuration du service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans ServiceNow en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour ServiceNow, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré ServiceNow pour l’authentification unique, recherchez votre instance de ServiceNow à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et effectuer une recherche pour **ServiceNow** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez ServiceNow, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de ServiceNow, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/active-directory-saas-servicenow-provisioning-tutorial/provisioning.png)

5. Dans la section Informations d’identification de l’administrateur, effectuez les étapes suivantes :
   
    a. Dans la zone de texte **Nom de l'Instance ServiceNow** , tapez le nom d'instance ServiceNow.

    b. Dans la zone de texte **Nom d’utilisateur admin ServiceNow** , tapez le nom d’utilisateur d’un administrateur.

    c. Dans la zone de texte **Mot de passe de l’admin ServiceNow** , tapez le mot de passe de l’administrateur.

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application ServiceNow. Si la connexion échoue, vérifiez que votre compte ServiceNow dispose des autorisations d’administrateur d’équipe, puis revenez à l’étape **Informations d’identification de l’administrateur**.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case.

8. Cliquez sur **Enregistrer.**

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ServiceNow**.

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ServiceNow. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ServiceNow pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour ServiceNow, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section Paramètres.

12. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à ServiceNow dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application ServiceNow.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-servicenow-tutorial.md)


