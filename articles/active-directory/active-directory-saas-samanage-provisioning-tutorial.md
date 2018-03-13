---
title: "Didacticiel : configurer Samanage pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Samanage."
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 901f0ec7ceeb80687b7b75d9a3710e8d6bc1811c
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Didacticiel : configurer Samanage pour l’approvisionnement automatique d’utilisateurs


L’objectif de ce didacticiel est de vous montrer la procédure à suivre dans Samanage et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur Azure AD vers Samanage. 

## <a name="prerequisites"></a>configuration requise

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory
*   Un locataire Samanage avec le [plan professionnel](https://www.samanage.com/pricing/) ou mieux activé 
*   Un compte d’utilisateur dans Samanage avec des autorisations d’administrateur 

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’[API REST Samanage](https://www.samanage.com/api/), qui est disponible pour les équipes Samanage disposant du plan professionnel ou mieux.

## <a name="assigning-users-to-samanage"></a>Affectation d’utilisateurs à Samanage

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Samanage. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application Samanage en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Conseils importants pour l’affectation d’utilisateurs à Samanage

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à Samanage afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous affectez un utilisateur à Samanage, vous devez sélectionner le rôle **utilisateur** ou un autre rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement et ces utilisateurs sont ignorés.

> [!NOTE]
> En guise de fonctionnalité ajoutée, le service d’approvisionnement lit les rôles personnalisés définis dans Samanage et les importe dans Azure AD où vous pouvez les sélectionner dans la boîte de dialogue Select Role. Ces rôles sont visibles dans le portail Azure une fois que le service d’approvisionnement a été activé et qu’un cycle de synchronisation s’est achevé.

## <a name="configuring-user-provisioning-to-samanage"></a>Configuration de l'approvisionnement des utilisateurs sur Samanage 

Cette section vous guide à travers la connexion de votre Azure AD au compte d’utilisateur de Samanage fournissant l’API et la configuration du service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans Samanage en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Samanage, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.


### <a name="configure-automatic-user-account-provisioning-to-samanage-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes utilisateur vers Samanage dans Azure AD :


1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Samanage pour l’authentification unique, recherchez votre instance de Samanage à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et effectuer une recherche pour **Samanage** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez Samanage, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de Samanage, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d'utilisateur de l'administrateur et le Mot de passe d'administrateur** de votre compte Samanage. 

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application Samanage. Si la connexion échoue, vérifiez que votre compte Samanage dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Entrez l’adresse de courrier d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case « Envoyer une notification par e-mail en cas de défaillance ».

8. Cliquez sur **Enregistrer**. 

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Samanage**.

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Samanage. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Samanage pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour Samanage, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

12. Cliquez sur **Enregistrer**. 

Cette opération démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à Samanage dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes, tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)
