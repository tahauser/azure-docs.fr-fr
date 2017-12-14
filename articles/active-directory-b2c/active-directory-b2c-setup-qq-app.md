---
title: 'Azure Active Directory B2C : configuration QQ | Microsoft Docs'
description: "Proposez l’inscription et la connexion à des consommateurs disposant de comptes QQ dans vos applications sécurisées par Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: d4cc26d4f206baf9137feae0825b1f9fa5a7c8d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C : Proposer l’inscription et la connexion à des consommateurs disposant de comptes QQ

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

## <a name="create-a-qq-application"></a>Créer une application QQ

Pour utiliser QQ en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application QQ et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte QQ. Si vous n’avez pas, vous pouvez en obtenir un à la page [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Inscrivez-vous au programme de développement QQ

1. Accédez au [centre des développeurs QQ](http://open.qq.com) et connectez-vous avec les informations d’identification de votre compte QQ.
2. Une fois que vous êtes connecté, accédez à la page [http://open.qq.com/reg](http://open.qq.com/reg) pour vous inscrire en tant que développeur.
3. Dans le menu, sélectionnez **个人** (Développeur individuel).
4. Entrez les informations requises dans le formulaire, puis cliquez sur **下一步** (Étape suivante).
5. Finalisez le processus de vérification d’e-mail.

> [!NOTE]
> Une fois que vous êtes inscrit en tant que développeur, vous devez attendre votre approbation pendant quelques jours. 

### <a name="register-a-qq-application"></a>Inscrire une application QQ

1. Accédez à [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Cliquez sur **应用管理** (Gestion de l’application).
3. Cliquez sur **创建应用** (Création d’application).
4. Entrez les informations nécessaires relatives à l’application.
5. Cliquez sur **创建应用** (Création d’application).
6. Entrez les informations requises.
7. Pour le champ **授权回调域** (URL de rappel), entrez `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Par exemple, si votre `tenant_name` est contoso.onmicrosoft.com, définissez l’URL sur `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Cliquez sur **创建应用** (Création d’application).
9. Dans la page de confirmation, cliquez sur **应用管理** (Gestion des applications) pour retourner à la page de gestion des applications.
10. Cliquez sur **查看** (Afficher) en regard de l’application que vous venez de créer.
11. Cliquez sur **修改** (Modifier).
12. En haut de la page, copiez l’**ID de l’application** et la **clé d’application**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configuration de QQ en tant que fournisseur d'identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « QQ ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **QQ**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Entrez la **clé d’application** que vous avez copiée précédemment dans **ID client**.
8. Entrez le **secret d’application** que vous avez copié précédemment dans **Secret client**.
9. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration QQ.

