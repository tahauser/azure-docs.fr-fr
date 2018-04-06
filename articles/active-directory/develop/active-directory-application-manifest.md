---
title: Connaître le manifeste d’application Azure Active Directory | Microsoft Docs
description: Présentation détaillée de l’utilisation du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire Azure AD et permet de faciliter l’autorisation OAuth, le consentement et bien plus encore.
services: active-directory
documentationcenter: ''
author: sureshja
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: ee4e7eb6625cab274455ca787feeb7f267e11051
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-application-manifest"></a>Manifeste de l’application Azure Active Directory
Les applications qui s’intègrent à Azure AD doivent être enregistrées avec un locataire Azure AD. Cette application peut être configurée à l’aide du manifeste d’application (sous le panneau d’Azure AD) dans le [portail Azure](https://portal.azure.com).

## <a name="manifest-reference"></a>Référence du manifeste

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Clé  |Type de valeur |Exemple de valeur  |Description  |
|---------|---------|---------|---------|
|appID     |  Chaîne d’identificateur       |""|  Identificateur unique de l’application qui est affectée à une application par Azure AD.|
|appRoles     |    Type de tableau     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Collection de rôles qu’une application peut déclarer. Ces rôles peuvent être assignés aux utilisateurs, groupes ou principaux du service.|
|availableToOtherTenants|booléenne|`true`|Si cette valeur est définie sur true, l’application est disponible pour d’autres locataires.  Si la valeur est définie sur false, l’application n’est disponible que pour l’abonné dans laquelle il est enregistré.  Pour plus d’informations, consultez : [Comment connecter un utilisateur Azure Active Directory (AD) à l’aide du modèle d’application mutualisée](active-directory-devhowto-multi-tenant-overview.md). |
|displayName     |chaîne         |`MyRegisteredApp`         |Nom d’affichage de l’application. |
|errorURL     |chaîne         |`http://MyRegisteredAppError`         |URL pour les erreurs rencontrées dans une application. |
|groupMembershipClaims     |    chaîne     |    `1`     |   Un masque de bits qui configure la revendication des « groupes » émise dans un utilisateur ou un jeton d’accès OAuth 2.0 attendu par l’application. Les valeurs du masque de bits sont : 0 : aucun, 1 : groupes de sécurité et rôles Azure AD, 2 : réservé et 4 : réservé. Définir le masque de bits sur 7 permet d’obtenir tous les groupes de sécurité, les groupes de distribution et les rôles d’annuaire Azure AD dont l’utilisateur connecté est membre.      |
|optionalClaims     |  chaîne       |     `null`    |    Les [revendications facultatives](active-directory-optional-claims.md) retournées dans le jeton par le service d’émission de jeton de sécurité pour cette application spécifique.     |
|acceptMappedClaims    |      booléenne   | `true`        |    Si cette valeur est définie sur true, elle permet à une application d’utiliser le mappage des revendications sans spécifier une clé de signature personnalisée.|
|page d’accueil     |  chaîne       |`http://MyRegistererdApp`         |    URL vers la page d’accueil de l’application.     |
|identifierUris     |  Tableau de chaînes       | `http://MyRegistererdApp`        |   Les URI définis par l’utilisateur qui identifient de manière unique une application web au sein de son locataire Azure AD ou au sein d’un domaine personnalisé vérifié si l’application est multi-locataires.      |
|keyCredentials     |   Type de tableau      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Cette propriété comporte des références à des informations d’identification de l’application affectée, des secrets partagés basés sur chaîne et des certificats X.509.  Ces informations d’identification sont utilisées lors de la requête de jetons d’accès (lorsque l’application agit en tant que client plutôt qu’en tant que ressource).     |
|knownClientApplications     |     Type de tableau    |    [guid]     |     La valeur est utilisée pour le regroupement de consentement si vous avez une solution qui contient deux parties : une application cliente et une application d’API web personnalisée. Si vous entrez l’appID de l’application cliente dans cette valeur, l’utilisateur n’aura à donner son consentement à l’application cliente qu’une seule fois. Azure AD saura que donner son consentement au client signifie implicitement donner son consentement à l’API web et fournira automatiquement des principaux du service au client et à l’API web en même temps.  L’application client et de l’API web doit être enregistrée dans le même abonné.|
|logoutUrl     |   chaîne      |     `http://MyRegisteredAppLogout`    |   URL de déconnexion de l’application.      |
|oauth2AllowImplicitFlow     |   booléenne      |  `false`       |       Spécifie si cette application web peut exiger des jetons de flux OAuth2.0 implicites. La valeur par défaut est false. Cet indicateur est utilisé pour les applications basées sur un navigateur, telles que des applications d’une seule page Javascript. |
|oauth2AllowUrlPathMatching     |   booléenne      |  `false`       |   Spécifie si, dans le cadre de requêtes de jeton OAuth 2.0, Azure AD autorise un chemin d’accès correspondant pour l’URI de redirection par rapport aux replyUrls de l’application. La valeur par défaut est false.      |
|oauth2Permissions     | Type de tableau         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Collection d’étendues d’autorisation OAuth 2.0 que l’application d’API web (ressource) expose aux applications clientes. Ces étendues d’autorisation peuvent être accordées aux applications clientes durant le consentement. |
|oauth2RequiredPostResponse     | booléenne        |    `false`     |      Spécifie si, dans le cadre des requêtes de jeton OAuth 2.0, Azure AD autorise les requêtes POST, par opposition aux requêtes GET. La valeur par défaut est false, ce qui indique que seules les requêtes GET sont autorisées.   
|objectId     | Chaîne d’identificateur        |     ""    |    Identificateur unique de l’application dans le répertoire.  Cet ID n’est l’identificateur utilisé pour identifier l’application d’une transaction de protocole.  Il est utilisé pour référencer l’objet dans les requêtes de répertoire.|
|passwordCredentials     | Type de tableau        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Consultez la description de la propriété keyCredentials.     |
|publicClient     |  booléenne       |      `false`   | Spécifie si une application est un client public (comme une application installée s’exécutant sur un appareil mobile). La valeur par défaut est false.        |
|supportsConvergence     |  booléenne       |   `false`      | Cette propriété ne doit pas être modifiée.  Acceptez la valeur par défaut.        |
|replyUrls     |  Tableau de chaînes       |   `http://localhost`     |  Cette propriété à valeurs multiples contient la liste des valeurs de redirect_uri inscrites que Azure AD acceptera comme destinations lors du renvoi des jetons. |
|requiredResourceAccess     |     Type de tableau    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}]</code>    |   Spécifie les ressources auxquelles cette application souhaite accéder et l’ensemble des étendues d’autorisation OAuth et de rôles d’application dont elle a besoin sous chacune de ces ressources. Cette pré-configuration d’accès aux ressources nécessaires implique une expérience de consentement.|
|resourceAppId     |    Chaîne d’identificateur     |  ""      |   Identificateur unique pour la ressource à laquelle l’application souhaite accéder. Cette valeur doit être égale à l’appID déclaré sur l’application de ressources cible.     |
|resourceAccess     |  Type de tableau       | Consultez l’exemple de valeur de la propriété requiredResourceAccess.        |   La liste des étendues d’autorisation OAuth2.0 et des rôles d’application requise par l’application à partir de la ressource spécifiée (contient les valeurs d’ID et de type des ressources spécifiées)        |
|samlMetadataUrl    |chaîne| `http://MyRegisteredAppSAMLMetadata` |URL vers les métadonnées SAML pour l’application.| 

## <a name="next-steps"></a>Étapes suivantes
* Consultez la rubrique [Application et objets principal du service dans Azure AD][AAD-APP-OBJECTS] pour plus d’informations sur la relation existant entre les objets du principal du service et une application d’application.
* Consultez le [glossaire du développeur Azure AD][AAD-DEVELOPER-GLOSSARY] pour connaître les définitions de certains des principaux concepts de développement Azure Active Directory (AD).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

