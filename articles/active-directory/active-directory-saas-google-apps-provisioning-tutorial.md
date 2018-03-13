---
title: "Didacticiel : configurer Google Apps pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: "Découvrez comment approvisionner et déprovisionner automatiquement des comptes utilisateur d’Azure AD vers Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: a90fe2c919e0c5452072c4129dcb7a1135d9ec25
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-configure-google-apps-for-automatic-user-provisioning"></a>Didacticiel : configurer Google Apps pour l’approvisionnement automatique des utilisateurs

L’objectif de ce didacticiel est de vous montrer comment approvisionner et déprovisionner automatiquement des comptes d’utilisateur d’Azure Active Directory (Azure AD) vers Google Apps.

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

*   Un locataire Azure Active Directory.
*   Un locataire valide pour Google Apps for Work ou Google Apps for Education Vous pouvez utiliser un compte d’essai gratuit pour chaque service.
*   Un compte utilisateur dans Google Apps avec les autorisations d’administrateur d’équipe.

## <a name="assign-users-to-google-apps"></a>Affecter des utilisateurs à Google Apps

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique de comptes utilisateur, seuls les utilisateurs et les groupes qui ont été « assignés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs ou groupes dans Azure AD ont besoin d’accéder à votre application Google Apps. Une fois cette décision prise, vous pouvez affecter ces utilisateurs à votre application Google Apps en suivant les instructions fournies dans [affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Nous vous recommandons de n’affecter qu’un seul utilisateur Azure AD à Google Apps pour tester la configuration de l’approvisionnement. Vous pouvez affecter des groupes et des utilisateurs supplémentaires ultérieurement.

> Quand vous affecter un utilisateur à Google Apps, sélectionnez le rôle **Utilisateur** ou **Groupe** dans la boîte de dialogue d’affectation. Le rôle **Accès par défaut** ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide tout au long du processus de connexion de votre annuaire Azure AD à l’API d’approvisionnement de comptes d’utilisateur de Google Apps. Elle vous aide également à configurer le service d’approvisionnement pour créer, mettre à jour et désactiver les comptes d’utilisateur affectés dans Google Apps en fonction de l’affectation d’utilisateurs et de groupes dans Azure AD.

>[!TIP]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour Google Apps, en suivant les instructions fournies dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

> [!NOTE]
> Une autre option possible pour automatiser l’approvisionnement des utilisateurs dans Google Apps consiste à utiliser [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS approvisionne vos identités Active Directory locales dans Google Apps. En revanche, la solution proposée dans ce didacticiel approvisionne vos utilisateurs Azure Active Directory (cloud) et vos groupes à extension messagerie dans Google Apps. 

1. Connectez-vous à la [Console d’administration de Google Apps](http://admin.google.com/) avec votre compte d’administrateur, puis sélectionnez **Sécurité**. Si le lien ne s’affiche pas, il est peut-être masqué par le menu **Autres contrôles** situé en bas de l’écran.
   
    ![Sélectionnez Sécurité.][10]

2. Dans la page **Sécurité**, sélectionnez **Informations de référence sur l’API**.
   
    ![Sélectionnez Informations de référence sur l’API.][15]

3. Sélectionnez **Activer l'accès à l'API**.
   
    ![Sélectionnez Informations de référence sur l’API.][16]

    > [!IMPORTANT]
    > Le nom d’utilisateur Azure Active Directory de chaque utilisateur que vous souhaitez approvisionner dans Google Apps *doit* être associé à un domaine personnalisé. Par exemple, les noms d’utilisateur qui ressemblent à bob@contoso.onmicrosoft.com ne sont pas acceptés par Google Apps. En revanche, bob@contoso.com est accepté. Vous pouvez modifier le domaine d’un utilisateur existant en modifiant ses propriétés dans Azure AD. Les étapes suivantes montrent comment définir un domaine personnalisé pour Azure Active Directory et Google Apps.
      
4. Si vous n’avez pas encore ajouté de nom de domaine personnalisé à Azure Active Directory, effectuez les étapes suivantes :
  
    a. Dans le panneau de navigation gauche du [portail Azure](https://portal.azure.com), sélectionnez **Active Directory**. Dans la liste Annuaire, sélectionnez votre annuaire. 

    b. Sélectionnez **Nom de domaine** dans le volet de navigation gauche, puis sélectionnez **Ajouter**.
     
     ![Domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![Ajout de domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. Saisissez votre nom de domaine dans le champ **Nom de domaine** . Ce nom de domaine doit être identique à celui que vous souhaitez utiliser pour Google Apps. Ensuite, sélectionnez le bouton **Ajouter un domaine**.
     
     ![Nom de domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. Sélectionnez **Suivant** pour accéder à la page de vérification. Pour vérifier que vous être propriétaire de ce domaine, modifiez ses enregistrements DNS en fonction des valeurs fournies dans cette page. Vous pouvez choisir d’effectuer la vérification en utilisant des **enregistrements MX** ou des **enregistrements TXT**, selon l’option de **Type d’enregistrement** sélectionnée. 
    
    Pour obtenir des instructions plus complètes sur la vérification des noms de domaine avec Azure AD, consultez [Ajouter votre propre domaine à Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domaine](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. Répétez la procédure précédente pour tous les domaines que vous souhaitez ajouter à votre répertoire.

    > [!NOTE]
    Pour l’attribution d’utilisateurs, le domaine personnalisé de Google Apps doit correspondre au nom de domaine de l’annuaire Azure AD source. S’ils ne correspondent pas, vous pouvez peut-être résoudre le problème en mettant en œuvre une personnalisation du mappage des attributs.


5. Maintenant que vous avez vérifié tous vos domaines avec Azure AD, vous devez les revérifier avec Google Apps. Pour chaque domaine qui n’est pas encore inscrit auprès de Google Apps, effectuez les étapes suivantes :
   
    a. Dans la [Console d’administration de Google Apps](http://admin.google.com/), sélectionnez **Domaines**.
     
     ![Sélectionner les domaines][20]

    b. Sélectionnez **Ajouter un domaine ou un alias de domaine**.
     
     ![Ajouter un nouveau domaine][21]

    c. Sélectionnez **Ajouter un autre domaine**, puis tapez le nom du domaine que vous souhaitez ajouter.
     
     ![Entrer votre nom de domaine][22]

    d. Sélectionnez **Continuer et vérifier la propriété du domaine**. Puis suivez les étapes pour vérifier que vous possédez le nom de domaine. Pour obtenir des instructions complètes sur la vérification de votre domaine avec Google Apps, consultez [Vérification de votre site avec Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Répétez la procédure précédente pour tous les domaines supplémentaires que vous souhaitez ajouter à Google Apps.
     
     > [!WARNING]
     > Si vous modifiez le domaine principal pour votre locataire Google Apps tout en ayant déjà configuré l’authentification unique avec Azure AD, vous devez répéter l’étape 3 sous [Étape 2 : Activer l’authentification unique](#step-two-enable-single-sign-on).
       
6. Dans la [Console d’administration de Google Apps](http://admin.google.com/), sélectionnez **Rôles d’administrateur**.
   
     ![Sélectionner Google Apps][26]

7. Déterminez le compte d’administrateur à utiliser pour gérer l’approvisionnement des utilisateurs. Pour le **Rôle d’administrateur** de ce compte, modifiez les **Privilèges** pour ce rôle. Veillez à activer tous les **Privilèges d’administrateur d’API** pour que ce compte puisse être utilisé pour l’approvisionnement.
   
     ![Sélectionner Google Apps][27]
   
    > [!NOTE]
    > Si vous configurez un environnement de production, la meilleure pratique est de créer un compte d’administrateur dans Google Apps spécialement pour cette étape. Ce compte doit avoir un rôle d’administrateur associé qui dispose des privilèges d’API nécessaires.
     
8. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**.

9. Si vous avez déjà configuré Google Apps pour l’authentification unique, recherchez votre instance de Google Apps à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et recherchez **Google Apps** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez **Google Apps**, puis ajoutez-le à votre liste d’applications.

10. Sélectionnez votre instance de Google Apps, puis sélectionnez l’onglet **Approvisionnement**.

11. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

     ![Approvisionnement](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. Sous la section **Informations d’identification de l’administrateur**, sélectionnez **Autoriser**. Une boîte de dialogue d’autorisation Google Apps s’ouvre dans une nouvelle fenêtre de navigateur.

13. Confirmez que vous souhaitez autoriser Azure Active Directory à apporter des modifications à votre locataire Google Apps. Sélectionnez **Accepter**.
    
     ![Confirmez les autorisations.][28]

14. Dans le portail Azure, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à votre application Google Apps. Si la connexion échoue, vérifiez que votre compte Google Apps dispose des autorisations Administrateur d’équipe. Ensuite, réessayez d’effectuer l’étape **Autoriser**.

15. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**. Ensuite, cochez la case.

16. Sélectionnez **Enregistrer**.

17. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Google Apps**.

18. Dans la section **Mappages d’attributs**, passez en revue les attributs utilisateur qui sont synchronisés à partir d’Azure AD vers Google Apps. Les attributs qui sont des propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans Google Apps pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

19. Pour activer le service d’approvisionnement Azure AD pour Google Apps, définissez l’**État de l’approvisionnement** sur **Activé** dans **Paramètres**.

20. Sélectionnez **Enregistrer**.

Ce processus démarre la synchronisation initiale des utilisateurs ou des groupes affectés à Google Apps dans la section Utilisateurs et groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ pendant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement. Ces journaux décrivent toutes les actions effectuées par le service de provisionnement dans votre application Google Apps.

Pour plus d’informations sur la lecture des journaux d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’authentification unique](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
