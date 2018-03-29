---
title: 'Azure Active Directory B2C : Multi-Factor Authentication | Microsoft Docs'
description: Activation de Multi-Factor Authentication dans des applications accessibles aux consommateurs sécurisées par Azure Active Directory B2C
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
ms.openlocfilehash: 0f560a02a51239cff0a7d4057ec7beb2f0f5c5c5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C : activation de Multi-Factor Authentication dans vos applications accessibles aux consommateurs
Azure Active Directory (Azure AD) B2C s’intègre directement à [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) pour vous permettre d’ajouter une deuxième couche de sécurité aux expériences d’inscription et de connexion dans vos applications accessibles aux consommateurs. Vous pouvez faire cela sans écrire une seule ligne de code. Actuellement, nous prenons en charge un appel téléphonique et un message texte pour la vérification. Si vous déjà créé des stratégies d’inscription et de connexion, vous pouvez toujours activer Multi-Factor Authentication.

> [!NOTE]
> Vous pouvez également activer Multi-Factor Authentication lors de la création de stratégies d’inscription et de connexion, pas seulement lors de la modification de stratégies existantes.
> 
> 

Cette fonctionnalité permettent aux applications de gérer des scénarios tels que le suivant :

* Vous n’avez pas besoin de Multi-Factor Authentication pour accéder à une application, mais en avez besoin pour accéder à une autre. Par exemple, le consommateur peut se connecter à une application d’assurance automobile avec un compte local ou social, mais il doit confirmer un numéro de téléphone avant d’accéder à l’application d’assurance domestique inscrite dans le même annuaire.
* Vous n’avez pas besoin de Multi-Factor Authentication pour accéder à une application en général, mais en avez besoin pour accéder à des portions sensibles de celle-ci. Par exemple, le consommateur peut se connecter à une application bancaire avec un compte local ou social pour consulter le solde de son compte, mais il doit confirmer un numéro de téléphone avant de tenter d’effectuer un virement.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modification de votre stratégie d’inscription pour activer Multi-Factor Authentication
1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Cliquez sur **Stratégies d'inscription**.
3. Ouvrez votre stratégie d’inscription (par exemple, « B2C_1_SiUp ») en cliquant dessus.
4. Cliquez sur **Multi-Factor Authentication**, puis affectez la valeur **Actif** à l’**État**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

La fonctionnalité « Exécuter maintenant » de la stratégie permet de vérifier l’expérience utilisateur. Vérifiez les éléments suivants :

Un compte de consommateur est créé dans votre annuaire avant l’étape Multi-Factor Authentication. Lors de cette étape, le consommateur est invité à fournir son numéro de téléphone et à le confirmer. Si la vérification réussit, le numéro de téléphone est associé au compte du consommateur en vue d’une utilisation ultérieure. Même si le consommateur annule ou abandonne, il peut être invité à confirmer à nouveau un numéro de téléphone lors de la prochaine connexion (avec Multi-Factor Authentication activée).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modification de votre stratégie de connexion pour activer Multi-Factor Authentication
1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Cliquez sur **Stratégies d’authentification**.
3. Ouvrez votre stratégie de connexion (par exemple, « B2C_1_SiIn ») en cliquant dessus. Cliquez sur **Modifier** dans la partie supérieure du panneau.
4. Cliquez sur **Multi-Factor Authentication**, puis affectez la valeur **Actif** à l’**État**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

La fonctionnalité « Exécuter maintenant » de la stratégie permet de vérifier l’expérience utilisateur. Vérifiez les éléments suivants :

Lorsque le consommateur se connecte (à l’aide d’un compte local ou social), si un numéro de téléphone vérifié est associé à son compte, il est invité à la confirmer. Si aucun numéro de téléphone n’est associé, le consommateur est invité à le fournir et à le confirmer. Si la vérification réussit, le numéro de téléphone est associé au compte du consommateur en vue d’une utilisation ultérieure.

## <a name="multi-factor-authentication-on-other-policies"></a>Authentification multifacteur sur d’autres stratégies
Comme décrit pour les stratégies d’inscription et de connexion ci-dessus, il est également possible d’activer l’authentification multifacteur sur les stratégies d’inscription ou d’authentification ainsi que sur les stratégies de réinitialisation de mot de passe. Cette fonctionnalité sera également disponible prochainement sur les stratégies de modification de profil.

