---
title: 'Azure Active Directory B2C : configuration Twitter | Microsoft Docs'
description: Proposez l’inscription et la connexion à des consommateurs disposant de comptes Twitter dans vos applications sécurisées par Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/06/2017
ms.author: davidmu
ms.openlocfilehash: ee2d82f8c90b88a898428973a1febaa21034a14f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Azure Active Directory B2C : Proposer l’inscription et la connexion à des consommateurs disposant de comptes Twitter

## <a name="create-a-twitter-application"></a>Création d'une application Twitter
Pour utiliser Twitter en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Twitter et lui fournir les paramètres appropriés. Pour ce faire, vous devez disposer d’un compte développeur Twitter. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://dev.twitter.com/](https://dev.twitter.com/).

1. Accédez au [site Web des développeurs Twitter](https://dev.twitter.com/) et connectez-vous à l’aide de vos informations d’identification.
2. Cliquez sur **My apps** (Mes applications sous **Tools & Support** (Outils et support), puis sur **Create New App** (Créer une application). 
3. Dans le formulaire, indiquez une valeur pour **Name** (Nom), **Description** (Description) et **Website** (Site Web).
4. Dans le champ **Callback URL** (URL de rappel), entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Assurez-vous de remplacer **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com).
5. Cochez la case pour accepter le **contrat pour les développeurs** et cliquez sur **Create your Twitter application** (Créer votre application Twitter).
6. Une fois l’application créée, cliquez sur **Keys and Access Tokens** (Clés et jetons d’accès).
7. Copiez les informations de **Consumer Key** (Clé du client) et de **Consumer Secret** (Secret du client). Vous aurez besoin de ces deux valeurs pour configurer Twitter en tant que fournisseur d’identité dans votre client.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurer Twitter en tant que fournisseur d’identité dans votre locataire
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** en haut du volet.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Twitter ».
5. Cliquez sur **Identity provider type** (Type de fournisseur d’identité), sélectionnez **Twitter**, puis cliquez sur **OK**.
6. Cliquez sur **Set up this identity provider** (Configurer ce fournisseur d’identité) et entrez la **clé du client** Twitter pour **l’ID du client** et le **secret du client** Twitter pour le **secret du client**.
7. Cliquez sur **OK**, puis sur **Create** (Créer) pour enregistrer votre configuration Twitter.

## <a name="next-steps"></a>Étapes suivantes

Créez ou modifiez une [stratégie intégrée](active-directory-b2c-reference-policies.md) et ajoutez Twitter comme fournisseur d’identité.