---
title: 'Azure Active Directory B2C : configuration Google+ | Microsoft Docs'
description: Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+ dans vos applications sécurisées par Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: db3044754e0c1316b49eda0600d41e3009c1ba16
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+
## <a name="create-a-google-application"></a>Création d’une application Google+
Pour utiliser Google+ en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Google+ et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Google+. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accédez à la [Console développeur de Google](https://console.developers.google.com/) et connectez-vous avec les informations d’identification de votre compte Google+.
2. Cliquez sur **Créer un projet**, saisissez un **nom de projet**, puis cliquez sur **Créer**.
   
    ![Google+ - Prise en main](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+ - Nouveau projet](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Cliquez sur **Gestionnaire d’API**, puis sur **Informations d’identification** dans le volet de navigation de gauche.
4. Cliquez sur l’onglet **OAuth consent screen** en haut.
   
    ![Google+ - Informations d’identification](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Sélectionnez ou spécifiez une valeur valide pour **Adresse de messagerie**, fournissez une valeur pour **Nom de produit**, puis cliquez sur **Enregistrer**.
   
    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Cliquez sur **Nouvelles informations d’identification**, puis sur **ID client OAuth**.
   
    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Sous **Type d’application**, sélectionnez **Application web**.
   
    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Fournissez un **nom** pour votre application, entrez `https://login.microsoftonline.com` dans le champ **Origines JavaScript autorisées**, et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URI de redirection autorisée**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com). La valeur **{tenant}** respecte la casse. Cliquez sur **Créer**.
   
    ![Google+ - Créer un identifiant client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Copiez les valeurs de **ID client** et **Clé secrète client**. Vous aurez besoin de ces deux valeurs pour configurer Google+ en tant que fournisseur d'identité dans votre client. **Client secret** est une information d’identification de sécurité importante.
   
    ![Google+ - Clé secrète client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configuration de Google+ en tant que fournisseur d'identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « G+ ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Google**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité** , puis entrez l’ID client et la clé secrète client de l’application Google+ que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google+.

