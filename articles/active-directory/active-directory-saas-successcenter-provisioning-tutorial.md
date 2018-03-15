---
title: "Didacticiel : Configurer Cornerstone OnDemand pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer Azure Active Directory pour approvisionner et retirer automatiquement des comptes d’utilisateur sur Cornerstone OnDemand."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Didacticiel : Configurer Cornerstone OnDemand pour l’approvisionnement automatique d’utilisateurs


L’objectif de ce didacticiel est de présenter les étapes à effectuer dans Cornerstone OnDemand et Azure Active Directory (Azure AD) afin de configurer Azure AD pour l’approvisionnement et le retrait automatiques d’utilisateurs et/ou de groupes sur Cornerstone OnDemand.


> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>configuration requise

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   un locataire Azure AD ;
*   Un client Cornerstone OnDemand
*   Un compte utilisateur dans Cornerstone OnDemand avec des autorisations d’administrateur


> [!NOTE]
> L’intégration de l’approvisionnement Azure AD repose sur le [service web Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), disponible pour les équipes Cornerstone OnDemand.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Ajout de Cornerstone OnDemand à partir de la galerie
Avant de configurer Cornerstone OnDemand pour l’approvisionnement automatique d’utilisateurs avec Azure AD, vous devez ajouter Cornerstone OnDemand à partir de la galerie d’applications Azure AD à votre liste d’applications SaaS gérées.

**Pour ajouter Cornerstone OnDemand à partir de la galerie d’applications Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Section Applications d’entreprise][2]
    
3. Pour ajouter Cornerstone OnDemand, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cornerstone OnDemand**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. Dans le volet de résultats, sélectionnez **Cornerstone OnDemand**, puis cliquez sur le bouton **Ajouter** pour ajouter Cornerstone OnDemand à votre liste d’applications SaaS.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Assignation d’utilisateurs à Cornerstone OnDemand

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique d’utilisateurs, seuls les utilisateurs et/ou les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés. 

Avant de configurer et d’activer l’approvisionnement automatique d’utilisateurs, vous devez décider quels utilisateurs et/ou groupes dans Azure AD ont besoin d’accéder à Cornerstone OnDemand. Une fois que vous avez choisi, vous pouvez assigner ces utilisateurs et/ou groupes à votre application Cornerstone OnDemand en suivant les instructions fournies ici :

*   [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Conseils importants pour l’assignation d’utilisateurs à Cornerstone OnDemand

*   Il est recommandé de n’affecter qu’un seul utilisateur Azure AD à Cornerstone OnDemand afin de tester la configuration de l’approvisionnement automatique d’utilisateurs. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.

*   Quand vous assignez un utilisateur à Cornerstone OnDemand, vous devez sélectionner un rôle valide propre à l’application (si disponible) dans la boîte de dialogue d’assignation. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configuration de l’approvisionnement automatique d’utilisateurs sur Cornerstone OnDemand

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement d’Azure AD pour créer, mettre à jour et désactiver des utilisateurs et/ou des groupes dans Cornerstone OnDemand en fonction des assignations d’utilisateurs et/ou de groupes dans Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Pour configurer l’approvisionnement automatique d’utilisateurs pour Cornerstone OnDemand dans Azure AD :


1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

2. Sélectionnez Cornerstone OnDemand dans la liste de vos applications SaaS.
 
    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. Dans la section **Informations d’identification de l’administrateur**, entrez le **Nom d’utilisateur de l’administrateur**, le **Mot de passe d’administrateur** et le **Domaine** de votre compte Cornerstone OnDemand.

    *   Dans le champ **Nom d’utilisateur de l’administrateur**, indiquez le nom de l’utilisateur du compte administrateur sur votre locataire Cornerstone OnDemand. Exemple : admin.

    *   Dans le champ **Mot de passe d’administrateur**, indiquez le mot de passe correspondant au nom de l’utilisateur administrateur.

    *   Dans le champ **Domaine**, indiquez l’URL du service web de Cornerstone. Exemple : le service se trouve à l’adresse `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`. Pour Contoso, le domaine est `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. Après avoir renseigné les champs indiqués à l’étape 5, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à Cornerstone OnDemand. Si la connexion échoue, vérifiez que votre compte Cornerstone OnDemand dispose des autorisations d’administrateur et réessayez.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. Dans le champ **E-mail de notification**, entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement, puis cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Cliquez sur **Enregistrer**.

9. Dans la section **Mappages**, sélectionnez **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Synchroniser des utilisateurs Azure Active Directory avec Cornerstone OnDemand).

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. Passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers Cornerstone OnDemand dans la section **Mappages des attributs**. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Cornerstone OnDemand pour les opérations de mise à jour. Cliquez sur le bouton **Enregistrer** pour valider les modifications.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Pour configurer des filtres d’étendue, reportez-vous aux instructions suivantes fournies dans [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](./active-directory-saas-scoping-filters.md).

12. Pour activer le service d’approvisionnement Azure AD pour Cornerstone OnDemand, affectez la valeur **Activé** au paramètre **Statut d’approvisionnement** dans la section **Paramètres**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Définissez les utilisateurs et/ou groupes que vous aimeriez approvisionner sur Cornerstone OnDemand en choisissant les valeurs souhaitées dans **Étendue** dans la section **Paramètres**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Lorsque vous êtes prêt à effectuer l’approvisionnement, cliquez sur **Enregistrer**.

    ![Approvisionnement Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


Cette opération démarre la synchronisation initiale de tous les utilisateurs et/ou groupes définis dans **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service de provisionnement Azure AD est en cours d’exécution. Vous pouvez utiliser la section **Détails de synchronisation** pour surveiller la progression et les liens vers les rapports d’activité d’approvisionnement, qui décrivent toutes les actions effectuées par le service d’approvisionnement Azure AD sur Cornerstone OnDemand.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-enterprise-apps-manage-provisioning.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>étapes suivantes

* [Découvrez comment consulter les journaux et obtenir des rapports sur l’activité d’approvisionnement](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
