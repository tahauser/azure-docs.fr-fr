---
title: Complexité du mot de passe dans les stratégies personnalisées - Azure AD B2C | Microsoft Docs
description: Guide pratique pour configurer les exigences de complexité des mots de passe dans une stratégie personnalisée
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
ms.openlocfilehash: 4dabcdef32d337c5063d2b0503f32b06d5d0fdeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>Configurer la complexité du mot de passe dans les stratégies personnalisées

> [!NOTE]
> **Cette fonctionnalité est en version préliminaire.**  Contactez [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) pour que votre locataire de test soit activé avec cette fonctionnalité.  Ne testez pas cette fonctionnalité sur des locataires de production.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article offre une description avancée du fonctionnement de la complexité du mot de passe et de son activation grâce aux stratégies personnalisées Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C : configurer les exigences de complexité des mots de passe

Azure Active Directory B2C (Azure AD B2C) prend en charge la modification des exigences de complexité des mots de passe fournis par un utilisateur final à la création d’un compte.  Par défaut, Azure AD B2C utilise des mots de passe **forts**.  Azure AD B2C prend également en charge les options de configuration permettant de contrôler la complexité des mots de passe utilisables par les clients.  Cet article explique comment configurer la complexité du mot de passe dans les stratégies personnalisées.  Il est également possible de [configurer la complexité du mot de passe dans les stratégies intégrées](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Prérequis


Un locataire Azure AD B2C configuré pour effectuer une inscription/connexion à un compte local, comme décrit dans [Bien démarrer](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Guide pratique pour configurer la complexité du mot de passe dans une stratégie personnalisée

Pour configurer la complexité du mot de passe dans une stratégie personnalisée, la structure globale de votre stratégie personnalisée doit inclure un élément `ClaimsSchema`, `Predicates` et `InputValidations` à l’intérieur de `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

L’objectif de ces éléments est le suivant :

- Chaque élément `Predicate` définit une vérification de base des chaînes qui retourne true ou false.
- L’élément `InputValidations` contient un ou plusieurs éléments `InputValidation`.  Chaque `InputValidation` est construit à l’aide d’une série d’éléments `Predicate`. Cet élément permet d’effectuer des agrégations booléennes (comme `and` et `or`).
- Le `ClaimsSchema` définit les revendications à valider,  puis la règle `InputValidation` à utiliser pour cela.

### <a name="defining-a-predicate-element"></a>Définir un élément de prédicat

Les prédicats ont deux types de méthodes : IsLengthRange et MatchesRegex. Examinons un exemple de chaque.  Nous avons tout d’abord un exemple de MatchesRegex, utilisé pour correspondre à une expression régulière.  Ici, il correspond à une chaîne qui contient des nombres.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Maintenant, examinons un exemple d’IsLengthRange.  Cette méthode prend une longueur de chaîne minimale et maximale.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Utilisez l’attribut `HelpText` pour fournir un message d’erreur aux utilisateurs finaux en cas d’échec de la vérification.  Il est possible de localiser cette chaîne grâce à la [fonctionnalité de personnalisation du langage](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Définir un élément InputValidation

Un `InputValidation` est une agrégation de `PredicateReferences`. Chaque `PredicateReferences` doit être remplie pour que `InputValidation` réussisse.  Toutefois, à l’intérieur de l’élément `PredicateReferences`, utilisez un attribut nommé `MatchAtLeast` pour spécifier le nombre de vérifications `PredicateReference` qui doivent retourner true.  Si vous le souhaitez, vous pouvez définir un attribut `HelpText` pour remplacer le message d’erreur défini dans les éléments `Predicate` auxquels il fait référence.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Définir un élément ClaimsSchema

Les types de revendications `newPassword` et `reenterPassword` sont considérés comme spéciaux : ne modifiez pas les noms.  L’interface utilisateur valide que l’utilisateur a correctement entré de nouveau son mot de passe à la création du compte, en fonction de ces éléments `ClaimType`.  Pour trouver les mêmes éléments `ClaimType`, recherchez dans TrustFrameworkBase.xml, dans votre pack de démarrage.  La nouveauté de cet exemple est que nous remplaçons ces éléments pour définir un `InputValidationReference`. L’attribut `ID` de ce nouvel élément pointe vers l’élément `InputValidation` que nous avons défini.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Exemple complet

Cet exemple montre comment tous les éléments s’articulent pour former une stratégie fonctionnelle.  Pour utiliser cet exemple :

1. Suivez les instructions des prérequis [Prise en main](active-directory-b2c-get-started-custom.md) pour télécharger, configurer et charger TrustFrameworkBase.xml et TrustFrameworkExtensions.xml.
1. Créez un fichier SignUporSignIn.xml avec le contenu de l’exemple de cette section.
1. Mettez à jour SignUporSignIn.xml en remplaçant `yourtenant` par le nom de votre client Azure AD B2C.
1. Enfin, chargez le fichier de stratégie SignUporSignIn.xml.

Cet exemple contient une validation pour les codes confidentiels et une autre pour les mots de passe forts :

- Recherchez `PINpassword`. Cet élément `InputValidation` valide un code confidentiel quelle que soit sa longueur.  Il n’est pas utilisé pour le moment, car l’élément `InputValidationReference` n’y fait pas référence à l’intérieur de `ClaimType`. 
- Recherchez `PasswordValidation`. Cet élément `InputValidation` valide un mot de passe qui contient entre 8 et 16 caractères et au moins trois de ces quatre types d’éléments : chiffres, majuscules, minuscules ou symboles.  `ClaimType` y fait référence.  Par conséquent, la règle s’applique dans cette stratégie.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
