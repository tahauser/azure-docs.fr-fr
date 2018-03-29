---
title: Complexité du mot de passe - Azure AD B2C | Microsoft Docs
description: Guide pratique pour configurer les exigences de complexité des mots de passe fournis par les consommateurs dans Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: b89a404d583e9d495b65180e145295e6a106b3ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C : configurer les exigences de complexité des mots de passe

> [!NOTE]
> **Cette fonctionnalité est en version préliminaire.**  Contactez [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) pour que votre locataire de test soit activé avec cette fonctionnalité.  Ne testez pas cette fonctionnalité sur des locataires de production.

Azure Active Directory B2C (Azure AD B2C) prend en charge la modification des exigences de complexité des mots de passe fournis par un utilisateur final à la création d’un compte.  Par défaut, Azure AD B2C utilise des mots de passe `Strong`.  Azure AD B2C prend également en charge les options de configuration permettant de contrôler la complexité des mots de passe utilisables par les clients.

## <a name="when-password-rules-are-enforced"></a>Quand les règles de mots de passe s’appliquent

Lors de l’inscription ou de la réinitialisation de mot de passe, un utilisateur final doit fournir un mot de passe qui respecte les règles de complexité.  Les règles de complexité du mot de passe s’appliquent stratégie par stratégie.  Il est possible qu’une stratégie demande un code confidentiel à quatre chiffres et une autre une chaîne de huit caractères à l’inscription.  Par exemple, vous pouvez utiliser une stratégie avec différents niveaux de complexité du mot de passe pour les adultes et pour les enfants.

La complexité du mot de passe ne s’applique jamais lors de la connexion.  Les utilisateurs ne sont jamais invités à modifier leur mot de passe parce qu’il ne répond pas à l’exigence de complexité actuelle.

Voici les types de stratégies pour lesquels il est possible de configurer la complexité du mot de passe :

* stratégie d’inscription ou de connexion ;
* stratégie de réinitialisation de mot de passe ;
* stratégie personnalisée ([Configurer la complexité du mot de passe dans une stratégie personnalisée](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="how-to-configure-password-complexity"></a>Configurer la complexité du mot de passe

1. Suivez ces étapes pour [accéder aux paramètres d’Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Ouvrez **Stratégies d’inscription ou de connexion**.
1. Sélectionnez une stratégie, puis cliquez sur **Modifier**.
1. Ouvrez **Complexité du mot de passe**.
1. Modifiez la complexité du mot de passe de cette stratégie : **Simple**, **Fort** ou **Personnalisé**.

### <a name="comparison-chart"></a>Tableau comparatif

| Complexité | Description |
| --- | --- |
| Simple | Un mot de passe entre 8 et 64 caractères. |
| Remarque | Un mot de passe entre 8 et 64 caractères. Trois de ces quatre types d’éléments : minuscules, majuscules, chiffres ou symboles. |
| Personnalisée | C’est l’option qui fournit le contrôle le plus poussé sur les règles de complexité du mot de passe.  Elle permet de configurer une longueur personnalisée,  et d’accepter les mots de passe composés exclusivement de chiffres (codes confidentiels). |

## <a name="options-available-under-custom"></a>Options disponibles dans le mode personnalisé

### <a name="character-set"></a>Jeu de caractères

Permet d’accepter des chiffres uniquement (code confidentiel) ou la totalité du jeu de caractères.

* **Numbers only** (Chiffres uniquement) autorise la saisie de mots de passe composés exclusivement de chiffres (0-9).
* **All** (Tout) accepte toutes les lettres, tous les chiffres et tous les symboles.

### <a name="length"></a>Longueur

Permet de contrôler les critères de longueur du mot de passe.

* **Minimum Length** (Longueur minimale) doit avoir au moins la valeur 4.
* **Maximum Length** (Longueur maximale) doit avoir une valeur supérieure ou égale à la longueur minimale, au maximum 64 caractères.

### <a name="character-classes"></a>Classes de caractères

Permet de contrôler les différents types de caractères utilisés dans le mot de passe.

* **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (2 sur 4 : minuscule, majuscule, chiffre (0-9), symbole) garantit que le mot de passe contient au moins deux types de caractères, par exemple, un nombre et une minuscule.
* **3 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (3 sur 4 : minuscule, majuscule, chiffre (0-9), symbole) garantit que le mot de passe contient au moins trois types de caractères, par exemple, un chiffre, une minuscule et une majuscule.
* **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** (4 sur 4 : minuscule, majuscule, chiffre (0-9), symbole) garantit que le mot de passe contient les quatre types de caractères.

    > [!NOTE]
    > La condition **4 sur 4** peut contrarier l’utilisateur final. Des études ont montré qu’elle n’améliore pas l’entropie du mot de passe. Consultez les [Recommandations du NIST en matière de mots de passe](https://pages.nist.gov/800-63-3/sp800-63b.html#appA).
