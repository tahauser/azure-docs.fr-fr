---
title: 'Azure Active Directory B2C : Configuration de WeChat | Microsoft Docs'
description: "Proposez l’inscription et la connexion à des consommateurs disposant de comptes WeChat dans vos applications sécurisées par Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 1cc1de1926edd28d09a3779da9595ef22491d0d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C : Proposer l’inscription et la connexion à des consommateurs disposant de comptes WeChat

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-wechat-application"></a>Créer une application WeChat

Pour utiliser WeChat en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application WeChat et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte WeChat. Si vous n’en avez pas, vous pouvez en obtenir un en vous inscrivant via une de leurs applications mobiles ou à l’aide de votre numéro QQ. Après cela, ouvrez votre compte inscrit auprès du programme de développeurs WeChat. Pour plus d’informations, consultez la [page suivante](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html):

### <a name="register-a-wechat-application"></a>Inscrire une application WeChat

1. Accédez à [https://open.weixin.qq.com/](https://open.weixin.qq.com/) et connectez-vous.
2. Cliquez sur **管理中心** (Centre de gestion).
3. Suivez les étapes nécessaires à l’inscription d’une nouvelle application.
4. Pour **授权回调域** (URL de rappel), entrez `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Par exemple, si votre `tenant_name` est contoso.onmicrosoft.com, définissez l’URL sur `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Recherchez et copiez l’**ID de l’application** et la **clé d’application**. Vous en aurez besoin ultérieurement.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurer WeChat en tant que fournisseur d’identité dans votre locataire
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « WeChat ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **WeChat**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Entrez la **clé d’application** que vous avez copiée précédemment dans **ID client**.
8. Entrez le **secret d’application** que vous avez copié précédemment dans **Secret client**.
9. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration WeChat.

