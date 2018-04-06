---
title: 'Azure Active Directory B2C : configuration LinkedIn | Microsoft Docs'
description: Fourniture d’inscription et de connexion à des consommateurs disposant de comptes LinkedIn dans vos applications sécurisées par Azure Active Directory B2C
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
ms.openlocfilehash: 91970b3ec41c645cd1cd3e203cc96ad655dafd7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes LinkedIn
## <a name="create-a-linkedin-application"></a>Création d’une application LinkedIn
Pour utiliser LinkedIn en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application LinkedIn et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte LinkedIn. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://www.linkedin.com/](https://www.linkedin.com/).

1. Accédez au site web [Développeurs LinkedIn](https://www.developer.linkedin.com/) et connectez-vous à l’aide des informations d’identification de votre compte LinkedIn.
2. Cliquez sur **Mes applications** dans la barre de menu, puis sur **Créer une application**.
   
    ![LinkedIn - nouvelle application](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. Dans le formulaire **Créer une nouvelle application**, entrez les informations pertinentes (**Nom de l’entreprise**, **Nom**, **Description**, **Application Logo URL** (URL du logo de l’application), **Application Use** (Utilisation de l’application), **URL du site Web**, **Adresse de messagerie professionnelle** et **Numéro de téléphone professionnel**).
4. Acceptez les **conditions d’utilisation de l’API LinkedIn**, puis cliquez sur **Envoyer**.
   
    ![LinkedIn - inscription d’application](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Copiez les valeurs de **ID client** et **Clé secrète client**. (Vous les trouverez sous **Clés d’authentification**.) Vous aurez besoin de ces deux valeurs pour configurer LinkedIn en tant que fournisseur d’identité dans votre client.
   
   > [!NOTE]
   > **Client Secret** est une information d’identification de sécurité importante.
   > 
   > 
6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de redirection autorisées** (sous **OAuth 2.0**). Remplacez **{tenant}** par votre nom de client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Ajouter**, puis sur **Mettre à jour**. La valeur **{tenant}** respecte la casse.
   
    ![LinkedIn - configuration d’application](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>Configuration de LinkedIn en tant que fournisseur d’identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « LI ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **LinkedIn**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité** , puis entrez l’ID client et la clé secrète client de l’application LinkedIn que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration LinkedIn.

