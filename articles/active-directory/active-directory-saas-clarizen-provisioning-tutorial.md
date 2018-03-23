---
title: 'Didacticiel : Configurer Clarizen pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes utilisateur sur Clarizen.
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
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 3e75ce17f2806e4cb66f90f233b265398cdd0878
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Didacticiel : configurer Clarizen pour l’approvisionnement automatique d’utilisateurs

L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Clarizen et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Clarizen.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prérequis


Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un locataire Clarizen avec l’[Édition entreprise](https://www.clarizen.com/product/pricing/) ou supérieur activé 
*   Un compte d’utilisateur dans Clarizen avec des autorisations d’administration

> [!NOTE]
> L’intégration de l’approvisionnement Azure AD s’appuie sur l’[API REST Clarizen](https://api.clarizen.com/v2.0/services/), qui est disponible pour les équipes Clarizen disposant de l’Édition entreprise ou mieux.

## <a name="adding-clarizen-from-the-gallery"></a>Ajout de Clarizen à partir de la galerie
Avant de configurer Clarizen pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Clarizen à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Clarizen à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]
    
3. Pour ajouter Clarizen, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Clarizen**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. Dans le volet de résultats, sélectionnez **Clarizen**, puis cliquez sur le bouton **Ajouter** pour ajouter Clarizen à votre liste d’applications SaaS.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Affectation d’utilisateurs à Clarizen

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Clarizen. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Clarizen en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Conseils importants pour l’affectation d’utilisateurs à Clarizen

*   Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Clarizen afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à Clarizen, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Clarizen 

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Clarizen en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Clarizen en suivant les instructions fournies dans le [didacticiel sur l’authentification unique Clarizen](active-directory-saas-clarizen-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique d’utilisateur, bien que ces deux fonctionnalités se complètent.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Clarizen dans Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Clarizen dans votre liste d’applications SaaS.
 
    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Sélectionnez l’onglet **Approvisionnement**.
    
    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Domaine**, le **Nom d’utilisateur de l’administrateur** et le **Mot de passe d’administrateur** de votre compte Clarizen. Voici des exemples de valeurs :

    *   Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Clarizen. Exemple : admin@contoso.com.

    *   Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe du compte administrateur correspondant au nom de l’utilisateur administrateur.

    *   Dans le champs **Domaine**, indiquez le sous-domaine en fonction de l’étape 6.
    
6. Récupérez **serverLocation** pour votre compte Clarizen en fonction des étapes mentionnées sous **Authentification** dans le [Guide d’API REST Clarizen](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Lors de l’obtention de serverLocation, utilisez le sous-domaine de l’URL comme montré ci-dessous pour remplir le champ **Domaine**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Clarizen. Si la connexion échoue, vérifiez que votre compte Clarizen dispose des autorisations d’administrateur et réessayez.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Cliquez sur **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Clarizen**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés entre Azure AD et Clarizen. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Clarizen pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Clarizen**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs groupe qui sont synchronisés entre Azure AD et Clarizen. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les groupes dans Clarizen pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./active-directory-saas-scoping-filters.md).

15. Pour activer le service d’approvisionnement Azure AD pour Clarizen, affectez au paramètre **Statut d’approvisionnement** la valeur **Activé** dans la section **Paramètres**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Clarizen en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement de Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Clarizen.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
