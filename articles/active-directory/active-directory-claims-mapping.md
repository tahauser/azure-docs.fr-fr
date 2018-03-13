---
title: "Mappage de revendications dans Azure Active Directory (préversion publique) | Microsoft Docs"
description: "Cette page décrit le mappage de revendications Azure Active Directory."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: 1bc669dfa5a41e38b35751af62560ff650575a08
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Mappage de revendications dans Azure Active Directory (préversion publique)

>[!NOTE]
>Cette fonctionnalité remplace la [personnalisation des revendications](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) offerte au moyen du portail aujourd'hui. Si vous personnalisez des revendications à l’aide du portail en plus de la méthode Graph/PowerShell détaillée dans ce document sur la même application, les jetons émis pour celle-ci ignorent la configuration définie dans le portail.
Les configurations effectuées au moyen des méthodes décrites dans ce document n’apparaissent pas dans le portail.

Les administrateurs de locataire utilisent cette fonctionnalité pour personnaliser les revendications émises dans des jetons pour une application spécifique dans leur locataire. Vous pouvez utiliser des stratégies de mappage de revendications pour effectuer les opérations suivantes :

- sélectionner les revendications incluses dans les jetons ;
- créer des types de revendications inexistants ;
- choisir ou modifier la source des données émises dans des revendications spécifiques.

>[!NOTE]
>Cette fonctionnalité est actuellement disponible en préversion publique. Soyez prêt à rétablir ou à supprimer les modifications. La fonctionnalité est disponible dans tout abonnement Azure Active Directory (Azure AD) durant la période de préversion publique. Toutefois, lorsque la fonctionnalité sera généralement disponible, il se peut que certains de ses aspects nécessitent un abonnement Azure Active Directory Premium.

## <a name="claims-mapping-policy-type"></a>Type de stratégie de mappage de revendications
Dans Azure AD, un objet de **stratégie** représente un ensemble de règles appliquées à des applications individuelles ou à toutes les applications d’une organisation. Chaque type de stratégie présente une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels elles sont affectées.

Une stratégie de mappage de revendications est un type d’objet de **stratégie** qui modifie les revendications comprises dans les jetons émis pour des applications spécifiques.

## <a name="claim-sets"></a>Ensembles de revendications
Il existe des ensembles de revendications qui définissent comment et quand ils sont utilisés dans des jetons.

### <a name="core-claim-set"></a>Ensemble de revendications principal
Les revendications dans l’ensemble de revendications principal sont présentes dans chaque jeton, quelle que soit la stratégie. Ces revendications sont également considérées comme restreintes, et ne peuvent pas être modifiées.

### <a name="basic-claim-set"></a>Ensemble de revendications de base
L’ensemble de revendications de base inclut les revendications émises par défaut pour les jetons (en plus de l’ensemble de revendications principal). Ces revendications peuvent être omises ou modifiées à l’aide des stratégies de mappage de revendications.

