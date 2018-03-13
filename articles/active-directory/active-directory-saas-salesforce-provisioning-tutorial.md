---
title: "Didacticiel : configurer Salesforce pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3d300eb397b58b4e1f8c8a6516e0a279980d8d09
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Didacticiel : configurer Salesforce pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de montrer les étapes à effectuer dans Salesforce et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur d’Azure AD vers Salesforce.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un client Azure Active Directory.
*   Vous devez posséder un abonné valide pour les éditions Salesforce for Work ou Salesforce for Education. Vous pouvez utiliser un compte d’essai gratuit pour chaque service.
*   Un compte d’utilisateur dans Salesforce avec les autorisations d’administrateur d’équipe.

## <a name="assigning-users-to-salesforce"></a>Assigner des utilisateurs à Salesforce

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service de provisionnement, vous devez déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à votre application Salesforce. Une fois cette décision prise, vous pouvez affecter ces utilisateurs à votre application Salesforce en suivant les instructions fournies dans [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Conseils importants concernant l’assignation d’utilisateurs à Salesforce

*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à Salesforce afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*  Lorsque vous assignez un utilisateur à Salesforce, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement

    > [!NOTE]
    > Cette application importe des rôles personnalisés à partir de Salesforce dans le cadre du processus d’approvisionnement. Il est possible que le client souhaite sélectionner ce processus lors de l’assignation des utilisateurs

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section va vous guider afin de connecter votre instance Azure AD à votre compte utilisateur Salesforce qui fournit l’API. À l’aide de ce guide, vous pourrez aussi découvrir comment configurer le service d’approvisionnement afin de créer, mettre à jour et désactiver des comptes utilisateur assignés dans Salesforce, en fonction des attributions d’utilisateur et de groupe dans Azure AD.

>[!Tip]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Salesforce, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

Cette section décrit comment activer l’approvisionnement des utilisateurs des comptes d’utilisateurs Active Directory sur Salesforce.

1. Dans le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Si vous avez déjà configuré Salesforce pour l’authentification unique, recherchez votre instance de Salesforce à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et effectuer une recherche pour **Salesforce** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez Salesforce, puis ajoutez-le à votre liste d’applications.

3. Sélectionnez votre instance de Salesforce, puis sélectionnez l’onglet **Approvisionnement**.

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![approvisionnement](./media/active-directory-saas-salesforce-provisioning-tutorial/provisioning.png)

5. Dans la section **Informations d’identification de l’administrateur**, fournissez les paramètres de configuration suivants :
   
    a. Dans la zone de texte **Nom d’utilisateur administrateur**, saisissez le nom d’un compte Salesforce auquel le profil **Administrateur système** est assigné dans Salesforce.com.
   
    b. Dans la zone de texte **Mot de passe d’administrateur**, entrez le mot de passe de ce compte.

6. Pour obtenir le jeton de sécurité Salesforce, ouvrez un nouvel onglet et connectez-vous au même compte d’administration Salesforce. Dans le coin supérieur droit de la page, cliquez sur votre nom, puis cliquez sur **Paramètres**.

     ![Activer l’approvisionnement automatique des utilisateurs](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-my-settings.png "Activer l’approvisionnement automatique des utilisateurs")

7. Dans le volet de navigation gauche, cliquez sur **Mes informations personnelles** pour développer la section associée, puis sur **Réinitialiser mon jeton de sécurité**.
  
    ![Activer l’approvisionnement automatique des utilisateurs](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-personal-reset.png "Activer l’approvisionnement automatique des utilisateurs")

8. Sur la page **Réinitialiser le jeton de sécurité**, cliquez sur **Réinitialiser le jeton de sécurité**.

    ![Activer l’approvisionnement automatique des utilisateurs](./media/active-directory-saas-salesforce-provisioning-tutorial/sf-reset-token.png "Activer l’approvisionnement automatique des utilisateurs")

9. Contrôlez la boîte de réception associée à ce compte d’administrateur. Recherchez un message électronique provenant de Salesforce.com qui contient le nouveau jeton de sécurité.

10. Copiez le jeton, accédez à votre fenêtre Azure AD et collez-le dans le champ **Jeton secret**.

11. L’**URL de locataire** doit être entrée si l’instance de Salesforce se trouve sur Salesforce Government Cloud. Dans le cas contraire, elle est facultative. Entrez l’URL de locataire en utilisant le format https://votre-instance.my.salesforce.com, où vous devez remplacer votre-instance par le nom de votre instance Salesforce.

12. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application Salesforce.

13. Dans le champ **E-mail de notification**, saisissez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case.

14. Cliquez sur **Enregistrer.**  
    
15.  Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Salesforce.**

16. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers Salesforce. Remarque : Les attributs sélectionnés en tant que propriétés de **Correspondance** servent à faire correspondre les comptes utilisateur dans Salesforce, en vue d’opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

17. Pour activer le service d’approvisionnement Azure AD pour Salesforce, accédez à la section Paramètres et définissez le **Statut de l’approvisionnement** sur **Activé**.

18. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à Salesforce dans la section Utilisateurs et Groupes. Notez que la synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application Salesforce.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
