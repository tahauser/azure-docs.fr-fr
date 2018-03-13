---
title: "Didacticiel : configurer DocuSign pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 921382a727816b6b0b58d185608a7b6a01dae752
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Didacticiel : configurer DocuSign pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans DocuSign et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers DocuSign.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Un abonnement DocuSign pour lequel l’authentification unique est activée.
*   Un compte d’utilisateur dans DocuSign avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-docusign"></a>Affectation d’utilisateurs à DocuSign

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs et les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application DocuSign. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs à votre application DocuSign en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Conseils importants pour l’affectation d’utilisateurs à DocuSign

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à DocuSign afin de tester la configuration de l’approvisionnement. Les autres utilisateurs peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à DocuSign, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

> [!NOTE]
> Azure AD ne prend pas en charge le provisionnement de groupes avec l’application Docusign, seuls les utilisateurs peuvent être provisionnés.

## <a name="enable-user-provisioning"></a>Activer l’approvisionnement des utilisateurs

Cette section explique comment connecter votre annuaire Azure AD à l’API d’approvisionnement de comptes d’utilisateur de DocuSign pour créer, mettre à jour et désactiver les comptes d’utilisateur affectés dans DocuSign en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!Tip]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour DocuSign en suivant les instructions fournies sur le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-user-account-provisioning"></a>Pour configurer l’approvisionnement de comptes d’utilisateur :

L’objectif de cette section est d’expliquer comment activer l’approvisionnement utilisateur des comptes d’utilisateurs Active Directory sur DocuSign.

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré DocuSign pour l’authentification unique, recherchez votre instance de DocuSign à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **DocuSign** dans la galerie d’applications. Sélectionnez DocuSign dans les résultats de recherche et ajoutez-la à votre liste d’applications.

3. Sélectionnez votre instance de DocuSign, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/active-directory-saas-docusign-provisioning-tutorial/provisioning.png)

5. Dans la section **Informations d’identification de l’administrateur**, fournissez les paramètres de configuration suivants :
   
    a. Dans la zone de texte **Nom d’utilisateur Admin**, tapez le nom d’un compte DocuSign auquel le profil **System Administrator** (Administrateur système) est affecté dans DocuSign.com.
   
    b. Dans la zone de texte **Mot de passe d’administrateur**, entrez le mot de passe de ce compte.

6. Sur le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application DocuSign.

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case.

8. Cliquez sur **Enregistrer.**

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec DocuSign**.

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers DocuSign. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans DocuSign pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour DocuSign, affectez au paramètre **Statut d’approvisionnement** la valeur **Activé** dans la section Paramètres.

12. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs affectés à DocuSign dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application DocuSign.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-docusign-tutorial.md)