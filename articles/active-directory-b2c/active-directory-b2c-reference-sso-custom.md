---
title: Gestion de session d’authentification unique à l’aide de stratégies personnalisées - Azure AD B2C | Microsoft Docs
description: Découvrez comment gérer des sessions d’authentification unique (SSO) à l’aide de stratégies personnalisées dans Azure AD B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/20/2017
ms.author: davidmu
ms.openlocfilehash: 3d378d188d9acec06f37ca91e3c67e82fb31fc08
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C : gestion de session d’authentification unique (SSO)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C permet à un administrateur de contrôler la façon dont Azure AD B2C interagit avec un utilisateur après que celui-ci s’est authentifié. Cela est effectué via une gestion de session d’authentification unique. Par exemple, l’administrateur peut contrôler si la sélection des fournisseurs d’identité s’affiche, ou si des détails de compte local doivent être entrés à nouveau. Cet article décrit comment configurer les paramètres d’authentification unique pour Azure AD B2C.

## <a name="overview"></a>Vue d'ensemble

La gestion de session d’authentification unique comporte deux parties. La première a trait aux interactions de l’utilisateur directement avec Azure AD B2C, et la deuxième aux interactions de l’utilisateur avec des tiers externes, tels que Facebook. Azure AD B2C ne peut ni remplacer, ni contourner des sessions d’authentification unique qui pourraient être tenues par des parties externes. Au lieu de cela, l’itinéraire parcouru via Azure AD B2C pour accéder à la partie externe est « mémorisé », ce qui évite de devoir réinviter l’utilisateur à sélectionner son fournisseur d’identité sociale ou d’entreprise. La décision finale en lien avec l’authentification unique appartient au tiers externe.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

La gestion de session d’authentification unique utilise la même sémantique que tout autre profil technique dans des stratégies personnalisées. Quand une étape d’orchestration est exécutée, le profil technique associé à l’étape est interrogé pour obtenir une référence `UseTechnicalProfileForSessionManagement`. S’il en existe une, le fournisseur de la session de l’authentification unique référencé est vérifié pour voir si l’utilisateur est un participant de la session. Dans l’affirmative, le fournisseur de session d’authentification unique est utilisé pour remplir à nouveau la session. De même, lors de l’exécution d’une étape d’orchestration est terminée, le fournisseur est utilisé pour stocker les informations de la session si un fournisseur de session d’authentification unique a été spécifié.

Azure AD B2C a défini un certain nombre de fournisseurs de session d’authentification unique qui peuvent être utilisés :

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Les classes de gestion de l’authentification unique sont spécifiées à l’aide de l’élément `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` d’un profil technique.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Comme son nom l’indique, ce fournisseur ne fait rien. Ce fournisseur peut être utilisé pour supprimer un comportement d’authentification unique pour un profil technique spécifique.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Ce fournisseur peut être utilisé pour le stockage de revendications dans une session. Ce fournisseur est en général référencé dans un profil technique utilisé pour la gestion de comptes locaux. 

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Pour ajouter des revendications dans la session, utilisez l’élément `<PersistedClaims>` du profil technique. Lorsque le fournisseur est utilisé pour remplir à nouveau la session, les revendications persistantes sont ajoutées au jeu de revendications. `<OutputClaims>` est utilisé pour récupérer des revendications de la session.

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ce fournisseur est utilisé pour supprimer l’écran permettant de choisir le fournisseur d’identité. Il est généralement référencé dans un profil technique configuré pour un fournisseur d’identité externe, tel que Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ce fournisseur est utilisé pour gérer les sessions SAML d’Azure AD B2C entre applications ainsi que les fournisseurs d’identité SAML externes.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Le profil technique contient deux éléments de métadonnées :

| Item | Valeur par défaut | Valeurs possibles | Description
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true/false | Indique au fournisseur que l’index de la session doit être stocké. |
| RegisterServiceProviders | true | true/false | Indique que le fournisseur doit inscrire tous les fournisseurs de services SAML auxquels une assertion a été envoyée. |

Lorsque vous utilisez le fournisseur pour stocker une session de fournisseur d’identité SAML, les éléments ci-dessus doivent tous deux avoir la valeur false. Lorsque vous utilisez le fournisseur pour stocker la session SAML B2C, les éléments ci-dessus doivent avoir la valeur true. Cette valeur peut également être omise, car les valeurs de ces éléments sont true par défaut.

>[!NOTE]
> La fermeture de la session SAML requiert les valeurs `SessionIndex` et `NameID` pour aboutir.

## <a name="next-steps"></a>Étapes suivantes

Nous adorons que vous nous fassiez part de vos commentaires et suggestions. Si vous rencontrez des difficultés dans le cadre de cette rubrique, publiez un billet sur Stack Overflow en utilisant la mention [« azure-ad-b2c »](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Pour des demandes de fonctionnalités, votez pour celles-ci sur notre [Forum des commentaires](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

