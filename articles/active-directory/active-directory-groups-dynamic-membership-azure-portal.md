---
title: Appartenance à un groupe dynamique basé sur les attributs dans Azure Active Directory | Microsoft Docs
description: Procédure de création de règles avancées pour une adhésion de groupe dynamique incluant des paramètres et des opérateurs de règle d’expression pris en charge.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/23/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 2b42840bc1053e9574e7c8ab1c68611c3b2bc7df
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Créer des règles basées sur les attributs pour l’appartenance à un groupe dynamique dans Azure Active Directory
Dans Azure Active Directory (Azure AD), vous pouvez créer des règles avancées pour activer des appartenances dynamiques complexes basées sur les attributs pour les groupes. Cet article détaille les attributs et la syntaxe pour créer des règles d’appartenance dynamiques pour des utilisateurs ou des appareils.

Lorsqu’un attribut d’un utilisateur ou d’un appareil change, le système évalue toutes les règles de groupe dynamique d’un annuaire pour voir si la modification déclenche des ajouts ou suppressions de groupe. Si un utilisateur ou un appareil respecte une règle d’un groupe, il est ajouté en tant que membre de ce groupe. S’il ne respecte plus la règle, il est supprimé.

> [!NOTE]
> Vous pouvez définir une règle d’appartenance dynamique sur les groupes de sécurité ou Office 365.
>
> Cette fonctionnalité nécessite une licence Azure AD Premium P1 pour chaque utilisateur membre ajouté à au moins un groupe dynamique. Il n’est pas nécessaire d'attribuer réellement des licences aux utilisateurs pour qu’ils soient membres de groupes dynamiques, mais vous avez besoin d'un nombre suffisant de licences dans le client pour couvrir tous les utilisateurs de ce type. Par exemple, si vous avez un total de 1 000 utilisateurs uniques dans tous les groupes dynamiques de votre client, vous devez disposer d’au moins 1 000 licences pour Azure AD Premium P1, ou plus, pour répondre aux exigences de licence.
>
> Vous pouvez créer un groupe dynamique pour les appareils ou utilisateurs, mais vous ne pouvez pas créer une règle qui contient à la fois des objets d’utilisateur et d’appareils.
> 
> Il est actuellement impossible de créer un groupe d’appareil basé sur les attributs de l’utilisateur propriétaire. Les règles d’appartenance d’un appareil ne peuvent définir que des attributs immédiats d’objets d’appareil dans le répertoire.
> 
> Les équipes Microsoft ne prennent pas encore en charge l’appartenance à un groupe dynamique. Vous pouvez valider l’erreur dans les journaux associés à « Impossible de migrer le groupe d’appartenance dynamique »

