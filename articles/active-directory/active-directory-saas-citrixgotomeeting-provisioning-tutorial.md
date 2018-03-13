---
title: "Didacticiel : configurer GoToMeeting pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et GoToMeeting."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 48b8072b5ebe61f5a9dccd9d8ea31e5a6945f265
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Didacticiel : configurer GoToMeeting pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans GoToMeeting et Azure AD pour provisionner automatiquement des comptes d’utilisateur Azure AD dans GoToMeeting, ainsi que pour annuler automatiquement ce provisionnement.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Un abonnement GoToMeeting pour lequel l’authentification unique est activée.
*   Un compte d’utilisateur GoToMeeting avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-gotomeeting"></a>Attribution d’utilisateurs à GoToMeeting

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service de provisionnement, vous devez déterminer quels utilisateurs et/ou groupes Azure AD ont besoin d’accéder à votre application GoToMeeting. Une fois que vous avez choisi, vous pouvez attribuer ces utilisateurs à votre application GoToMeeting en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Conseils importants pour l’attribution d’utilisateurs à GoToMeeting

*   Dans un premier temps, il est recommandé de n’assigner qu’un seul utilisateur Azure AD à GoToMeeting pour tester la configuration de l’attribution. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous attribuez un utilisateur à GoToMeeting, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section explique comment connecter Azure AD à l’API de provisionnement de comptes d’utilisateur de GoToMeeting pour créer, mettre à jour et désactiver les comptes d’utilisateur attribués dans GoToMeeting, en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique SAML pour GoToMeeting en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning"></a>Pour configurer l’approvisionnement automatique des comptes d’utilisateur :

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré GoToMeeting pour l’authentification unique, recherchez votre instance de GoToMeeting à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter**, puis recherchez **GoToMeeting** dans la galerie d’applications. Sélectionnez GoToMeeting dans les résultats de la recherche, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de GoToMeeting, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. Dans la section Informations d’identification de l’administrateur, effectuez les étapes suivantes :
   
    a. Dans la zone de texte **Nom d’utilisateur admin GoToMeeting**, tapez le nom d’utilisateur d’un administrateur.

    b. Dans la zone de texte **Mot de passe de l’admin GoToMeeting**, tapez le mot de passe de l’administrateur.

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application GoToMeeting. Si la connexion échoue, vérifiez que votre compte GoToMeeting dispose des autorisations d’administrateur d’équipe, puis revenez à l’étape **Informations d’identification de l’administrateur**.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case.

8. Cliquez sur **Enregistrer.**

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec GoToMeeting**.

10. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et GoToMeeting. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour établir une correspondance avec les comptes d’utilisateur GoToMeeting en vue de mises à jour ultérieures. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service de provisionnement Azure AD pour GoToMeeting, définissez **État de l’approvisionnement** sur **Activé** dans la section Paramètres.

12. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes attribués à GoToMeeting dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application GoToMeeting.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


