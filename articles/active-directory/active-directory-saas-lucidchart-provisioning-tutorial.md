---
title: "Didacticiel : configurer LucidChart pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur LucidChart."
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
ms.openlocfilehash: 7d0855a7040af595744dbd3977f6a969abb78017
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Didacticiel : configurer LucidChart pour l’approvisionnement automatique d’utilisateurs


L’objectif de ce didacticiel est de vous montrer la procédure à suivre dans LucidChart et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur Azure AD sur LucidChart. 

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory
*   Un locataire LucidChart avec le [plan d’entreprise](https://www.lucidchart.com/user/117598685#/subscriptionLevel) ou supérieur activé 
*   Un compte utilisateur dans LucidChart avec des autorisations d’administrateur 

## <a name="assigning-users-to-lucidchart"></a>Affectation d’utilisateurs à LucidChart

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application LucidChart. Dès que vous avez fait votre choix, vous pouvez assigner ces utilisateurs à votre application LucidChart en suivant les instructions disponibles à la page suivante :

[Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Conseils importants concernant l’assignation d’utilisateurs à LucidChart

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à LucidChart afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Lorsque vous affectez un utilisateur à LucidChart, vous devez sélectionner le rôle **utilisateur** ou un autre rôle valide spécifique de l’application (si disponible) dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement, et ces utilisateurs sont ignorés.


## <a name="configuring-user-provisioning-to-lucidchart"></a>Configuration de l’approvisionnement des utilisateurs sur LucidChart 

Cette section vous guide afin de connecter votre instance Azure AD à votre compte utilisateur LucidChart qui fournit l’API. À l’aide de ce guide, découvrez aussi comment configurer le service d’approvisionnement afin de créer, mettre à jour et désactiver des comptes utilisateur assignés dans LucidChart, en fonction des attributions d’utilisateur et de groupe dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour LucidChart, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.


### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Configurer l’approvisionnement automatique de comptes utilisateur vers LucidChart dans Azure AD


1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré LucidChart pour l’authentification unique, recherchez votre instance de LucidChart à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et effectuer une recherche pour **LucidChart** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez LucidChart, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de LucidChart, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Jeton secret** généré par votre compte LucidChart (vous pouvez rechercher le jeton sous votre compte : **Team (Équipe)** > **App Integration (Intégration de l’application)** > **SCIM**). 

    ![Approvisionnement de LucidChart](./media/active-directory-saas-lucidchart-provisioning-tutorial/LucidChart2.png)

6. Dans le portail Azure, cliquez sur **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application LucidChart. Si la connexion échoue, vérifiez que votre compte LucidChart dispose des autorisations d’administrateur et réessayez l’étape 5.

7. Entrez l’adresse de courrier d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case « Envoyer une notification par e-mail en cas de défaillance ».

8. Cliquez sur **Enregistrer**. 

9. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec LucidChart**.

10. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers LucidChart. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes utilisateur dans LucidChart pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

11. Pour activer le service d’approvisionnement Azure AD pour LucidChart, accédez à la section **Paramètres** et définissez l’**État de l’approvisionnement** sur **Activé**.

12. Cliquez sur **Enregistrer**. 

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à LucidChart dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)