### <a name="restricted-claim-set"></a>Ensemble de revendications restreint
Les revendications restreintes ne peuvent pas être modifiées à l’aide d’une stratégie. La source de données ne peut pas être modifiée, et aucune transformation n’est appliquée lors de la génération de ces revendications.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tableau 1 : ensemble de revendications restreint JSON Web Token (JWT)
|Type de revendication (nom)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|acr|
|actor|
|actortoken|
|aio|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|appid|
|appidacr|
|assertion|
|at_hash|
|aud|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|code|
|controls|
|credential_keys|
|csr|
|csr_type|
|deviceid|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|email|
|endpoint|
|enfpolids|
|exp|
|expires_on|
|grant_type|
|graph|
|group_sids|
|groups|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier|
|iat|
|identityprovider|
|idp|
|in_corp|
|instance|
|ipaddr|
|isbrowserhostedapp|
|iss|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|nbf|
|netbios_name|
|nonce|
|oid|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|password|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|puid|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|resource|
|role|
|roles|
|scope|
|scp|
|sid|
|signature|
|signin_state|
|src1|
|src2|
|sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|tid|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|upn|
|user_setting_sync_url|
|username|
|uti|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tableau 2 : ensemble de revendications restreint Security Assertion Markup Language (SAML)
|Type de revendication (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/expired|
|http://schemas.microsoft.com/identity/claims/accesstoken|
|http://schemas.microsoft.com/identity/claims/openid2_id|
|http://schemas.microsoft.com/identity/claims/identityprovider|
|http://schemas.microsoft.com/identity/claims/objectidentifier|
|http://schemas.microsoft.com/identity/claims/puid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
|http://schemas.microsoft.com/identity/claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groups|
|http://schemas.microsoft.com/claims/groups.link|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/role|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn|
|http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier|
|http://schemas.microsoft.com/identity/claims/scope|

## <a name="claims-mapping-policy-properties"></a>Propriétés de stratégie de mappage de revendications
Les propriétés d’une stratégie de mappage de revendications permettent de contrôler les revendications émises et l’origine des données. Si aucune stratégie n’est définie, le système émet des jetons contenant l’ensemble de revendications principal, l’ensemble de revendications de base et des revendications facultatives que l’application a choisi de recevoir.

### <a name="include-basic-claim-set"></a>Ensemble de revendications de base Include

**Chaîne :** IncludeBasicClaimSet

**Type de données :** valeur booléenne (True ou False)

**Résumé :** cette propriété détermine si l’ensemble de revendications de base est inclus dans les jetons affectés par cette stratégie. 

- Si la valeur est True, toutes les revendications de l’ensemble de revendications de base sont émises dans les jetons affectés par la stratégie. 
- Si la valeur est False, les revendications de l’ensemble de revendications de base ne figurent pas dans les jetons, sauf si elles sont ajoutées individuellement à la propriété de schéma de revendications de la même stratégie.

>[!NOTE] 
>Les revendications de l’ensemble de revendications principal sont présentes dans chaque jeton, indépendamment de la définition de cette propriété. 

### <a name="claims-schema"></a>Schéma de revendications

**Chaîne :** ClaimsSchema

**Type de données :** objet blob JSON avec une ou plusieurs entrées de schéma de revendication

**Résumé :** cette propriété définit les revendications présentes dans les jetons affectés par la stratégie, en plus de l’ensemble de revendications de base et de l’ensemble de revendications principal.
Pour chaque entrée de schéma de revendication définie dans cette propriété, certaines informations sont requises. Vous devez spécifier l’origine des données (**Value** ou **Paire Source/ID**), et la revendication à laquelle les données ont trait (**Type de revendication**).

### <a name="claim-schema-entry-elements"></a>Éléments d’entrée du schéma de revendication

**Value :** l’élément Value définit une valeur statique en tant que données à émettre dans la revendication.

**Paire Source/ID :** les éléments Source et ID définissent la provenance des données de la revendication. 

L’élément Source doit être l’un des suivants : 


- « user » : les données de la revendication sont une propriété définie sur l’objet User. 
- « application » : les données de la revendication sont une propriété définie sur le principal du service de l’application (client). 
- « resource » : les données de la revendication sont une propriété définie sur le principal du service de la ressource.
- « audience » : les données de la revendication sont une propriété définie sur le principal du service qui est l’audience du jeton (principal du service du client ou de la ressource ).
- « company » : les données de la revendication sont une propriété définie sur l’objet Company du client de la ressource.
- « transformation » : les données de la revendication proviennent d’une transformation de revendications (voir la section « Transformation de revendications » plus loin dans cet article). 

Si la source est une transformation, l’élément **TransformationID** doit également être inclus dans cette définition de revendication.

L’élément ID identifie la propriété définie sur la source qui fournit la valeur de la revendication. Le tableau suivant répertorie les valeurs d’ID valides pour chaque valeur de Source.

#### <a name="table-3-valid-id-values-per-source"></a>Tableau 3 : valeurs d’ID valides par source
|Source|ID|Description|
|-----|-----|-----|
|Utilisateur|surname|Nom de famille|
|Utilisateur|givenname|Prénom|
|Utilisateur|displayname|Nom d’affichage|
|Utilisateur|objectid|ObjectID|
|Utilisateur|mail|Adresse de messagerie|
|Utilisateur|userPrincipalName|Nom d’utilisateur principal|
|Utilisateur|department|department|
|Utilisateur|onpremisessamaccountname|Nom de compte Sam local|
|Utilisateur|netbiosname|Nom NetBios|
|Utilisateur|dnsdomainname|Nom de domaine DNS|
|Utilisateur|onpremisesecurityidentifier|Identificateur de sécurité local|
|Utilisateur|companyname|Nom de l’organisation|
|Utilisateur|streetaddress|Adresse postale|
|Utilisateur|postalcode|Code postal|
|Utilisateur|preferredlanguange|Langue par défaut|
|Utilisateur|onpremisesuserprincipalname|UPN local|
|Utilisateur|mailNickName|pseudonyme de messagerie|
|Utilisateur|extensionattribute1|Attribut d’extension 1|
|Utilisateur|extensionattribute2|Attribut d’extension 2|
|Utilisateur|extensionattribute3|Attribut d’extension 3|
|Utilisateur|extensionattribute4|Attribut d’extension 4|
|Utilisateur|extensionattribute5|Attribut d’extension 5|
|Utilisateur|extensionattribute6|Attribut d’extension 6|
|Utilisateur|extensionattribute7|Attribut d’extension 7|
|Utilisateur|extensionattribute8|Attribut d’extension 8|
|Utilisateur|extensionattribute9|Attribut d’extension 9|
|Utilisateur|extensionattribute10|Attribut d’extension 10|
|Utilisateur|extensionattribute11|Attribut d’extension 11|
|Utilisateur|extensionattribute12|Attribut d’extension 12|
|Utilisateur|extensionattribute13|Attribut d’extension 13|
|Utilisateur|extensionattribute14|Attribut d’extension 14|
|Utilisateur|extensionattribute15|Attribut d’extension 15|
|Utilisateur|othermail|Autre adresse e-mail|
|Utilisateur|country|Pays|
|Utilisateur|city|City|
|Utilisateur|state|État|
|Utilisateur|jobtitle|Poste|
|Utilisateur|employeeid|ID d’employé|
|Utilisateur|facsimiletelephonenumber|Numéro de télécopie|
|application, ressource, audience|displayname|Nom d’affichage|
|application, ressource, audience|objected|ObjectID|
|application, ressource, audience|tags|Balise de principal du service|
|Company|tenantcountry|Pays du locataire|

**TransformationID :** l’élément TransformationID doit être fourni uniquement si l’élément Source est défini sur « transformation ».

- Cet élément doit correspondre à l’élément d’ID de l’entrée de transformation dans la propriété **ClaimsTransformation** qui définit la façon dont les données de cette revendication sont générées.

**Type de revendication :** les éléments **JwtClaimType** et **SamlClaimType** définissent la revendication à laquelle cette entrée de schéma de revendication fait référence.

- L’élément JwtClaimType doit contenir le nom de la revendication que les jetons JWT doivent émettre.
- L’élément SamlClaimType doit contenir l’URI de la revendication que les jetons SAML doivent émettre.

>[!NOTE]
>Les noms et URI des revendications dans l’ensemble de revendications restreint ne peuvent pas être utilisés pour les éléments de type de revendication. Pour plus d’informations, consultez la section « Exceptions et restrictions » plus loin dans cet article.

### <a name="claims-transformation"></a>Transformation de revendications

**Chaîne :** ClaimsTransformation

**Type de données :** blob JSON avec une ou plusieurs entrées de transformation 

**Résumé :** cette propriété permet d’appliquer des transformations communes à des données sources afin de générer les données de sortie pour les revendications spécifiées dans le schéma de revendications.

**ID :** l’élément ID permet de faire référence à cette entrée de transformation dans l’entrée de schéma de revendication TransformationID. Cette valeur doit être unique pour chaque entrée de transformation au sein de cette stratégie.

**TransformationMethod :** l’élément TransformationMethod identifie l’opération effectuée pour générer les données de la revendication.

Selon la méthode choisie, un ensemble d’entrées et sorties est attendu. Celles-ci sont définies à l’aide des éléments **InputClaims**, **InputParameters** et **OutputClaims**.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tableau 4 : méthodes de transformation et entrées et sorties attendues
|Méthode de transformation|Entrée attendue|Sortie attendue|Description|
|-----|-----|-----|-----|
|Join|string1, string2, séparateur|outputClaim|Joint les chaînes d’entrée à l’aide d’un séparateur. Par exemple : string1:"foo@bar.com", string2:"sandbox", separator:"." produit outputClaim:"foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Extrait la partie locale d’une adresse de courrier. Par exemple : mail:"foo@bar.com" produit outputClaim:"foo". Si aucun signe @ n’est présent, la chaîne d’entrée originale est retournée telle quelle.|

**InputClaims :** un élément InputClaims permet de transmettre les données d’une entrée de schéma de revendication à une transformation. Il possède deux attributs : **ClaimTypeReferenceId** et **TransformationClaimType**.

- L’attribut **ClaimTypeReferenceId** est joint à l’élément ID de l’entrée de schéma de revendication pour rechercher la revendication d’entrée appropriée. 
- L’attribut **TransformationClaimType** est utilisé pour donner un nom unique à cette entrée. Ce nom doit correspondre à l’une des entrées attendues pour la méthode de transformation.

**InputParameters :** un élément InputParameters permet de transmettre une valeur constante à une transformation. Il possède deux attributs : **Value** et **ID**.

- L’attribut **Value** est la valeur de constante réelle à transmettre.
- L’attribut **ID** est utilisé pour donner un nom unique à cette entrée. Ce nom doit correspondre à l’une des entrées attendues pour la méthode de transformation.

**OutputClaims :** un élément OutputClaims permet conserver les données générées par une transformation, et de les lier à une entrée de schéma de revendication. Il possède deux attributs : **ClaimTypeReferenceId** et **TransformationClaimType**.

- L’attribut **ClaimTypeReferenceId** est joint à l’élément ID de l’entrée de schéma de revendication pour rechercher la revendication de sortie appropriée.
- L’attribut **TransformationClaimType** est utilisé pour donner un nom unique à cette sortie. Ce nom doit correspondre à l’une des sorties attendues pour la méthode de transformation.

### <a name="exceptions-and-restrictions"></a>Exceptions et restrictions

**NameID et UPN SAML :** les attributs à partir desquels vous obtenez les valeurs NameID et UPN ainsi que les transformations de revendications autorisées sont limités.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tableau 5 : attributs autorisés en tant que sources de données pour NameID SAML
|Source|ID|Description|
|-----|-----|-----|
|Utilisateur|mail|Adresse de messagerie|
|Utilisateur|userPrincipalName|Nom d’utilisateur principal|
|Utilisateur|onpremisessamaccountname|Nom de compte Sam local|
|Utilisateur|employeeid|ID d’employé|
|Utilisateur|extensionattribute1|Attribut d’extension 1|
|Utilisateur|extensionattribute2|Attribut d’extension 2|
|Utilisateur|extensionattribute3|Attribut d’extension 3|
|Utilisateur|extensionattribute4|Attribut d’extension 4|
|Utilisateur|extensionattribute5|Attribut d’extension 5|
|Utilisateur|extensionattribute6|Attribut d’extension 6|
|Utilisateur|extensionattribute7|Attribut d’extension 7|
|Utilisateur|extensionattribute8|Attribut d’extension 8|
|Utilisateur|extensionattribute9|Attribut d’extension 9|
|Utilisateur|extensionattribute10|Attribut d’extension 10|
|Utilisateur|extensionattribute11|Attribut d’extension 11|
|Utilisateur|extensionattribute12|Attribut d’extension 12|
|Utilisateur|extensionattribute13|Attribut d’extension 13|
|Utilisateur|extensionattribute14|Attribut d’extension 14|
|Utilisateur|extensionattribute15|Attribut d’extension 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tableau 6 : méthodes de transformation autorisées pour NameID SAML
|Méthode de transformation|Restrictions|
| ----- | ----- |
|ExtractMailPrefix|Aucun|
|Join|Le suffixe joint doit être un domaine vérifié du locataire de ressources.|

### <a name="custom-signing-key"></a>Clé de signature personnalisée
Une clé de signature personnalisée doit être affectée à l’objet de principal du service pour qu’une stratégie de mappage de revendications entre en vigueur. Tous les jetons émis qui ont été affectés par la stratégie sont signés avec cette clé. Les applications doivent être configurées pour accepter les jetons signés avec cette clé. Cela garantit la reconnaissance que les jetons ont été modifiés par le créateur de la stratégie de mappage de revendications. Cela protège les applications contre des stratégies de mappage de revendications créées par des acteurs malveillants.

### <a name="cross-tenant-scenarios"></a>Scénarios inter-locataires
Les stratégies de mappage de revendications ne s’appliquent pas aux utilisateurs invités. Si un utilisateur invité tente d’accéder à une application avec une stratégie de mappage de revendications assignée à son principal du service, le jeton par défaut est émis (la stratégie est sans effet).

## <a name="claims-mapping-policy-assignment"></a>Attribution de stratégie de mappage de revendications
Des stratégies de mappage de revendications peuvent être attribuées uniquement à des objets de principal du service.

### <a name="example-claims-mapping-policies"></a>Exemples de stratégies de mappage de revendications

Dans Azure AD, de nombreux scénarios sont possibles où vous pouvez personnaliser des revendications émises dans des jetons pour des principaux du service spécifiques. Cette section décrit quelques scénarios courants qui peuvent vous aider à comprendre comment utiliser le type de stratégie de mappage de revendications.

#### <a name="prerequisites"></a>Conditions préalables
Dans les exemples suivants, vous créez, mettez à jour, liez et supprimez des stratégies pour les principaux du service. Si vous débutez avec Azure AD, nous vous recommandons de vous documenter sur l’obtention d’un locataire Azure Active Directory avant de continuer avec ces exemples. 

Pour commencer, suivez les étapes ci-dessous :


1. Téléchargez la dernière [préversion publique du module Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Exécutez la commande Connect pour vous connecter à votre compte d'administrateur Azure AD. Exécutez cette commande chaque fois que vous démarrez une nouvelle session.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Pour afficher toutes les stratégies qui ont été créées dans votre organisation, exécutez la commande suivante. Nous vous conseillons d’exécuter cette commande après la plupart des opérations dans les scénarios suivants afin de vérifier que vos stratégies sont créées comme prévu.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemple : créer et attribuer une stratégie pour omettre les revendications de base des jetons émis pour un principal du service.
Dans cet exemple, vous créez une stratégie qui supprime l’ensemble de revendications de base des jetons émis pour des principaux du service liés.


1. Créez une stratégie de mappage de revendications. Cette stratégie, liée à des principaux du service spécifiques, supprime l’ensemble de revendications de base des jetons.
    1. Pour créer la stratégie, exécutez la commande suivante : 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service. 
    1.  Pour afficher tous les principaux du service de votre organisation, vous pouvez interroger Microsoft Graph. Ou bien, dans l’explorateur Azure AD Graph, connectez-vous à votre compte Azure AD.
    2.  Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemple : créer et attribuer une stratégie pour inclure EmployeeID et TenantCountry en tant que revendications dans des jetons émis pour un principal du service.
Dans cet exemple, vous créez une stratégie qui ajoute EmployeeID et TenantCountry à des jetons émis pour des principaux du service liés. EmployeeID est émis en tant que type de revendication de nom dans les jetons SAML et JWT. TenantCountry est émis en tant que type de revendication de pays dans les jetons SAML et JWT. Dans cet exemple, nous continuons à inclure les ensembles de revendications de base dans les jetons.

1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
    1. Pour créer la stratégie, exécutez la commande suivante :  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante :
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service. 
    1.  Pour afficher tous les principaux du service de votre organisation, vous pouvez interroger Microsoft Graph. Ou bien, dans l’explorateur Azure AD Graph, connectez-vous à votre compte Azure AD.
    2.  Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante :  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemple : créer et attribuer une stratégie qui utilise une transformation de revendications dans des jetons émis pour un principal du service.
Dans cet exemple, vous créez une stratégie qui émet une revendication personnalisée « JoinedData » pour des jetons JWT émis pour des principaux du service liés. Cette revendication contient une valeur créée en joignant les données stockées dans l’attribut extensionattribute1 sur l’objet utilisateur avec « .sandbox ». Dans cet exemple, nous excluons l’ensemble de revendications de base des jetons.


1. Créez une stratégie de mappage de revendications. Cette stratégie liée à des principaux du service spécifiques ajoute les revendications EmployeeID et TenantCountry aux jetons.
    1. Pour créer la stratégie, exécutez la commande suivante : 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy" 
    ```
    
    2. Pour voir votre nouvelle stratégie et obtenir son ObjectId, exécutez la commande suivante : 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Affectez la stratégie au principal de service. Vous devez également obtenir l’ObjectId de votre principal du service. 
    1.  Pour afficher tous les principaux du service de votre organisation, vous pouvez interroger Microsoft Graph. Ou bien, dans l’explorateur Azure AD Graph, connectez-vous à votre compte Azure AD.
    2.  Une fois que vous disposez de l’ObjectId de votre principal du service, exécutez la commande suivante : 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
