---
title: "Didacticiel : Intégration d’Azure Active Directory à Workplace par Facebook | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 4f34f6509d762cba6aba98a6eba010fd94656e67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Didacticiel : Intégration d’Azure Active Directory à Workplace by Facebook

Dans ce didacticiel, vous allez apprendre à intégrer Workplace by Facebook à Azure Active Directory (Azure AD).

L’intégration de Workplace by Facebook à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Workplace by Facebook.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Workplace by Facebook (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour plus d’informations sur l’intégration d’applications SaaS (software as a service) à Azure AD, consultez l’article [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Workplace by Facebook, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Workplace by Facebook pour lequel l’authentification unique est activée

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajoutez Workplace by Facebook à partir de la galerie.
2. Configurez et testez l’authentification unique Azure AD.

## <a name="add-workplace-by-facebook-from-the-gallery"></a>Ajout de Workplace by Facebook à partir de la galerie
Pour configurer l’intégration de Workplace by Facebook avec Azure AD, ajoutez Workplace by Facebook à partir de la galerie à votre liste d’applications SaaS gérées.

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez **Workspace by Facebook**, puis sélectionnez **Workspace by Facebook** dans les résultats. Sélectionnez ensuite **Ajouter** pour ajouter l’application.

    ![Workplace by Facebook dans la liste des résultats](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Workplace by Facebook, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’utilisateur Workplace by Facebook équivalant à un utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Workplace by Facebook associé.

Pour cela, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Workplace by Facebook.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application Workplace by Facebook.

1. Sur le portail Azure, à la page d’intégration de l’application **Workplace by Facebook**, sélectionnez **Authentification unique**.

    ![Configurer le lien d’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous la zone **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Dans la section **Domaine et URL Workplace by Facebook**, effectuez les étapes suivantes :

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company subdomain>.facebook.com`

    b. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > Ces valeurs sont données à titre d’exemple uniquement. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de prise en charge des clients Workplace by Facebook](https://workplace.fb.com/faq/). 

4. Dans la section **Certificat de signature SAML**, sélectionnez **Certificat (Base64)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Workplace by Facebook**, sélectionnez **Configurer Workplace by Facebook** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Configuration de Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Workplace by Facebook en tant qu’administrateur.
  
   > [!NOTE] 
   > Dans le cadre du processus d’authentification SAML, Workplace peut utiliser des chaînes de requête d’une taille pouvant aller jusqu’à 2,5 Ko pour transmettre des paramètres à Azure AD.

8. Dans **Company Dashboard**, accédez à l’onglet **Authentication**.

9. Sous **SAML Authentication**, sélectionnez **SSO Only** dans la liste déroulante.

10. Entrez les valeurs copiées à partir de la section **Configuration de Workplace by Facebook** du portail Azure dans les champs correspondants :

    *   Dans la zone de texte **SAML URL**, collez la valeur de l’**URL du service d’authentification unique**, copiée à partir du portail Azure.
    *   Dans la zone de texte **SAML Issuer URL** (URL de l’émetteur SAML), collez la valeur de l’**ID d’entité SAML**, que vous avez copiée à partir du portail Azure.
    *   Dans **SAML Logout Redirect** (Redirection de déconnexion SAML) (facultatif), collez la valeur de l’**URL de déconnexion**, que vous avez copiée à partir du portail Azure.
    *   Dans le bloc-notes, ouvrez votre **certificat codé en base 64**, téléchargé à partir du portail Azure. Copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat SAML**.

11. Vous devrez peut-être entrer l’URL d’audience, l’URL de destinataire et l’URL du service consommateur d’assertion (ACS), répertoriées sous la section **SAML Configuration** (Configuration SAML).

12. Faites défiler l’affichage jusqu’au bas de la section et sélectionnez **Test SSO**. Une fenêtre indépendante contenant la page de connexion à Azure AD s’affiche. Pour vous authentifier, entrez vos informations d’identification comme à votre habitude. Vérifiez que l’adresse e-mail retournée par Azure AD est identique au compte Workplace avec lequel vous êtes connecté.

13. Si le test se termine avec succès, faites défiler la page vers le bas et sélectionnez **Enregistrer**.

14. La page de connexion à Azure AD sera désormais présentée à tous les utilisateurs de Workplace pour qu’ils s’authentifient.

Vous pouvez choisir de configurer une URL de déconnexion SAML, qui peut être utilisée pour pointer vers la page de déconnexion d’Azure AD. Quand ce paramètre est activé et configuré, l’utilisateur n’est plus dirigé vers la page de déconnexion de Workplace. Au lieu de cela, il est redirigé vers l’URL qui a été ajoutée dans le paramètre de redirection de déconnexion SAML.


> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez simplement l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="configure-reauthentication-frequency"></a>Configuration de la fréquence de réauthentification

Vous pouvez configurer Workplace pour demander une vérification SAML chaque jour, tous les trois jours, toutes les semaines, toutes les deux semaines, tous les mois ou jamais.

> [!NOTE] 
>La valeur minimale pour la vérification SAML sur les applications mobiles est toutes les semaines.

Vous pouvez également forcer une réinitialisation SAML pour tous les utilisateurs. Pour ce faire, utilisez le bouton **Require SAML authentication for all users now** (Demander une authentification SAML pour tous les utilisateurs).


### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

1. Dans le volet gauche du **portail Azure**, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, et sélectionnez **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter**.
 
    ![Bouton Ajouter](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, saisissez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et prenez-en note.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-workplace-by-facebook-test-user"></a>Création d’un utilisateur de test Workplace by Facebook

Dans cette section, un utilisateur appelé Britta Simon est créé dans Workplace by Facebook. Workplace by Facebook prend en charge l’approvisionnement juste-à-temps, qui est activé par défaut.

Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas d’utilisateur dans Workplace by Facebook, un nouvel utilisateur est créé quand vous tentez d’accéder à Workplace by Facebook.

>[!Note]
>Si vous avez besoin de créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients Workplace by Facebook](https://workplace.fb.com/faq/).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Workplace by Facebook.

![Affecter des utilisateurs][200] 

1. Dans le portail Azure, ouvrez la vue des applications. Accédez à la vue des répertoires, puis sélectionnez **Applications d’entreprise** et **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Workplace by Facebook**.

    ![Lien Workplace by Facebook dans la liste des applications](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Sélectionnez **Ajouter**. Ensuite, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste d’utilisateurs.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès.
Pour plus d’informations, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).


## <a name="next-steps"></a>Étapes suivantes

* Consultez la [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md).
* Voir [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).
* En savoir plus sur la [configuration de l’approvisionnement d’utilisateurs](active-directory-saas-facebook-at-work-provisioning-tutorial.md).



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png

