---
title: "Didacticiel : configurer Jive pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 4190b1cbc7e32b1471795e29a211f7a7e5831648
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Didacticiel : configurer Jive pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans Jive et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers Jive.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Un abonnement Jive pour lequel l’authentification unique est activée.
*   Un compte utilisateur dans Jive avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-jive"></a>Affectation d’utilisateurs à Jive

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Jive. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application Jive en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Conseils importants pour l’affectation d’utilisateurs à Jive

*   Il est recommandé qu’un seul utilisateur Azure AD soit affecté à Jive pour tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous affectez un utilisateur à Jive, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-user-provisioning"></a>Activer l’approvisionnement des utilisateurs

Cette section vous guide lors de la connexion de votre instance Azure AD au compte utilisateur Jive fournissant l’API et la configuration du service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Jive en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Jive en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-user-account-provisioning"></a>Pour configurer l’approvisionnement de comptes d’utilisateur

Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Jive.
Dans le cadre de cette procédure, vous devez fournir un jeton de sécurité à demander sur Jive.com.

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Jive pour l’authentification unique, recherchez votre instance Jive à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Jive** dans la galerie d’applications. Sélectionnez Jive dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance Jive, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/active-directory-saas-jive-provisioning-tutorial/provisioning.png)

5. Dans la section **Informations d’identification de l’administrateur**, fournissez les paramètres de configuration suivants :
   
    a. Dans la zone de texte **Nom d’utilisateur admin Jive**, tapez le nom d’un compte Jive auquel le profil **System Administrator** est attribué dans Jive.com.
   
    b. Dans la zone de texte **Mot de passe de l’admin Jive** , tapez le mot de passe de ce compte.
   
    c. Dans la zone de texte **URL de locataire Jive** , tapez l’URL de locataire Jive.
      
      > [!NOTE]
      > L’URL de locataire Jive est celle utilisée par votre organisation pour se connecter à Jive.  
      > En règle générale, l’URL a le format suivant : **www.\<organisation\>.jive.com**.          

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Jive.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case se trouvant en dessous.

8. Cliquez sur **Enregistrer.**

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Jive**.

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Jive. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans Jive pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour Jive, définissez le paramètre **État d’approvisionnement** sur **Activé** dans la section Paramètres.

12. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à Jive dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application Jive.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-jive-tutorial.md)