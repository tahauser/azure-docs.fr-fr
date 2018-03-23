---
title: 'Didacticiel : Configurer Replicon pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 8d612012505ea43a3635650c6a38fe993b8e57f6
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Didacticiel : configurer Replicon pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Replicon et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Replicon.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis


Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un locataire Replicon avec le forfait [Plus](https://www.replicon.com/time-bill-pricing/) ou mieux activé
*   Un compte d’utilisateur dans Replicon avec des autorisations d’administration

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur [l’API Replicon](https://www.replicon.com/help/developers) qui est disponible pour les équipes Replicon disposant du forfait Plus ou mieux.

## <a name="adding-replicon-from-the-gallery"></a>Ajout de Replicon à partir de la galerie
Avant de configurer Replicon pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Replicon à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Replicon à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]
    
3. Pour ajouter Replicon, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Replicon**.

    ![Recherche d’application Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. Dans le volet de résultats, sélectionnez **Replicon**, puis cliquez sur le bouton **Ajouter** pour ajouter Replicon à votre liste d’applications SaaS.

    ![Résultats de recherche Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Créer une application Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Affectation d’utilisateurs à Replicon

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Replicon. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Replicon en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Conseils importants pour l’affectation d’utilisateurs à Replicon

*   Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Replicon afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à Replicon, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Replicon 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Replicon en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Replicon en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Replicon](active-directory-saas-replicon-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique d’utilisateur, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Replicon dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Replicon dans votre liste d’applications SaaS.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur**, le **Mot de passe d’administrateur**, **CompanyID** et le **Domaine** de votre compte Replicon. Voici des exemples de valeurs :

    *   Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Replicon. Exemple : contosoadmin.

    *   Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe correspondant au nom de l’utilisateur administrateur.

    *   Dans le champ **CompanyID**, indiquez l’ID de société de votre locataire Replicon. Exemple : l’ID de société de la connexion ci-dessous est Contoso.

    ![Connexion Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   Dans le champ **Domaine**, indiquez le domaine comme décrit dans l’étape 6.
    
6. Obtenez l’URL **serviceEndpointRootURL** pour votre compte de locataire Replicon en vous basant sur les étapes mentionnées dans le [service d’aide Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Lors de l’obtention de l’URL, le **domaine** doit être le sous-domaine de l’URL **serviceEndpointRootURL**, comme souligné. 

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Replicon. Si la connexion échoue, vérifiez que votre compte Replicon dispose des autorisations d’administrateur et réessayez.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory sur Replicon**.
    
    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Replicon. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Replicon pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./active-directory-saas-scoping-filters.md).

13. Pour activer le service d’approvisionnement Azure AD pour Replicon, affectez au paramètre **Statut d’approvisionnement** la valeur **Activé** dans la section **Paramètres**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Replicon en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Replicon.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
