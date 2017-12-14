---
title: "Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour FishEye/Crucible | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kantega SSO pour FishEye/Crucible."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: cbed5303a605579c595a2230c2e18193f93d4366
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour FishEye/Crucible

Ce didacticiel explique comment intégrer Kantega SSO pour FishEye/Crucible avec Azure Active Directory (Azure AD).

L’intégration de Kantega SSO pour FishEye/Crucible avec Azure AD offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Kantega SSO pour FishEye/Crucible.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement connectés à Kantega SSO pour FishEye/Crucible (authentification unique) avec leur comptes Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Kantega SSO pour FishEye/Crucible, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kantega SSO pour FishEye/Crucible pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kantega SSO pour FishEye/Crucible à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Ajout de Kantega SSO pour FishEye/Crucible à partir de la galerie
Pour configurer l’intégration de Kantega SSO pour FishEye/Crucible dans Azure AD, vous devez ajouter Kantega SSO pour FishEye/Crucible à partir de la galerie à la liste de vos applications SaaS managées.

**Pour ajouter Kantega SSO pour FishEye/Crucible à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Kantega SSO pour FishEye/Crucible**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_search.png)

5. Dans le volet de résultats, sélectionnez **Kantega SSO pour FishEye/Crucible**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Kantega SSO pour FishEye/Crucible sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur équivalent à l’utilisateur Azure AD dans Kantega SSO pour FishEye/Crucible. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Kantega SSO pour FishEye/Crucible associé doit être établie.

Dans Kantega SSO pour FishEye/Crucible, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Kantega SSO pour FishEye/Crucible, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Kantega SSO pour FishEye/Crucible](#creating-a-kantega-sso-for-fisheyecrucible-test-user)** pour avoir un équivalent de Britta Simon dans Kantega SSO pour FishEye/Crucible qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kantega SSO pour FishEye/Crucible.

**Pour configurer l’authentification unique Azure AD avec Kantega SSO pour FishEye/Crucible, procédez comme suit :**

1. Sur le portail Azure, dans la page d’intégration de l’application **Kantega SSO pour FishEye/Crucible**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_samlbase.png)

3. En mode initié **IDP**, dans la section **Domaine et URL Kantega SSO pour FishEye/Crucible**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. En mode initié **SP**, activez **Afficher les paramètres d’URL avancés**, puis procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Ces valeurs sont reçues durant la configuration du plug-in FishEye/Crucible qui est décrite plus loin dans le didacticiel.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre serveur local FishEye/Crucible en tant qu’administrateur.

8. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon1.png)

9. Dans la section Paramètres système, cliquez sur **Find new add-ons (Rechercher de nouveaux modules complémentaires)**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/add-on2.png)

10. Recherchez **Kantega SSO pour Crucible**, puis cliquez sur le bouton **Installer** pour installer le nouveau plug-in SAML.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon2.png)

11. L’installation du plug-in démarre. 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon33.png)

12. Une fois l’installation terminée. Cliquez sur **Fermer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon34.png)

13. Cliquez sur **Gérer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon35.png)

14. Cliquez sur **Configurer** pour configurer le nouveau plug-in.    

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon3.png)

15. Dans la section **SAML**. Dans le menu déroulant **Ajouter le fournisseur d’identité**, sélectionnez **Azure Active Directory (Azure AD)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon4.png)

16. Sélectionnez le niveau d’abonnement **De base**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon5.png)

17. Dans la section **Propriétés de l’application**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Copiez la valeur **URI ID d'application** et utilisez-la en tant que **Identifier, Reply URL, and Sign-On URL** (Identificateur, URL de réponse et URL de connexion) dans la section **Domaine et URL Kantega SSO pour FishEye/Crucible** du portail Azure.

    b. Cliquez sur **Suivant**.

18. Dans la section **Metadata import** (Importation des métadonnées), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Sélectionnez **Metadata file on my computer** (Fichier de métadonnées sur mon ordinateur), puis chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Cliquez sur **Suivant**.

19. Dans la section **Name and SSO location** (Nom et emplacement de l’authentification unique), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Ajoutez le nom du fournisseur d’identité dans la zone de texte **Identity provider name** (Nom du fournisseur d’identité) (par exemple, Azure AD).

    b. Cliquez sur **Suivant**.

20. Vérifiez le certificat de signature, puis cliquez sur **Suivant**.  

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon9.png)

21. Dans la section **FishEye user accounts** (Comptes d’utilisateur FishEye), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Sélectionnez **Create users in FishEye's internal Directory if needed** (Créer des utilisateurs dans l’annuaire interne de FishEye si nécessaire) et entrez le nom de groupe approprié pour les utilisateurs (peut être plusieurs groupes séparés par des virgules).

    b. Cliquez sur **Suivant**.

22. Cliquez sur **Terminer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon11.png)

23. Dans la section **Known domains for Azure AD** (Domaines connus pour Azure AD), procédez comme suit : 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Sélectionnez **Known domains** (Domaines connus) dans le volet gauche de la page.

    b. Entrez le nom de domaine dans la zone de texte **Known domains** (Domaines connus).

    c. Cliquez sur **Enregistrer**.  

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-kantega-sso-for-fisheyecrucible-test-user"></a>Création d’un utilisateur de test Kantega SSO pour FishEye/Crucible

Pour permettre aux utilisateurs Azure AD de se connecter à FishEye/Crucible, vous devez les approvisionner dans FishEye/Crucible. Dans Kantega SSO pour FishEye/Crucible, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre serveur local Crucible en tant qu’administrateur.

2. Pointez sur le roue dentée, puis cliquez sur **Users** (Utilisateurs).

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user1.png) 

3. Dans la section de l’onglet **Users** (Utilisateurs), cliquez sur **Add user** (Ajouter un utilisateur).

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user2.png)

4. Dans la page de boîte de dialogue **Add New User** (Ajouter un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/user3.png) 

    a. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.
    
    b. Dans la zone de texte **Display Name** (Nom d’affichage), tapez le nom d’affichage de l’utilisateur, par exemple, Britta Simon.
    
    c. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.  

    e. Dans la zone de texte **Confirm Password** (Confirmer le mot de passe), entrez à nouveau le mot de passe de l’utilisateur.

    f. Cliquez sur **Add**.   

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous permettez à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Kantega SSO pour FishEye/Crucible.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Kantega SSO pour FishEye/Crucible, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Kantega SSO pour FishEye/Crucible**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Kantega SSO pour FishEye/Crucible dans le panneau d’accès, vous devez être automatiquement connecté à votre application Kantega SSO pour FishEye/Crucible.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforfisheyecrucible-tutorial/tutorial_general_203.png

