---
title: 'Azure Active Directory B2C : configuration Amazon | Microsoft Docs'
description: Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Amazon dans vos applications sécurisées par Azure Active Directory B2C.
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
ms.openlocfilehash: a2989baa61e7b69534fe5703b2501d62a4f8aa94
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Amazon
## <a name="create-an-amazon-application"></a>Création d’une application Amazon
Pour utiliser Amazon en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Amazon et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Amazon. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [http://www.amazon.com/](http://www.amazon.com/).

1. Accédez au [centre des développeurs Amazon](https://login.amazon.com/) et connectez-vous avec les informations d’identification de votre compte Amazon.
2. Si ce n’est déjà fait, cliquez sur **Sign Up**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
3. Cliquez sur **Register new application**.
   
    ![Inscription d’une nouvelle application sur le site Web d’Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Fournissez les informations relatives à l’application (**Nom**, **Description** et **Privacy Notice URL** (URL de déclaration de confidentialité), puis cliquez sur **Enregistrer**.
   
    ![Fourniture d’informations d’application pour l’inscription d’une nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. Dans la section **Paramètres web**, copiez les valeurs **ID client** et **Clé secrète client**. (Vous devez au préalable cliquer sur le bouton **Show Secret** (Afficher la clé secrète client).) Vous avez besoin de ces deux valeurs pour configurer Amazon en tant que fournisseur d’identité dans votre client. Cliquez sur **Modifier** au bas de la section. **Client Secret** est une information d’identification de sécurité importante.
   
    ![Fourniture de l’ID client et de la clé secrète client pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Entrez `https://login.microsoftonline.com` dans le champ **Origines JavaScript autorisées** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Allowed Return URLs** (URL de retour autorisées). Remplacez **{tenant}** par votre nom de client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Enregistrer**. La valeur **{tenant}** respecte la casse.
   
    ![Fourniture de JavaScript Origins et Return URLs pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Configuration d’Amazon en tant que fournisseur d’identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Amzn ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Amazon**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité** , puis entrez l’ID client et la clé secrète client de l’application Amazon que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Amazon.

