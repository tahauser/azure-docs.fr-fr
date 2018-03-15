---
title: API REST de recherche de journal Azure Log Analytics | Microsoft Docs
description: "Ce guide fournit un didacticiel de base qui décrit comment utiliser l’API REST de recherche Azure Log Analytics avec des exemples d’utilisation des commandes."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 46c88f7cc250d4c35043039a6f0440aaac85b1c2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-log-search-rest-api"></a>API REST de recherche de journal Log Analytics

> [!IMPORTANT]
> Si vous avez mis a niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), consultez la [documentation relative à la nouvelle version de l’API Recherche dans les journaux](https://dev.loganalytics.io/).  Il est possible que cette API héritée fonctionne avec un espace de travail mis à niveau, mais elle sera bientôt obsolète.  Pour utiliser la nouvelle API, vous devez modifier les solutions existantes.

Ce guide fournit un didacticiel de base, y compris des exemples, sur la façon dont vous pouvez utiliser l’API REST de recherche de journal Log Analytics. 


## <a name="overview-of-the-log-search-rest-api"></a>Vue d’ensemble de l’API REST de recherche de journal
L’API REST de recherche Log Analytics est un service RESTful qui est accessible par le biais de l’API Azure Resource Manager. Cet article fournit des exemples qui vous indiqueront comment accéder à l'API via [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l'appel de l'API du Gestionnaire de ressources Azure. L'utilisation d’ARMClient est une des nombreuses options vous permettant d’accéder à l'API de recherche de journal de Log Analytics. Une autre option consiste à utiliser le module Azure PowerShell pour OperationalInsights qui inclut des applets de commande pour accéder à la recherche. Grâce à ces outils, vous pouvez utiliser l’API Azure Resource Manager pour effectuer des appels vers les espaces de travail Log Analytics et exécuter en leur sein des commandes de recherche. L'API produit des résultats de recherche au format JSON, qui vous permet d'utiliser ces résultats, par programme, de différentes manières.

Le Gestionnaire de ressources Azure peut être utilisé via une [Bibliothèque pour .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) et l'[API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Pour en savoir plus, consultez les pages web connexes.

> [!NOTE]
> Si vous utilisez une commande d’agrégation comme `|measure count()` ou `distinct`, chaque appel à la recherche peut retourner jusqu'à 500 000 enregistrements. Les recherches qui n’incluent pas une commande d’agrégation renvoient jusqu'à 5 000 enregistrements.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Didacticiel de base sur l’API de recherche de journal de Log Analytics
### <a name="to-use-armclient"></a>Pour utiliser ARMClient
1. Installez [Chocolatey](https://chocolatey.org/)(gestionnaire de packages open source pour Windows). Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur, puis exécutez la commande suivante :

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Installez ARMClient en exécutant la commande suivante :

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Pour effectuer une recherche à l’aide d’ARMClient
1. Connectez-vous à l’aide de votre compte Microsoft, ou de votre compte professionnel ou scolaire :

    ```
    armclient login
    ```

    Une connexion réussie répertorie tous les abonnements associés au compte spécifique :

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Obtenez les espaces de travail Operations Management Suite :

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Un appel Get réussi affiche tous les espaces de travail liés à l’abonnement :

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Créez votre variable de recherche :

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Lancez une recherche à l’aide de votre nouvelle variable de recherche :

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Exemples de référence de l’API REST de recherche Log Analytics
Les exemples suivants vous expliquent comment utiliser l'API de recherche.

### <a name="search---actionread"></a>Recherche - Action/lecture
**Exemple d’URL :**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Requête :**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Le tableau suivant décrit les propriétés qui sont disponibles

| **Propriété** | **Description** |
| --- | --- |
| top |Le nombre maximal de résultats à renvoyer. |
| highlight |Contient des pré- et post-paramètres, utilisés généralement pour mettre en surbrillance les champs de correspondance |
| pre |Préfixe la chaîne spécifique à vos champs correspondants. |
| post |Ajoute la chaîne spécifique à vos champs correspondants. |
| query |La requête de recherche utilisée pour recueillir et renvoyer des résultats. |
| start |Le début de la plage horaire à partir de laquelle vous souhaitez obtenir des résultats. |
| end |La fin de la plage horaire à partir de laquelle vous souhaitez obtenir des résultats. |

**Réponse :**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Recherche/{ID} - Action/lecture
**Demander le contenu d'une recherche enregistrée :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Si la recherche renvoie un état « En attente », une interrogation des résultats mis à jour peut alors être réalisée via cette API. Après 6 minutes, le résultat de la recherche est supprimé du cache et HTTP Gone est retourné. Si la demande de recherche initiale retourne immédiatement un état « Succès », les résultats ne sont pas ajoutés au cache, ce qui signifie que cette API retourne HTTP Gone si elle est interrogée. Le contenu d’un résultat HTTP 200 est présenté dans le même format que la demande de recherche initiale. Toutefois, les valeurs sont mises à jour.
>
>

### <a name="saved-searches"></a>Recherches enregistrées
**Demander la liste des recherches enregistrées :**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Méthodes prises en charge : GET PUT DELETE

Méthodes de collection prises en charge : GET

Le tableau suivant décrit les propriétés qui sont disponibles

| Propriété | Description |
| --- | --- |
| ID |Identificateur unique. |
| Etag |**Requis pour le correctif**. Mis à jour par le serveur à chaque écriture. La valeur doit être égale à la valeur actuelle stockée ou '*' pour mettre à jour. 409 retourné pour les valeurs anciennes ou non valides. |
| properties.query |**Requis**. Requête de la recherche. |
| properties.displayName |**Requis**. Nom d'affichage de la requête défini par l'utilisateur. |
| properties.category |**Requis**. Catégorie de la requête définie par l'utilisateur. |

> [!NOTE]
> Actuellement, l'API de recherche Log Analytics retourne des recherches enregistrées créées par l'utilisateur lorsqu'elle est interrogée concernant les recherches enregistrées dans un espace de travail. L'API ne retourne pas de recherches enregistrées fournies par les solutions.
>
>

### <a name="create-saved-searches"></a>Créer des recherches enregistrées
**Requête :**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> Le nom de l’ensemble des recherches enregistrées, des planifications et des actions créées avec l’API Log Analytics doit être en minuscules.

### <a name="delete-saved-searches"></a>Supprimer les recherches enregistrées
**Requête :**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Mettre à jour les recherches enregistrées
 **Requête :**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Métadonnées - JSON uniquement
Voici un moyen d'afficher les champs pour tous les types de journaux pour les données collectées dans votre espace de travail. Par exemple, si vous voulez savoir si le type Événement possède un champ nommé Ordinateur, ceci constitue une méthode de vérification.

**Requête de champs :**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Réponse :**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

Le tableau suivant décrit les propriétés qui sont disponibles

| **Propriété** | **Description** |
| --- | --- |
| Nom |Nom du champ. |
| displayName |Nom d'affichage du champ. |
| Type |Type de la valeur du champ. |
| facetable |Combinaison des propriétés « indexed », « stored » et « facet » actuelles. |
| display |Propriété « display » actuelle. True si le champ est visible dans la recherche. |
| ownerType |Réduit uniquement aux types appartenant aux adresses IP intégrées. |

## <a name="optional-parameters"></a>Paramètres facultatifs
Les informations suivantes décrivent les paramètres facultatifs disponibles.

### <a name="highlighting"></a>Highlighting
Le paramètre « Highlight » est un paramètre facultatif que vous pouvez utiliser pour demander au sous-système de recherche d'inclure un jeu de marqueurs dans sa réponse.

Ces marqueurs indiquent le début et la fin du texte mis en surbrillance qui correspond aux critères fournis dans votre requête de recherche.
Vous pouvez spécifier les marqueurs de début et de fin qui sont utilisés par la recherche pour encapsuler le terme en surbrillance.

**Exemple de requête de recherche**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Exemple de résultat :**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Notez que le résultat précédent contient un message d'erreur qui a été préfixé et ajouté.

## <a name="computer-groups"></a>Groupes d’ordinateurs
Les groupes d'ordinateurs sont des recherches spéciales enregistrées qui retournent un ensemble d'ordinateurs.  Vous pouvez utiliser un groupe d'ordinateurs dans d'autres requêtes pour limiter les résultats aux ordinateurs du groupe.  Un groupe d'ordinateurs est implémenté comme une recherche enregistrée avec une balise Group dont la valeur est Computer.

Voici un exemple de réponse pour un groupe d'ordinateurs.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Récupération de groupes d'ordinateurs
pour récupérer un groupe d'ordinateurs, utilisez la méthode Get avec l'ID de groupe.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Création ou mise à jour d'un groupe d'ordinateurs
Pour créer un groupe d'ordinateurs, utilisez la méthode Put avec un ID de recherche enregistrée unique. Si vous utilisez un ID de groupe d’ordinateurs existant, celui-ci est modifié. Lorsque vous créez un groupe d'ordinateurs dans le portail Log Analytics, l'ID est créé à partir du groupe et du nom.

La requête utilisée pour la définition du groupe doit retourner un ensemble d'ordinateurs pour que le groupe fonctionne correctement.  Il est recommandé de terminer la requête par `| Distinct Computer` pour s’assurer que les données correctes sont retournées.

La définition de la recherche enregistrée doit inclure une balise nommée Group avec une valeur Computer pour que la recherche soit classée comme un groupe d'ordinateurs.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Suppression de groupes d'ordinateurs
Pour supprimer un groupe d'ordinateurs, utilisez la méthode Delete avec l'ID de groupe.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les [recherches de journaux](log-analytics-log-searches.md) pour générer des requêtes utilisant des champs personnalisés comme critères.
