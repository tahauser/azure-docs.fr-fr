---
title: "Liaisons de table externe Azure Functions (préversion) | Microsoft Docs"
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
ms.openlocfilehash: 1d983a6924a939a8eb89355fab0c90596dbf2ed3
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Liaisons de table externe Azure Functions (préversion)
Cet article montre comment manipuler les données tabulaires sur des fournisseurs SaaS (par exemple, SharePoint, Dynamics) au sein de votre fonction en utilisant des liaisons intégrées. Azure Functions prend en charge des liaisons d’entrée et de sortie pour les tables externes.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Connexions d’API

Les liaisons de table tirent parti des connexions d’API externes pour s’authentifier auprès des fournisseurs SaaS tiers. 

Lors de l’attribution d’une liaison, vous pouvez créer une connexion d’API ou utiliser une API existante au sein du même groupe de ressources

### <a name="supported-api-connections-tables"></a>Tableau des connexions d’API prises en charge

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
> Les connexions aux tables externes peuvent également servir dans les applications [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

### <a name="creating-an-api-connection-step-by-step"></a>Création pas à pas d’une connexion d’API

1. Créer une fonction > fonction personnalisée ![Créer une fonction personnalisée](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Scénario `Experimental` > `ExternalTable-CSharp` Modèle > Créer `External Table connection`
![Choisir un modèle d’entrée de table](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Choisir votre fournisseur SaaS > Choisir/créer une connexion ![Configurer un connexion SaaS](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Sélectionner votre connexion d’API > Créer la fonction ![Créer une fonction de table](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Sélectionnez `Integrate` > `External Table`
    1. Configurez la connexion pour utiliser la table cible. Ces paramètres varient selon les fournisseurs SaaS. Ils sont décrits ci-dessous dans [Paramètres de la source de données](#datasourcesettings)
![Configurer la table](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Usage

Cet exemple se connecte à une table nommée « Contact » qui comporte les colonnes ID, LastName et FirstName. Le code répertorie les entités Contact dans la table et journalise les noms et les prénoms.

### <a name="bindings"></a>Liaisons
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
`entityId` doit être vide pour les liaisons de table.

`ConnectionAppSettingsKey` identifie le paramètre d’application qui stocke la chaîne de connexion d’API. Le paramètre d’application est créé automatiquement lorsque vous ajoutez une connexion d’API dans l’interface utilisateur d’intégration.

Un connecteur sous forme de tableau fournit des jeux de données et chaque jeu de données contient des tables. Le nom du jeu de données par défaut est « default ». Les titres de jeux de données et de tables de différents fournisseurs SaaS sont répertoriés ci-dessous :

|Connecteur|Jeu de données|Table|
|:-----|:---|:---| 
|**SharePoint**|Site|Liste SharePoint
|**SQL**|Base de données|Table 
|**Google Sheet**|Feuille de calcul|Feuille de calcul 
|**Excel**|Fichier Excel|Feuille 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilisation en C# #

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

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
##Paramètres de la source de données

### <a name="sql-server"></a>SQL Server

Le script qui permet de créer et de remplir la table Contact est affiché ci-dessous. dataSetName est défini sur « default ».

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

### <a name="google-sheets"></a>Google Sheets
Dans Google Docs, créez une feuille de calcul nommée `Contact`. Le connecteur ne peut pas utiliser le nom d’affichage de la feuille de calcul. Le nom interne (en gras) doit servir en tant que dataSetName, par exemple : `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Ajoutez les noms de colonne `Id`, `LastName`, `FirstName` à la première ligne, puis remplissez les données sur les lignes suivantes.

### <a name="salesforce"></a>Salesforce
dataSetName est défini sur « default ».

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
