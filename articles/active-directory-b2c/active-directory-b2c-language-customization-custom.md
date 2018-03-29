---
title: 'Azure Active Directory B2C : personnalisation de la langue dans les stratégies personnalisées | Microsoft Docs'
description: Découvrez comment localiser le contenu dans les stratégies personnalisées pour plusieurs langues
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.openlocfilehash: 45cfa152615da1447cc695e0dd201e5b8d046cf4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="language-customization-in-custom-policies"></a>Personnalisation de la langue dans les stratégies personnalisées

> [!NOTE]
> Cette fonctionnalité est en version préliminaire publique.
> 

Dans les stratégies personnalisées,la personnalisation de la langue fonctionne de la même façon que dans les stratégies intégrées.  Consultez [documentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) intégrée qui décrit la façon dont une langue est choisie, basée sur les paramètres et les paramètres du navigateur.

## <a name="enable-supported-languages"></a>Activation des langues prises en charge
Si aucun paramètre ui_locales n’a été spécifié et que le navigateur de l’utilisateur demande une de ces langues, les langues prises en charge sont affichées à l’utilisateur.  

Les langues prises en charge sont définies dans `<BuildingBlocks>` dans le format suivant :

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

La langue par défaut et les langues prises en charge se comportent de la même façon que dans les stratégies intégrées.

## <a name="enable-custom-language-strings"></a>Activation des chaînes de langue personnalisée

La création de chaînes de langue personnalisée requiert deux étapes :
1. Modifier le `<ContentDefinition>` de la page pour spécifier un ID de ressource pour les langues de votre choix
2. Créer le `<LocalizedResources>` avec des ID correspondants dans votre `<BuildingBlocks>`

N’oubliez pas que vous pouvez placer un `<ContentDefinition>` et `<BuildingBlock>` dans votre fichier d’extension ou dans le fichier de stratégie de confiance. Cela dépend de si vous souhaitez que les modifications se fassent dans toutes vos stratégies héritées ou non.

### <a name="edit-the-contentdefinition-for-the-page"></a>Modifier la ContentDefinition de la page

Pour chaque page à localiser, vous pouvez spécifier dans le `<ContentDefinition>` les ressources de langue pour rechercher chaque code de langue.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

Dans cet exemple, les chaînes personnalisées Français (fr) et English (en) sont ajoutées à la page de connexion ou d’inscription unifiée.  Le `LocalizedResourcesReferenceId` pour chaque `LocalizedResourcesReference` est le même que pour les paramètres régionaux, mais vous pouvez utiliser n’importe quelle chaîne comme identificateur.  Pour chaque combinaison de langue et de page, vous devez créer un `<LocalizedResources>` correspondant, comme dans l’exemple suivant.


### <a name="create-the-localizedresources"></a>Créer le LocalizedResources

Vos remplacements sont contenus dans votre `<BuildingBlocks>` et qu’il existe un `<LocalizedResources>` pour chaque page et langue que vous avez spécifié dans le `<ContentDefinition>` pour chaque page.  Chaque remplacement est spécifiée comme un `<LocalizedString>` comme dans l’exemple suivant :

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Il existe quatre types d’éléments de chaîne dans la page :

**ClaimsProvider** - Étiquettes pour vos fournisseurs d’identité (Facebook, Google, Azure AD, etc.). **ClaimType** - Étiquettes pour vos attributs et leur texte d’aide correspondant, ou vos erreurs de validation de champ **UxElement** - D’autres éléments de chaîne sur la page qui se trouvent là par défaut, tels que des boutons, des liens ou du texte **ErrorMessage** - Messages d’erreur de validation de formulaire

Vérifiez que le `StringId`s correspond à la page pour laquelle vous utilisez ces remplacements, sinon elle sera bloquée par la validation de stratégie lors du téléchargement.  