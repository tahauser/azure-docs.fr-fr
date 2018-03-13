---
title: "Didacticiel : configurer Dropbox pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dropbox for Business."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6b082127f74ff5f823506c0f75f40c4b4246dcfe
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Didacticiel : configurer Dropbox for Business pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Dropbox for Business et Azure AD permettant d’approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers Dropbox for Business.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un abonné Azure Active Directory.
*   Un abonnement Dropbox for Business pour lequel l’authentification unique est activée.
*   Un compte d’utilisateur dans Dropbox for Business avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-dropbox-for-business"></a>Assignation d’utilisateurs à Dropbox for Business

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes d’utilisateur, les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application Dropbox for Business. Dès que vous avez fait votre choix, vous pouvez assigner ces utilisateurs à votre application Dropbox for Business en suivant les instructions disponibles ici :

[Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Conseils importants concernant l’assignation d’utilisateurs à Dropbox for Business

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à Dropbox for Business afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Lorsque vous assignez un utilisateur à Dropbox for Business, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section va vous guider pour connecter votre instance Azure AD à votre compte d’utilisateur Dropbox for Business qui fournit l’API. Vous pourrez aussi découvrir comment configurer le service d’approvisionnement pour créer, mettre à jour et désactiver les comptes d’utilisateur assignés dans Dropbox for Business, en fonction de l’assignation des utilisateurs et des groupes dans Azure AD.

>[!Tip]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Dropbox for Business, grâce aux instructions disponibles sur le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="to-configure-automatic-user-account-provisioning"></a>Pour configurer l’approvisionnement automatique des comptes d’utilisateur :

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Dropbox for Business pour l’authentification unique, recherchez votre instance de Dropbox for Business à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Dropbox for Business** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez Dropbox for Business, puis ajoutez-la à votre liste d’applications.

3. Sélectionnez votre instance de Dropbox for Business, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. Sous la section **informations d’identification de l’administrateur**, cliquez sur **Autoriser**. Cela permet d’ouvrir une boîte de dialogue de connexion à Dropbox for Business dans une nouvelle fenêtre de navigateur.

6. Dans la boîte de dialogue **Se connecter à Dropbox pour établir une connexion à Active Directory**, connectez-vous à votre abonné Dropbox for Business.

     ![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769518.png "Approvisionnement d’utilisateurs")

7. Confirmez que vous souhaitez permettre à Azure Active Directory d’apporter des modifications à votre client Dropbox for Business. Cliquez sur **Autoriser**.
    
      ![Approvisionnement d'utilisateurs](./media/active-directory-saas-dropboxforbusiness-provisioning-tutorial/ic769519.png "Approvisionnement d’utilisateurs")

8. Sur le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application Dropbox for Business. Si la connexion échoue, vérifiez que votre compte Dropbox for Business dispose des autorisations d’administrateur d’équipe et recommencez l’étape **« Autoriser »**.

9. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case.

10. Cliquez sur **Enregistrer.**

11. Dans la section Mappages, sélectionnez **Synchroniser des utilisateurs Azure Active Directory avec Dropbox for Business**.

12. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers Dropbox for Business. Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes d’utilisateur dans Dropbox for Business, en vue d’opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

13. Pour activer le service d’approvisionnement Azure AD pour Dropbox for Business, accédez à la section Paramètres et définissez l’**État de l’approvisionnement** sur **Activé**.

14. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes assignés à Dropbox for Business dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application Dropbox for Business.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-dropboxforbusiness-tutorial.md)