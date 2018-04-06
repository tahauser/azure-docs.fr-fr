---
title: Configuration des revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer les revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 88a9f5988d1fe3f4de4fe10da23a5f713e3f3370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configuration des revendications de rôle émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory

Cette fonctionnalité permet aux utilisateurs de personnaliser le type de revendication pour la revendication « rôles » dans le jeton de réponse reçu lors de l’autorisation d’une application à l’aide d’Azure AD.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure AD avec l’installation des répertoires
- Un abonnement pour lequel l’authentification unique est activée
- Vous devez configurer l’authentification unique avec votre application

## <a name="when-to-use-this-feature"></a>Quand utiliser cette fonctionnalité

Si votre application s’attend à voir passer dans la réponse SAML des rôles personnalisés, vous devez utiliser cette fonctionnalité. Cette fonctionnalité vous permet de créer autant de rôles que nécessaire à passer à nouveau d’Azure AD vers votre application.

## <a name="steps-to-use-this-feature"></a>Étapes d’utilisation de cette fonctionnalité

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez le nom de votre application, sélectionnez votre application dans le volet de résultats, puis, cliquez sur **Ajouter** pour ajouter l’application.

    ![Application dans la liste de résultats](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Une fois l’application ajoutée, allez à la page **Propriétés** et copiez **l’ID de l’objet**

    ![Page Propriétés](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

    a. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.

    b. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Extrayez la propriété appRoles à partir de l’objet du principal du service.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Vous devez générer de nouveaux rôles pour votre application maintenant. Vous pouvez télécharger le Générateur de rôles d’Azure AD à partir [d’ici](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Ouvrez le Générateur Azure AD, puis procédez comme suit :

    ![Générateur Azure AD](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Entrez le **Nom de rôle**, la **Description du rôle** et la **Valeur du rôle**. Cliquez sur **Ajouter** pour ajouter le rôle
    
    Après avoir ajouté tous les rôles requis, cliquez sur **Générer**
    
    Copiez le contenu en cliquant sur **Copier le contenu**

    > [!NOTE] 
    > Assurez-vous que vous avez le rôle d’utilisateur **msiam_access** et que l’ID correspond au rôle généré.

    g. Revenez à votre Explorateur de graphique. Modifiez la méthode de **GET** à **PATCH**. Corrigez l’objet du principal du service pour obtenir les appRoles en mettant à jour la propriété appRoles avec les valeurs copiées. Cliquez sur **Exécuter la requête**.

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Vous trouverez ci-dessous un exemple d’objet appRoles. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Une fois le principal du service corrigé avec plusieurs rôles, nous pouvons assigner des utilisateurs aux rôles respectifs. Cela est possible en accédant au Portail et en navigant vers l’application respective. Puis, en cliquant sur l’onglet **Utilisateurs et groupes** en haut. Ce processus répertorie tous les utilisateurs ou groupes.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Pour assigner un rôle à n’importe quel utilisateur, sélectionnez simplement l’utilisateur/groupe en particulier et cliquez sur le bouton **Assigner** dans la partie inférieure de la page.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Le fait de cliquer permet d’afficher une fenêtre contextuelle afin de sélectionner un rôle dans différents rôles définis pour le principal du service respectif.

    c. Choisissez le rôle requis et cliquez sur Envoyer.

8. Après l’assignation des rôles aux utilisateurs, nous devons mettre à jour la table **Attributs** pour définir un mappage personnalisé de revendication de **rôle**.

9. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | -------------- | ----------------|    
    | Nom du rôle      | user.assignedrole |

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configuration d’attribut de l’authentification unique](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

10. Pour tester votre application dans l’authentification unique initiée par IDP, connectez-vous au volet d’accès (https://myapps.microsoft.com) puis cliquez sur la mosaïque de votre application. Dans le jeton SAML, vous devez voir tous les rôles assignés à l’utilisateur avec le nom de la revendication que vous avez attribué.

## <a name="update-existing-role"></a>Mettre à jour un rôle existant

1. Pour mettre à jour un rôle existant, procédez comme suit :

    a. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

    b. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.
    
    c. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extrayez la propriété appRoles à partir de l’objet du principal du service.
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Pour mettre à jour le rôle existant, procédez comme suit :

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Modifiez la méthode de **GET** à **PATCH**.

    * Copiez les rôles existants à partir de l’application et collez-les dans le **Corps de la demande**.
    
    * Mettez à jour la valeur du rôle en remplaçant la **Description du rôle**, la **Valeur rôle** et le **Nom d’affichage du rôle** conformément aux exigences de votre organisation.
    
    * Après avoir mis à jour tous les rôles requis, cliquez sur **Exécuter la requête**.
        
## <a name="delete-existing-role"></a>Supprimer un rôle existant

1. Pour supprimer un rôle existant, procédez comme suit :

    a. Ouvrez [l’Explorateur graphique Azure AD](https://developer.microsoft.com/graph/graph-explorer) dans une autre fenêtre.

    b. Connectez-vous au site de l’Explorateur graphique en utilisant les informations d’identification d’administrateur/Co-admin générales de votre abonné.

    c. Modifiez la version sur **bêta** et extrayez la liste des principaux du service à partir de votre abonné à l’aide de la requête suivante :
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Si vous utilisez plusieurs répertoires, suivez alors le modèle suivant `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Dans la liste des principaux du service extraits, obtenez celui que vous souhaitez modifier. Vous pouvez également utiliser les touches Ctrl + F pour rechercher l’application dans la liste les principaux du service. Recherchez **l’ID de l’objet** que vous avez copié à partir de la page Propriétés et utilisez la requête suivante pour accéder au principal du service respectif.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extrayez la propriété appRoles à partir de l’objet du principal du service.
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Pour supprimer le rôle existant, procédez comme suit :

    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Modifiez la méthode de **GET** à **PATCH**.

    Copiez les rôles existants à partir de l’application et collez-les dans le **Corps de la demande**.
    
    Définissez la valeur **IsEnabled** sur **false** pour le rôle que vous souhaitez supprimer

    Cliquez sur **Exécuter la requête**.
    
    > [!NOTE] 
    > Assurez-vous que vous avez le rôle d’utilisateur **msiam_access** et que l’ID correspond au rôle généré.
    
    g. Après avoir suivi la procédure ci-dessus, conservez la méthode en tant que **PATCH** et collez le rôle restant contenu dans le **Corps de la demande** puis cliquez sur **Exécuter la requête**.
    
    ![Boîte de dialogue de l’Explorateur graphique](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Le rôle sera supprimé après l’exécution de la requête.
    
    > [!NOTE]
    > Le rôle doit d’abord être désactivé avant de pouvoir être supprimée. 

## <a name="next-steps"></a>Étapes suivantes

Consultez [Documentation de l’application ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) pour connaître les étapes supplémentaires.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
