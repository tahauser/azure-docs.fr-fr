---
title: "Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory | Microsoft Docs"
description: "Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 859459bbce6b81e2e855201d5c310233d88d0393
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Référence d’API de création de rapports sur l’activité de connexion Azure Active Directory
Cette rubrique fait partie d’un ensemble de rubriques relatives à l’API de création de rapports Azure Active Directory.  
La création de rapports Azure AD fournit une API qui vous permet d’accéder aux données des rapports sur l’activité de connexion à l’aide de code ou d’outils associés.
Cette rubrique a pour but de vous fournir des informations de référence sur **l’API de création de rapports sur l’activité de connexion**.

Consultez l'article :

* [Activités de connexion](active-directory-reporting-azure-portal.md#activity-reports) pour plus d’informations conceptuelles
* [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md) pour plus d’informations sur l’API de création de rapports.


## <a name="who-can-access-the-api-data"></a>Qui peut accéder aux données de l’API ?
* Les utilisateurs et les principaux de service du rôle Administrateur de sécurité ou Lecteur de sécurité
* Administrateurs généraux
* Toute application qui a l’autorisation d’accéder à l’API (l’autorisation de l’application peut être configurée uniquement en fonction de l’autorisation Administrateur général)

Pour configurer l’accès afin qu’une application accède aux API de sécurité telles que les événements de connexion, utilisez la commande PowerShell suivante pour ajouter le principal de service des applications au rôle Lecteur de sécurité

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>configuration requise
Pour accéder à ce rapport via l’API de création de rapports, vous devez disposer des éléments suivants :

* Une [édition Azure Active Directory Premium P1 ou P2](active-directory-editions.md)
* Avoir respecté la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Accès à l’API
Vous pouvez soit accéder à cette API via [l’Afficheur Graph](https://graphexplorer2.cloudapp.net) , soit par programme à l’aide, par exemple, de PowerShell. Pour que PowerShell puisse interpréter correctement la syntaxe de filtre OData utilisée dans les appels REST Graph AAD, vous devez utiliser le caractère accent grave (`) pour « échapper » au caractère $. Le caractère accent grave sert de [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), ce qui permet à PowerShell d’effectuer une interprétation littérale du caractère $ et de ne pas le confondre avec un nom de variable PowerShell (par exemple : $filter).

Cette rubrique porte sur l’Afficheur Graph. Pour obtenir un exemple PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>Point de terminaison d’API
Vous pouvez accéder à cette API à l’aide de l’URI de base suivant :  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



En raison du volume de données, cette API est limitée à un million d’enregistrements retournés. 

Cet appel renvoie les données par lots. Chaque lot comporte un maximum de 1 000 enregistrements.  
Pour obtenir le lot d’enregistrements suivant, cliquez sur le lien Suivant. Obtenez les informations du [jeton d’évitement](https://msdn.microsoft.com/library/dd942121.aspx) dans le premier jeu d’enregistrements retournés. Le jeton d’évitement se trouve à la fin du jeu de résultats.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Filtres pris en charge
Vous pouvez réduire le nombre d’enregistrements qui sont retournés par un appel d’API à l’aide d’un filtre.  
Pour les données liées à l’API de connexion, les filtres suivants sont pris en charge :

* **$top=\<&lt;nombre d’enregistrements à retourner&gt;\>** : pour limiter le nombre d’enregistrements retournés. Il s’agit d’une opération coûteuse. N’utilisez pas ce filtre si vous souhaitez retourner des milliers d’objets.  
* **$filter=\<&lt;votre instruction de filtre&gt;\>** : pour spécifier, en fonction des champs de filtre pris en charge, les types d’enregistrements qui vous intéressent

## <a name="supported-filter-fields-and-operators"></a>Opérateurs et champs de filtre pris en charge
Pour indiquer le type d’enregistrements qui vous intéressent, vous pouvez créer une déclaration de filtre contenant l’un des champs de filtre suivants ou une combinaison de ceux-ci :

* [signinDateTime](#signindatetime) : définit une date ou une plage de dates
* [userId](#userid) : définit un utilisateur spécifique en fonction de l’ID de l’utilisateur.
* [userPrincipalName](#userprincipalname) : définit un utilisateur spécifique en fonction du nom d’utilisateur principal (UPN) de l’utilisateur
* [appId](#appid) : définit une application spécifique en fonction de l’ID de l’application
* [appDisplayName](#appdisplayname) : définit une application spécifique en fonction du nom d’affichage de l’application
* [loginStatus](#loginStatus) : définit l’état des connexions (réussite / échec)

> [!NOTE]
> Lorsque vous utilisez l’Afficheur Graph, vous devez respecter la casse pour chaque lettre dans les champs de filtre.
> 
> 

Pour réduire l’étendue des données retournées, vous pouvez créer une combinaison des filtres et des champs de filtres pris en charge. Par exemple, l’instruction suivante retourne les 10 premiers enregistrements entre le 1er juillet 2016 et le 6 juillet 2016 :

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Opérateurs pris en charge**: eq, ge, le, gt, lt

**Exemple**:

Utilisation d’une date spécifique

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Utilisation d’une plage de dates    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Remarques**:

Le paramètre datetime doit être au format UTC 

- - -
### <a name="userid"></a>userId
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Remarques**:

La valeur d’userId est une valeur de chaîne

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Remarques**:

La valeur d’userPrincipalName est une valeur de chaîne

- - -
### <a name="appid"></a>appId
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Remarques**:

La valeur d’appId est une valeur de chaîne

- - -
### <a name="appdisplayname"></a>appDisplayName
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Remarques**:

La valeur d’appDisplayName est une valeur de chaîne

- - -
### <a name="loginstatus"></a>loginStatus
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=loginStatus+eq+'1'  


**Remarques**:

Il existe deux options pour loginStatus : 0 - Réussite, 1 - Échec

- - -
## <a name="next-steps"></a>Étapes suivantes
* Voulez-vous voir des exemples d’activités de connexion filtrées ? Consultez les [exemples de l’API de création de rapports sur l’activité de connexion Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).
* Vous souhaitez en savoir plus sur l’API de création de rapports Azure AD ? Consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).

