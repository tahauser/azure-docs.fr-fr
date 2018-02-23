---
title: "Configuration de fournisseur d’identité GitHub - Azure AD B2C | Microsoft Docs"
description: "Fournissez une inscription et une connexion à des clients disposant de comptes GitHub dans vos applications sécurisées par Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 5518e135-36b3-4f86-9a01-be25f021ed22
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parja
ms.openlocfilehash: e827bea257309f6d6dda1af4e68ccda5a26d30ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Azure Active Directory B2C : fournir une inscription et une connexion à des consommateurs disposant de comptes GitHub

> [!NOTE]
> Cette fonctionnalité est en préversion.
> 

Cet article explique comment activer la connexion pour les utilisateurs disposant d’un compte GitHub.

## <a name="create-a-github-oauth-application"></a>Créer une application GitHub OAuth

Pour utiliser GitHub en tant que fournisseur d’identité dans Azure AD B2C, vous devez créer une application GitHub OAuth et fournir à celle-ci les paramètres appropriés.

1. Une fois connecté à GitHub, accédez aux [paramètres du développeur GitHub](https://github.com/settings/developers).
1. Cliquez sur **Nouvelle application OAuth**
1. Entrez le **Nom de l’application** et l’**URL de la page d’accueil**.
1. Pour l’**URL de rappel d’autorisation**, entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Remplacez **{tenant}** par le nom de votre client Azure AD B2C (par exemple B2C.onmicrosoft.com).

    >[!NOTE]
    >La valeur de « client » doit être en minuscules dans **URL de connexion**.

1. Cliquez sur **Inscrire l’application**.
1. Enregistrez les valeurs **ID client** et **Clé secrète client**. Vous en aurez besoin dans la section suivante.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Configurer GitHub en tant que fournisseur d’identité dans votre client Azure AD B2C

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) sur le portail Azure.
1. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
1. Cliquez sur **+Ajouter** en haut du volet.
1. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « GitHub ».
1. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **GitHub**, puis cliquez sur **OK**.
1. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’ID client et la clé secrète client de l’application GitHub OAuth que vous avez copiée précédemment.
1. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration GitHub.

## <a name="next-steps"></a>Étapes suivantes

Créez ou modifiez une [stratégie intégrée](active-directory-b2c-reference-policies.md) et ajoutez GitHub comme fournisseur d’identité.