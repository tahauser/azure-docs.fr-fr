---
title: 'Azure Active Directory B2C : résoudre les problèmes liés aux stratégies personnalisées | Microsoft Docs'
description: Découvrez les approches pour résoudre les erreurs quand vous utilisez des stratégies personnalisées dans Azure Active Directory.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/07/2017
ms.author: davidmu
ms.openlocfilehash: e51990bd2e121b89e7260e0d745d08ecc82ff8a0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Résoudre les problèmes liés aux stratégies personnalisées Azure AD B2C et à l’Infrastructure d’expérience d’identité.

Si vous utilisez des stratégies personnalisées Azure Active Directory B2C (Azure AD B2C), vous risquez de rencontrer des difficultés lors de la configuration de l’Infrastructure d’expérience d’identité dans son format XML de langage de stratégie.  Apprendre à écrire des stratégies personnalisées peut s’apparenter à l’apprentissage d’une nouvelle langue. Dans cet article, nous décrivons des outils et proposons des conseils qui peuvent vous aider à identifier et résoudre rapidement les problèmes. 

> [!NOTE]
> Cet article est axé sur la résolution des problèmes de configuration de stratégie personnalisée Azure AD B2C. Il ne traite pas de l’application par partie de confiance ou de sa bibliothèque d’identités.

## <a name="xml-editing"></a>Édition XML

L’erreur la plus courante lors de la configuration des stratégies personnalisées concerne la mise en forme du code XML. Un bon éditeur XML est quasiment essentiel. Un bon éditeur XML affiche le code XML en mode natif, applique un code couleur au contenu, prérenseigne les termes courants, maintient les éléments indexés et peut valider avec un schéma. Voici deux de nos éditeurs XML préférés :

* [Visual Studio Code](https://code.visualstudio.com/)
* [Notepad++](https://notepad-plus-plus.org/)

La validation du schéma XML identifie les erreurs avant le chargement de votre fichier XML. Dans le dossier racine du pack de démarrage, obtenez la définition du schéma XML TrustFrameworkPolicy_0.3.0.0.xsd. Pour plus d’informations, recherchez *outils XML* et *validation XML* dans la documentation de votre éditeur XML.

Il peut être utile de passer en revue les règles XML. Azure AD B2C rejette toute erreur de mise en forme XML qu’il détecte. Parfois, une mauvaise mise en forme XML peut entraîner des messages d’erreur trompeurs.

## <a name="upload-policies-and-policy-validation"></a>Chargement et validation de stratégies

 La validation du chargement de fichier XML est automatique. La plupart des erreurs provoquent l’échec du chargement. La validation inclut le fichier de stratégie que vous chargez. Elle inclut également la chaîne de fichiers à laquelle le fichier de chargement fait référence (le fichier de stratégie de la partie de confiance, le fichier d’extensions et le fichier de base). 
 
 Voici quelques-unes des erreurs de validation courantes.

Extrait de code de l’erreur :`... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* La valeur ClaimType est mal orthographiée ou n’existe pas dans le schéma.
* Les valeurs ClaimTypes doivent être définies dans au moins l’un des fichiers de la stratégie. 
    Par exemple : ` <ClaimType Id="socialIdpUserId">`
* Si la valeur ClaimType est définie dans le fichier d’extensions mais utilisée également dans une valeur TechnicalProfile du fichier de base, le chargement du fichier de base provoque une erreur.

Extrait de code de l’erreur :`...makes a reference to a ClaimsTransformation with id...`
* Les causes de l’erreur peuvent être les mêmes que pour l’erreur ClaimType.

Extrait de code de l’erreur :`Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* Vérifiez que la valeur TenantId dans les éléments  **\<TrustFrameworkPolicy\>** et **\<BasePolicy\>** correspondent à votre locataire Azure AD B2C cible.  

## <a name="troubleshoot-the-runtime"></a>Résoudre les problèmes au moment de l’exécution

* Utilisez `Run Now` et `https://jwt.io` pour tester vos stratégies indépendamment de votre application web ou mobile. Ce site web agit comme une application par partie de confiance. Il affiche le contenu du JSON Web Token (JWT) généré par votre stratégie Azure AD B2C. Pour créer une application de test dans l’Infrastructure d’expérience d’identité, utilisez les valeurs suivantes :
    * Nom : TestApp
    * Application/API web : Non
    * Client natif : Non

* Pour suivre l’échange de messages entre votre navigateur client et Azure AD B2C, utilisez [Fiddler](http://www.telerik.com/fiddler). Il peut vous aider à obtenir une indication de l’endroit où vos étapes d’orchestration échouent dans le parcours utilisateur.

* En **Mode de développement**, utilisez **Application Insights** pour suivre l’activité de votre parcours utilisateur d’Infrastructure d’expérience d’identité. En **Mode de développement**, vous pouvez observer l’échange de revendications entre l’Infrastructure d’expérience d’identité et les divers fournisseurs de revendications définis par les profils techniques, tels que les fournisseurs d’identité, les services basés sur les API, le répertoire utilisateur Azure AD B2C et d’autres services comme Azure Multi-Factor-Authentication.  

## <a name="recommended-practices"></a>Pratiques recommandées

**Conservez plusieurs versions de vos scénarios. Regroupez-les dans un projet avec votre application.** Les fichiers de base, d’extensions et de partie de confiance dépendent directement les uns des autres. Enregistrez-les en tant que groupe. À mesure que de nouvelles fonctionnalités sont ajoutées à vos stratégies, conservez des versions de travail distinctes. Organisez les versions de travail dans votre propre système de fichiers avec le code d’application avec lequel ils interagissent.  Vos applications peuvent appeler de nombreuses stratégies de partie de confiance différentes dans un locataire. Elles peuvent devenir dépendantes envers les revendications qu’elles attendent de vos stratégies Azure AD B2C.

**Développez et testez des profils techniques avec des parcours utilisateur connus.** Utilisez des stratégies de pack de démarrage testées pour configurer vos profils techniques. Testez-les séparément avant de les incorporer à vos propres parcours utilisateur.

**Développez et testez des parcours utilisateur avec des profils techniques testés.** Changez les étapes d’orchestration d’un parcours utilisateur de manière incrémentielle. Générez progressivement vos scénarios prévus.

## <a name="next-steps"></a>Étapes suivantes

* Dans GitHub, téléchargez le fichier .zip [active-directory-b2c-custom-policy-starterpack] (https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip).
