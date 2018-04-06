---
title: 'Azure Active Directory B2C : configuration de compte Microsoft | Microsoft Docs'
description: Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Microsoft dans vos applications sécurisées par Azure Active Directory B2C.
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
ms.openlocfilehash: e167c8737bc600f52759f33456fec3bc6e0a3678
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Microsoft
## <a name="create-a-microsoft-account-application"></a>Créer une application de compte Microsoft
Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application de compte Microsoft et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Microsoft. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://www.live.com/](https://www.live.com/).

1. Accédez au [Portail d’inscription des applications Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) et connectez-vous avec votre compte Microsoft.
2. Cliquez sur **Ajouter une application**.
   
    ![Compte Microsoft - Ajouter une nouvelle application](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Indiquez un **nom** pour votre application et cliquez sur **Créer une application**.
   
    ![Compte Microsoft - Nom d’application](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Copiez la valeur **ID de l’application**. Vous aurez besoin de cette valeur pour configurer le compte Microsoft en tant que fournisseur d’identité dans votre client.
   
    ![Compte Microsoft - ID de l’application](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Cliquez sur **Ajouter une plateforme** et choisissez **Web**.
   
    ![Compte Microsoft - Ajouter une plateforme](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Compte Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Rediriger les URI** . Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com).
   
    ![Compte Microsoft - URL de redirection](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Cliquez sur **Générer un nouveau mot de passe** dans la section **Secrets de l’application**. Copiez le nouveau mot de passe affiché à l’écran. Vous aurez besoin de cette valeur pour configurer le compte Microsoft en tant que fournisseur d’identité dans votre client. Ce mot de passe est une information d’identification de sécurité importante.
   
    ![Compte Microsoft - générer un nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Compte Microsoft - Nouveau mot de passe](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Cochez la case **Support du Kit de développement logiciel (SDK) Live** dans la section **Options avancées**. Cliquez sur **Enregistrer**.
   
    ![Compte Microsoft - Support du Kit SDK Live](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configuration du compte Microsoft en tant que fournisseur d’identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, saisissez « MSA ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Compte Microsoft**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité** , puis saisissez l’ID de l’application et le mot de passe de l’application de compte Microsoft que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte Microsoft.

