---
title: "Azure AD Connect : authentification directe - mise à niveau des agents d’authentification | Microsoft Docs"
description: "Cet article décrit la mise à niveau de la configuration d’authentification directe de votre Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: f0a254b7216ca6fda40e26bafb7de57e796a5218
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory Connect : authentification directe - mise à niveau de la version préliminaire des agents d’authentification

## <a name="overview"></a>Vue d’ensemble

Cet article est destiné aux clients utilisant l’authentification directe Azure AD directe via l’aperçu. Nous avons récemment mis à niveau le logiciel de l’agent d’authentification (tout en changeant son nom). Vous devez procéder à la mise à niveau _manuelle_ de la version préliminaire des agents d’authentification sur vos serveurs locaux. Cette mise à niveau manuelle s’effectue une seule fois. Toutes les futures mises à jour des agents d’authentification sont automatiques. Vous pouvez effectuer la mise à niveau pour les raisons suivantes :

- La version préliminaire des agents d’authentification ne sera plus sécurisée ou ne recevra plus les correctifs de bogues.
-   La version préliminaire des agents d’authentification ne peut être installée sur des serveurs supplémentaires pour une haute disponibilité.

## <a name="check-versions-of-your-authentication-agents"></a>Vérifiez les versions de vos agents d’authentification

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Étape 1 : vérifier l’emplacement de l’installation de vos agents d’authentification

Suivez les étapes ci-après pour vérifier l’emplacement de l’installation de vos agents d’authentification :

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) à l’aide des informations d’identification d’administrateur général de votre locataire.
2. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche.
3. Sélectionnez ensuite **Azure AD Connect**. 
4. Sélectionnez **Authentification directe**. Ce panneau répertorie les serveurs sur lesquels vos agents d’authentification sont installés.

![Centre d’administration Azure Active Directory - panneau Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Étape 2 : vérifiez les versions de vos agents d’authentification

Pour vérifier les versions de vos Agents d’authentification sur chaque serveur identifié à l’étape précédente, suivez ces instructions :

1. Accédez à **panneau de configuration -> Programmes -> Programmes et fonctionnalités** sur le serveur local.
2. S’il existe une entrée pour «**Agent d’authentification Microsoft Azure AD Connect**», vous n’avez pas besoin d’entamer une action sur ce serveur.
3. S’il existe une entrée pour «**connecteur Proxy d’Application Microsoft Azure AD**», les versions 1.5.132.0 ou version antérieure, vous devez effectuer une mise à jour manuelle sur ce serveur.

![Version préliminaire de l’agent d’authentification](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Meilleures pratiques à suivre avant de commencer la mise à niveau

Avant la mise à niveau, vérifiez que les éléments suivants sont en place :

1. **Créer le compte d’administrateur général uniquement dans le cloud**: ne pas mettre à niveau sans avoir un compte d’administrateur général uniquement dans le cloud à utiliser en cas d’urgence, lorsque vos agents d’authentification directe ne fonctionnent pas correctement. Découvrez comment [ajouter un compte d’administrateur général de type cloud uniquement](../active-directory-users-create-azure-portal.md). Cette étape est essentielle pour éviter que votre locataire ne soit verrouillé.
2.  **Garantir une haute disponibilité** : si l’installation précédente a échoué, installez un deuxième agent d’authentification autonome pour fournir une haute disponibilité pour les demandes de connexion, à l’aide de ces [instructions](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Mise à niveau de l’agent d’authentification sur votre serveur Azure AD Connect

La mise à niveau d’Azure AD Connect doit précéder celle de l’agent d’authentification sur le même serveur. Sur votre serveur principal et intermédiaire Azure AD Connect, procédez comme suit :

1. **Mettre à niveau Azure AD Connect** : consultez cet [article](./active-directory-aadconnect-upgrade-previous-version.md) pour obtenir la version la plus récente d’Azure AD Connect.
2. **Désinstaller la version préliminaire de l’agent d’authentification** : téléchargez [ce script PowerShell](https://aka.ms/rmpreviewagent) et exécutez-le en tant qu’administrateur sur le serveur.
3. **Téléchargez la dernière version de l’agent d’authentification (version 1.5.193.0 ou ultérieure)** : connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) avec les droits d’administrateur général de votre locataire. Sélectionnez **Azure Active Directory -> Azure AD Connect -> authentification directe -> agent de téléchargement**. Acceptez les [conditions d’utilisation](https://aka.ms/authagenteula) et téléchargez la dernière version de l’agent d’authentification. Vous pouvez également télécharger l’agent d’authentification [ici](https://aka.ms/getauthagent).
4. **Installer la dernière version de l’agent d’authentification** : exécutez le fichier exécutable téléchargé à l’étape 3. Fournissez les informations d’identification de l’administrateur général de votre locataire lorsque vous y êtes invité.
5. **Vérifier que la version la plus récente a été installée** : comme indiqué précédemment, accédez à **Panneau de configuration -&gt; Programmes -&gt; Programmes et fonctionnalités** et vérifiez qu’il existe une entrée pour «**Agent d’authentification Microsoft Azure AD Connect**».

>[!NOTE]
>Si vous consultez le panneau Authentification directe dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) une fois les étapes précédentes effectuées, vous pouvez voir deux entrées Agent d’authentification par serveur : une entrée indiquant que l’agent d’authentification est **actif** et une autre indiquant qu’il est **inactif**. Ceci est _normal_. L’entrée indiquant **inactif** est automatiquement supprimée après quelques jours.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Mise à niveau de l’agent d’authentification sur d’autres serveurs

Procédez comme suit pour mettre à niveau les agents d’authentification sur d’autres serveurs (lorsqu’Azure AD Connect n’est pas installé) :

1. **Désinstaller la version préliminaire de l’agent d’authentification** : téléchargez [ce script PowerShell](https://aka.ms/rmpreviewagent) et exécutez-le en tant qu’administrateur sur le serveur.
2. **Téléchargez la dernière version de l’agent d’authentification (version 1.5.193.0 ou ultérieure)** : connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) avec les droits d’administrateur général de votre locataire. Sélectionnez **Azure Active Directory -> Azure AD Connect -> authentification directe -> agent de téléchargement**. Acceptez les conditions d’utilisation et téléchargez la dernière version.
3. **Installer la dernière version de l’agent d’authentification** : exécutez le fichier exécutable téléchargé à l’étape 2. Fournissez les informations d’identification de l’administrateur général de votre locataire lorsque vous y êtes invité.
4. **Vérifier que la version la plus récente a été installée** : comme indiqué précédemment, accédez à **Panneau de configuration -&gt; Programmes -&gt; Programmes et fonctionnalités** et vérifiez qu’il existe une entrée appelée **Agent d’authentification Microsoft Azure AD Connect**.

>[!NOTE]
>Si vous consultez le panneau Authentification directe dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) une fois les étapes précédentes effectuées, vous pouvez voir deux entrées Agent d’authentification par serveur : une entrée indiquant que l’agent d’authentification est **actif** et une autre indiquant qu’il est **inactif**. Ceci est _normal_. L’entrée indiquant **inactif** est automatiquement supprimée après quelques jours.

## <a name="next-steps"></a>Étapes suivantes
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec cette fonctionnalité.