## <a name="to-create-an-advanced-rule"></a>Pour créer une règle avancée
1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général ou en tant qu’administrateur de compte d’utilisateur.
2. Sélectionnez **Utilisateurs et groupes**.
3. Sélectionnez **Tous les groupes**, puis **Nouveau groupe**.

   ![Ajouter un nouveau groupe](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. Dans le panneau **Groupe** , saisissez un nom et une description pour le nouveau groupe. Sélectionnez un **Type d’appartenance** entre **Utilisateur dynamique** et **Appareil dynamique**, selon que vous souhaitiez créer une règle pour des utilisateurs ou des périphériques, puis sélectionnez **Ajouter une requête dynamique**. Vous pouvez utiliser le générateur de règle pour créer une règle simple, ou écrire une règle avancée vous-même. Cet article contient plus d’informations sur les attributs d’utilisateur et d’appareil disponibles, ainsi que des exemples de règles avancées.

   ![Ajouter une règle d’appartenance dynamique](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. Après avoir créé la règle, sélectionnez **Ajouter une requête** dans le bas du panneau.
6. Sélectionnez **Créer** on the **Groupe** panneau pour créer le groupe.

> [!TIP]
> La création d’un groupe peut échouer si la règle avancée que vous avez entrée est incorrecte. Une notification s’affiche alors dans le coin supérieur droit du portail. Elle contient une explication de la raison pour laquelle la règle ne peut pas être acceptée par le système. Lisez-la avec attention pour comprendre comment vous devez ajuster la règle pour la rendre valide.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construction du corps d’une règle avancée
La règle avancée que vous pouvez créer pour l’appartenance dynamique à des groupes est essentiellement une expression binaire qui se compose de trois parties et qui génère un résultat true ou false. Les trois parties sont les suivantes :

* Paramètre de gauche (leftParameter)
* Opérateur binaire (binaryOperator)
* Constante de droite (rightConstant)

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator "RightConstant"). Les parenthèses ouvrantes et fermantes sont facultatives pour l’ensemble de l’expression binaire, les guillemets doubles le sont aussi, mais il est nécessaire de les utiliser pour la constante de droite lorsqu’il s’agit d’une chaîne et la syntaxe pour le paramètre de gauche est la propriété de l’utilisateur. Une règle avancée peut se composer de plusieurs expressions binaires séparées par les opérateurs logiques -and, -or et -not.

Voici des exemples de règles avancées correctement construites :
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Pour obtenir la liste complète des paramètres et des opérateurs de règle d’expression pris en charge, consultez les sections ci-dessous. Pour les attributs utilisés pour les règles d’appareil, consultez la page [Utilisation d’attributs pour créer des règles pour les objets d’appareil](#using-attributes-to-create-rules-for-device-objects).

La longueur totale du corps de votre règle avancée ne peut pas dépasser 2 048 caractères.

> [!NOTE]
> Les opérations de chaîne et regex (expressions régulières) ne prennent pas en compte la casse. Vous pouvez également effectuer des vérifications de valeur Null, en utilisant *null* en tant que constante. Par exemple : user.department -eq *null*.
> Les chaînes contenant des guillemets doubles doivent être placées dans une séquence d’échappement à l’aide du caractère « ' ». Par exemple : `"\`Sales".

## <a name="supported-expression-rule-operators"></a>Opérateurs de règle d’expression pris en charge
Le tableau suivant répertorie tous les opérateurs de règle d’expression pris en charge et leur syntaxe à utiliser dans le corps de la règle avancée :

| Operator | Syntaxe |
| --- | --- |
| Non égal à |-ne |
| Égal à |-eq |
| Ne commence pas par |-notStartsWith |
| Commence par |-startsWith |
| Ne contient pas |-notContains |
| Contains |-contains |
| Ne correspond pas |-notMatch |
| Correspond |-match |
| Dans | -in |
| Pas dans | -notIn |

## <a name="operator-precedence"></a>Précédence des opérateurs

Tous les opérateurs sont répertoriés ci-dessous par priorité, de la plus faible à la plus élevée. Les opérateurs sur la même ligne ont la même priorité :
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
Tous les opérateurs peuvent être utilisés avec ou sans le préfixe de trait d’union. Des parenthèses ne sont nécessaires que lorsque la priorité ne répond pas à vos besoins.
Par exemple : 
```
   user.department -eq "Marketing" -and user.country -eq "US"
```
équivaut à :
```
   (user.department -eq "Marketing") -and (user.country -eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>Utilisation des opérateurs -in et -notIn

Si vous souhaitez comparer la valeur d’un attribut utilisateur par rapport à un nombre de valeurs différentes, vous pouvez utiliser les opérateurs -in ou -notin. Voici un exemple d’utilisation d’un opérateur -in :
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
Notez l’utilisation de « [ » et «] » au début et à la fin de la liste de valeurs. Cette condition évalue sur True la valeur de user.department égale à l’une des valeurs dans la liste.


## <a name="query-error-remediation"></a>Correction d’erreur de requête
Le tableau suivant répertorie les erreurs courantes et les méthodes pour les corriger

| Erreur d’analyse de requête | Utilisation incorrecte | Utilisation corrigée |
| --- | --- | --- |
| Erreur : attribut non pris en charge. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Assurez-vous que l’attribut se trouve dans la [liste des propriétés prises en charge](#supported-properties). |
| Erreur : l’opérateur n’est pas pris en charge sur l’attribut. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/><br/>L’opérateur utilisé n’est pas pris en charge pour le type de propriété (dans cet exemple,-contains ne peut pas être utilisé avec le type booléen). Utilisez les opérateurs corrects pour le type de propriété. |
| Erreur : erreur de compilation de la requête. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Opérateur manquant. Utilisez -and ou -or pour associer les prédicats<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2.Erreur dans l’expression régulière utilisée avec -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), alternativement : (user.userPrincipalName -match "@domain.ext$")|

## <a name="supported-properties"></a>Propriétés prises en charge
Voici toutes les propriétés d’utilisateur que vous pouvez utiliser dans vos règles avancées :

### <a name="properties-of-type-boolean"></a>Propriétés de type booléen
Opérateurs autorisés

* -eq
* -ne

| properties | Valeurs autorisées | Usage |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriétés de type chaîne
Opérateurs autorisés

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| properties | Valeurs autorisées | Usage |
| --- | --- | --- |
| city |Toute valeur de chaîne ou *$null* |(user.city -eq "value") |
| country |Toute valeur de chaîne ou *$null* |(user.country -eq "value") |
| companyName | Toute valeur de chaîne ou *$null* | (user.companyName -eq "value") |
| department |Toute valeur de chaîne ou *$null* |(user.department -eq "value") |
| displayName |Toute valeur de chaîne. |(user.displayName -eq "value") |
| employeeId |Toute valeur de chaîne. |(user.employeeId -eq "value")<br>(user.employeeId -ne *$null*) |
| facsimileTelephoneNumber |Toute valeur de chaîne ou *$null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Toute valeur de chaîne ou *$null* |(user.givenName -eq "value") |
| jobTitle |Toute valeur de chaîne ou *$null* |(user.jobTitle -eq "value") |
| mail |Toute valeur de chaîne ou *$null* (adresse SMTP de l’utilisateur) |(user.mail -eq "value") |
| mailNickName |Toute valeur de chaîne (alias de messagerie de l’utilisateur) |(user.mailNickName -eq "value") |
| mobile |Toute valeur de chaîne ou *$null* |(user.mobile -eq "value") |
| objectId |GUID de l’objet utilisateur |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificateur de sécurité (SID) local pour les utilisateurs synchronisés localement vers le cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Aucune DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Toute valeur de chaîne ou *$null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Toute valeur de chaîne ou *$null* |(user.postalCode -eq "value") |
| preferredLanguage |Code ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Toute valeur de chaîne ou *$null* |(user.sipProxyAddress -eq "value") |
| state |Toute valeur de chaîne ou *$null* |(user.state -eq "value") |
| streetAddress |Toute valeur de chaîne ou *$null* |(user.streetAddress -eq "value") |
| surname |Toute valeur de chaîne ou *$null* |(user.surname -eq "value") |
| telephoneNumber |Toute valeur de chaîne ou *$null* |(user.telephoneNumber -eq "value") |
| usageLocation |Paramètre régional à deux lettres |(user.usageLocation -eq "US") |
| userPrincipalName |Toute valeur de chaîne. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *$null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propriétés de type collection de chaînes
Opérateurs autorisés

* -contains
* -notContains

| properties | Valeurs autorisées | Usage |
| --- | --- | --- |
| otherMails |Toute valeur de chaîne. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propriétés à valeurs multiples
Opérateurs autorisés

* -any (respectée lorsqu’au moins un élément de la collection correspond à la condition)
* -all (respectée lorsque tous les éléments de la collection correspondent à la condition)

| properties | Valeurs | Usage |
| --- | --- | --- |
| assignedPlans |Chaque objet de la collection affiche les propriétés de chaînes suivantes : capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -et assignedPlan.capabilityStatus -eq "Enabled") |

Les propriétés à valeurs multiples sont des collections d’objets du même type. Vous pouvez utiliser les opérateurs -any et -all pour appliquer respectivement une condition à un ou tous les objets de la collection. Par exemple : 

assignedPlans est une propriété à valeurs multiples qui répertorie tous les plans de service assignés à l’utilisateur. L’expression ci-dessous sélectionne les utilisateurs dont le plan de service Exchange Online (Plan 2) est également dans l’état activé :

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(L’identificateur Guid détermine le plan de service Exchange Online (Plan 2).)

> [!NOTE]
> Ceci est utile si vous souhaitez identifier tous les utilisateurs pour lesquels une fonctionnalité Office 365 (ou tout autre service en ligne Microsoft) a été activée, pour cibler un ensemble de stratégies défini, par exemple.

L’expression suivante sélectionne tous les utilisateurs qui disposent d’un plan de service associé au service Intune (identifié par le nom de service « SCO ») :
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Utilisation des valeurs Null

Pour spécifier une valeur null dans une règle, vous pouvez utiliser la valeur *null*. Veillez à ne pas insérer de guillemets autour du mot *null*. Autrement, il sera interprété comme une valeur de chaîne littérale. La manière correcte de référencer la valeur null est la suivante :
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Attributs d’extension et attributs personnalisés
Les attributs d’extension et les attributs personnalisés sont pris en charge dans les règles d’appartenance dynamique.

Les attributs d’extension sont synchronisés à partir de Windows Server AD local et prennent le format « ExtensionAttributeX », lorsque X est égal à 1-15.
Voici en exemple de règle utilisant un attribut d’extension :
```
(user.extensionAttribute15 -eq "Marketing")
```
Les attributs personnalisés sont synchronisés à partir de Windows Server AD local ou d’une application SaaS connectée et le format de « user.extension[GUID]\__[Attribut] », lorsque [GUID] est l’identificateur unique dans AAD pour l’application qui a créé l’attribut dans AAD et [Attribut] est le nom de l’attribut tel qu’il a été créé.
Voici un exemple de règle utilisant un attribut personnalisé :
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
Vous pouvez accéder au nom de l’attribut personnalisé dans le répertoire en lançant une requête sur un attribut d’utilisateur, à l’aide de l’explorateur graphique, et en recherchant le nom d’attribut.

## <a name="direct-reports-rule"></a>Règle de « collaborateurs directs »
Vous pouvez créer un groupe contenant tous les collaborateurs directs d’un responsable. À l’avenir, lorsque les collaborateurs directs d’un responsable changeront, l’appartenance du groupe sera ajustée automatiquement.

> [!NOTE]
> 1. Pour que la règle fonctionne, assurez-vous que la propriété **ID Responsable** est correctement définie sur les utilisateurs de votre client. Vous pouvez vérifier la valeur actuelle d’un utilisateur sur son **onglet Profil**.
> 2. Cette règle prend uniquement en charge les collaborateurs **directs**. Il est actuellement impossible de créer un groupe pour une hiérarchie imbriquée, par exemple un groupe qui inclut des collaborateurs directs et leurs rapports.

**Pour configurer le groupe**

1. Suivez les étapes 1 à 5 de la section [Pour créer la règle avancée](#to-create-the-advanced-rule), puis sélectionnez le **type d’appartenance** de l’**utilisateur dynamique**.
2. Dans le panneau **Règles d’appartenance dynamique** , saisissez la règle avec la syntaxe suivante :

    *Collaborateurs directs pour {ID objet_du_responsable}*

    Exemple de règle valide :
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Une fois la règle enregistrée, tous les utilisateurs avec la valeur ID Responsable spécifiée seront ajoutés au groupe.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Utilisation d’attributs pour créer des règles pour les objets d’appareil
Vous pouvez également créer une règle qui sélectionne des objets d’appareil pour l’appartenance à un groupe. Les attributs d’appareil suivants peuvent être utilisés.

 Attribut d’appareil  | Valeurs | Exemples
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | Toute valeur de chaîne. |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Toute valeur de chaîne. | (device.deviceOSType -eq "iPad") ou (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Toute valeur de chaîne. | (device.OSVersion -eq "9.1")
 deviceCategory | Un nom de catégorie d’appareil valide. | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Toute valeur de chaîne. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Toute valeur de chaîne. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personnel, Entreprise, Inconnu | (device.deviceOwnership -eq "Company")
 domainName | Toute valeur de chaîne. | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Nom de profil de l’inscription d’appareil Apple. | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | Gestion des périphériques mobiles (pour les appareils mobiles).<br>PC (pour les ordinateurs gérés par l’agent PC Intune) | (device.managementType -eq "MDM")
 organizationalUnit | Toute valeur de chaîne correspondant au nom de l’unité d’organisation définie par un Active Directory local. | (device.organizationalUnit -eq "US PCs")
 deviceId | Un ID d’appareil Azure AD valide. | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | Un ID d’objet Azure AD valide. |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Changement de l’appartenance dynamique en appartenance statique et vice versa
Il est possible de modifier la façon dont l’appartenance est gérée dans un groupe. Cela est utile lorsque vous souhaitez conserver le même nom et le même ID de groupe dans le système, afin que toutes les références au groupe existantes soient toujours valides ; la création d’un nouveau groupe nécessiterait la mise à jour de ces références.

Nous sommes en train de mettre à jour le portail Azure pour prendre en charge cette fonctionnalité. En attendant, vous pouvez utiliser les applets de commande PowerShell comme indiqué ci-dessous.

> [!WARNING]
> Lorsque vous changez un groupe statique existant en groupe dynamique, tous les membres existants sont supprimés du groupe, puis la règle d’appartenance est exécutée pour ajouter de nouveaux membres. Si le groupe est utilisé pour contrôler l’accès aux applications ou aux ressources, les membres d’origine peuvent perdre leur accès tant que la règle d’appartenance n’a pas été totalement exécutée.
>
> Il est recommandé de tester la nouvelle règle d’appartenance au préalable pour vous assurer que la nouvelle appartenance du groupe est conforme à votre attente.

**Utilisation de PowerShell pour modifier la gestion des appartenances d’un groupe**

> [!NOTE]
> Pour modifier les propriétés de groupe dynamique, vous devez utiliser les applets de commande de la **préversion** d'[Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Vous pouvez installer la préversion depuis [ici](https://www.powershellgallery.com/packages/AzureADPreview).

Voici un exemple de fonctions qui permettent de changer la gestion des appartenances d’un groupe existant. Notez qu’une attention particulière est nécessaire pour manipuler correctement la propriété GroupTypes et conserver toutes les valeurs qui peuvent exister ici, qui ne sont pas liées à l’appartenance dynamique.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Pour rendre un groupe statique :
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Pour rendre un groupe dynamique :
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes dans Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
