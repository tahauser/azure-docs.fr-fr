---
title: "Liaison de table externe pour Azure Functions (expérimental)"
description: Utilisation de liaisons de tables externes dans Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Liaison de table externe pour Azure Functions (expérimental)

Cet article explique comment utiliser les données tabulaires sur les fournisseurs SaaS, comme Sharepoint Dynamics, dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour les tables externes.

> [!IMPORTANT]
> La liaison de la table externe est expérimentale et pourrait ne jamais atteindre l’état de disponibilité générale (GA). Elle est incluse uniquement dans Azure Functions 1.x, et nous ne prévoyons actuellement pas de l’ajouter à Azure Functions 2.x. Pour les scénarios qui requièrent l’accès aux données dans les fournisseurs SaaS, envisagez d’utiliser [des applications logiques qui appellent des fonctions](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Connexions d’API

Les liaisons de table tirent parti des connexions d’API externes pour s’authentifier auprès des fournisseurs SaaS tiers. 

Lors de l’attribution d’une liaison, vous pouvez créer une connexion d’API ou utiliser une API existante au sein du même groupe de ressources.

### <a name="available-api-connections-tables"></a>Connexions d’API disponibles (tables)

|Connecteur|Déclencheur|Entrée|Sortie|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Les connexions aux tables externes peuvent également servir dans les applications [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Création pas à pas d’une connexion d’API

1. Dans la page du portail Azure pour votre application de fonction, sélectionnez le signe plus (**+**) pour créer une fonction.

1. Dans la zone **Scénario**, sélectionnez **Expérimental**.

1. Sélectionnez **Table externe**.

1. Sélectionnez une langue.

2. Sous **Connexion de table externe**, sélectionnez une connexion existante ou **Nouveau**.

1. Pour une nouvelle connexion, configurez les paramètres, puis sélectionnez **Autoriser**.

1. Sélectionnez **Créer** pour créer la fonction.

1. Sélectionnez **Intégrer > Table externe**.

1. Configurez la connexion pour utiliser la table cible. Ces paramètres varient selon les fournisseurs SaaS. Des exemples figurent dans la section suivante.

## <a name="example"></a>Exemple

Cet exemple se connecte à une table nommée « Contact » qui comporte les colonnes ID, LastName et FirstName. Le code répertorie les entités Contact dans la table et journalise les noms et les prénoms.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Source de données SQL Server

Pour créer une table dans SQL Server à utiliser avec cet exemple, voici un script. `dataSetName` est la valeur par défaut.

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Source de données Google Sheets

Pour créer une table à utiliser avec cet exemple dans Google Docs, créez une feuille de calcul nommée `Contact`. Le connecteur ne peut pas utiliser le nom d’affichage de la feuille de calcul. Le nom interne (en gras) doit servir en tant que dataSetName, par exemple : `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Ajoutez les noms de colonne `Id`, `LastName`, `FirstName` à la première ligne, puis remplissez les données sur les lignes suivantes.

### <a name="salesforce"></a>Salesforce

Pour cet exemple avec Salesforce, `dataSetName` est la valeur « par défaut ».

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

|Propriété function.json | Description|
|---------|----------------------|
|**type** | Cette propriété doit être définie sur `apiHubTable`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. | 
|**Connexion**| Identifie le paramètre d’application qui stocke la chaîne de connexion d’API. Le paramètre d’application est créé automatiquement lorsque vous ajoutez une connexion d’API dans l’interface utilisateur d’intégration.|
|**dataSetName**|Le nom du jeu de données qui contient la table à lire.|
|**tableName**|Le nom de la table|
|**entityId**|Doit être vide pour les liaisons de table.

Un connecteur sous forme de tableau fournit des jeux de données et chaque jeu de données contient des tables. Le nom du jeu de données par défaut est « default ». Les titres de jeux de données et de tables de différents fournisseurs SaaS sont répertoriés ci-dessous :

|Connecteur|Jeu de données|Table|
|:-----|:---|:---| 
|**SharePoint**|Site|Liste SharePoint
|**SQL**|Base de données|Table 
|**Google Sheet**|Feuille de calcul|Feuille de calcul 
|**Excel**|Fichier Excel|Feuille 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
