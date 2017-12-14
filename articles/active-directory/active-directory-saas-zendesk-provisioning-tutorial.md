---
title: "Didacticiel : Configuration de ZenDesk pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur sur ZenDesk."
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
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.openlocfilehash: d9dcc7f81b32498802d52e864c834d785386985c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configuring-zendesk-for-automatic-user-provisioning"></a>Didacticiel : Configuration de ZenDesk pour l’approvisionnement automatique d’utilisateurs


L’objectif de ce didacticiel est de vous montrer les étapes à effectuer dans ZenDesk et Azure AD pour approvisionner et retirer automatiquement des comptes d’utilisateur d’Azure AD vers ZenDesk. 

## <a name="prerequisites"></a>Composants requis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory
*   Un locataire ZenDesk avec le [plan Entreprise](https://www.zendesk.com/product/pricing/) ou mieux activé 
*   Un compte d’utilisateur dans ZenDesk avec des autorisations d’administrateur 

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’[API REST ZenDesk](https://developer.zendesk.com/rest_api/docs/core/introduction#the-api), qui est disponible pour les équipes ZenDesk disposant du forfait Essentiel ou mieux.

## <a name="assigning-users-to-zendesk"></a>Affectation d’utilisateurs à ZenDesk

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application ZenDesk. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application ZenDesk en suivant les instructions fournies ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Conseils importants pour l’affectation d’utilisateurs à ZenDesk

*   Nous vous recommandons de n’assigner qu’un seul utilisateur Azure AD à ZenDesk afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous affectez un utilisateur à ZenDesk, vous devez sélectionner le rôle **utilisateur** ou un autre rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement, et ces utilisateurs sont ignorés.

> [!NOTE]
> En guise de fonctionnalité ajoutée, le service d’approvisionnement lit les rôles personnalisés définis dans Zendesk et les importe dans Azure AD où vous pouvez les sélectionner dans la boîte de dialogue Select Role. Ces rôles sont visibles dans le portail Azure une fois que le service d’approvisionnement a été activé et qu’un cycle de synchronisation s’est achevé.

## <a name="configuring-user-provisioning-to-zendesk"></a>Configuration de l’approvisionnement des utilisateurs vers ZenDesk 

Cette section explique comment connecter votre annuaire Azure AD à l’API d’approvisionnement de comptes d’utilisateur de ZenDesk pour créer, mettre à jour et désactiver les comptes d’utilisateur affectés dans ZenDesk en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP] 
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour ZenDesk en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.


### <a name="configure-automatic-user-account-provisioning-to-zendesk-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes d’utilisateur vers ZenDesk dans Azure AD


1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré ZenDesk pour l’authentification unique, recherchez votre instance de ZenDesk à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **ZenDesk** dans la galerie d’applications. Sélectionnez ZenDesk dans les résultats de recherche et ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de ZenDesk, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de ZenDesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. Dans la section **Informations d’identification administrateur**, entrez le **Admin Username&clé_jeton&Domain** généré par votre compte ZenDesk (vous pouvez trouver le jeton sous votre compte : **Admin** > **API** > **Settings**). 

    ![Approvisionnement de ZenDesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application ZenDesk. Si la connexion échoue, vérifiez que votre compte ZenDesk dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case « Envoyer une notification par e-mail en cas de défaillance ».

8. Cliquez sur **Enregistrer**. 

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ZenDesk**.

10. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ZenDesk. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ZenDesk pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour ZenDesk, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section **Paramètres**.

12. Cliquez sur **Enregistrer**. 

Cette opération démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à ZenDesk dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 20 minutes tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)
