---
title: "Envoyer des données à l’index de recherche à l’aide de Data Factory | Microsoft Docs"
description: "Découvrez comment envoyer des données à l’index Recherche Azure à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d8848f93518392333df16c9c7bf07bd0b2529034
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Envoyer des données à un index Recherche Azure à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](data-factory-azure-search-connector.md)
> * [Version 2 - Préversion](../connector-azure-search.md)

> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 de Data Factory, disponible en préversion, consultez [Connecteur Recherche Azure dans V2](../connector-azure-search.md).

Cet article décrit l’utilisation de l’activité de copie pour envoyer des données d’une banque de données source prise en charge à l’index Recherche Azure. Les magasins de données sources pris en charge sont répertoriés dans la colonne Source du tableau des [sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

## <a name="enabling-connectivity"></a>Activation de la connectivité
Pour permettre au service Data Factory de se connecter à un magasin de données local, vous devez installer la passerelle de gestion des données dans votre environnement local. Vous pouvez l’installer sur l’ordinateur qui héberge le magasin de données sources ou sur un autre ordinateur afin d’éviter toute mise en concurrence des ressources avec le magasin de données.

La passerelle de gestion des données connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui transmet les données d’une banque de données source à l’index Recherche Azure à l’aide de différents outils/API.

Le moyen le plus simple de créer un pipeline consiste à utiliser **l’Assistant Copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Vous pouvez également utiliser les outils suivants pour créer un pipeline : le **portail Azure**, **Visual Studio**, **Azure PowerShell**, le **modèle Azure Resource Manager**, l’**API .NET** et l’**API REST**. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [didacticiel sur l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Que vous utilisiez des outils ou des API, la création d’un pipeline qui déplace les données d’un magasin de données source vers un magasin de données récepteur implique les étapes suivantes : 

1. Création de **services liés** pour lier les magasins de données d’entrée et de sortie à votre fabrique de données.
2. Création de **jeux de données** pour représenter les données d’entrée et de sortie de l’opération de copie. 
3. Création d’un **pipeline** avec une activité de copie qui utilise un jeu de données en tant qu’entrée et un jeu de données en tant que sortie. 

Lorsque vous utilisez l’Assistant, les définitions JSON de ces entités Data Factory (services liés, jeux de données et pipeline) sont automatiquement créées pour vous. Lorsque vous utilisez des outils/API (à l’exception de l’API .NET), vous devez définir ces entités Data Factory au format JSON.  Pour consulter un exemple contenant des définitions JSON pour les entités Data Factory utilisées pour copier des données vers l’index Recherche Azure, consultez la section [Exemple JSON : copier des données depuis un serveur SQL local vers l’index Recherche Azure](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) de cet article. 

Les sections suivantes offrent des informations détaillées sur les propriétés JSON utilisées pour définir les entités Data Factory propres à l’Index Recherche Azure :

## <a name="linked-service-properties"></a>Propriétés du service lié

Le tableau suivant décrit les éléments JSON propres au service lié Recherche Azure.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| Type | La propriété de type doit être définie sur **AzureSearch**. | OUI |
| URL | URL du service Recherche Azure. | OUI |
| key | Clé d’administration du service Recherche Azure. | OUI |

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md) . Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données. La section **typeProperties** est différente pour chaque type de jeu de données. La section typeProperties d’un jeu de données de type **AzureSearchIndex** comprend les propriétés suivantes :

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| Type | La propriété de type doit être définie sur **AzureSearchIndex**.| OUI |
| indexName | Nom de l’index Recherche Azure. Data Factory ne crée pas l’index. L’index doit exister dans Recherche Azure. | OUI |


## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d’activité. En revanche, les propriétés disponibles dans la section typeProperties varient pour chaque type d’activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Pour l’activité de copie, quand la source est de type **AzureSearchIndexSink**, les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Indique s’il convient de procéder à une fusion ou à un remplacement lorsqu’un document existe déjà dans l’index. Voir la [propriété WriteBehavior](#writebehavior-property).| Merge (par défaut)<br/>Télécharger| Non  |
| writeBatchSize | Charge des données dans l’index Recherche Azure lorsque la taille du tampon atteint writeBatchSize. Pour plus d’informations, voir la [propriété WriteBatchSize](#writebatchsize-property). | 1 à 1 000. Valeur par défaut : 1 000. | Non  |

### <a name="writebehavior-property"></a>Propriété WriteBehavior
AzureSearchSink effectue une opération d’upsert lors de l’écriture des données. En d’autres termes, lorsque vous écrivez un document, si la clé du document existe déjà dans l’index Recherche Azure, Recherche Azure met à jour le document existant au lieu de lever une exception de conflit.

AzureSearchSink fournit les deux comportements upsert suivants (en utilisant le Kit de développement logiciel (SDK) AzureSearch) :

- **Fusion** : combine toutes les colonnes du nouveau document avec celles du document existant. Pour les colonnes ayant une valeur Null dans le nouveau document, la valeur de celles du document existant est conservée.
- **Chargement** : le nouveau document remplace l’ancien. Pour les colonnes qui ne sont pas spécifiées dans le nouveau document, la valeur est définie sur Null qu’il existe ou non une valeur autre que Null dans le document existant.

**Fusion** est le comportement par défaut.

### <a name="writebatchsize-property"></a>Propriété WriteBatchSize
Le service Recherche Azure prend en charge l’écriture de documents sous forme d’un lot. Un lot peut contenir 1 à 1 000 actions. Une action gère un document pour effectuer l’opération de chargement/fusion.

### <a name="data-type-support"></a>Prise en charge des types de données
Le tableau suivant indique si un type de données Recherche Azure est pris en charge ou non.

| Type de données Recherche Azure | Pris en charge dans le récepteur de l’index Recherche Azure |
| ---------------------- | ------------------------------ |
| Chaîne | O |
| Int32 | O |
| Int64 | O |
| Double | O |
| Booléen | O |
| DataTimeOffset | O |
| Tableau de chaînes | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemple JSON : copier des données depuis un serveur SQL Server local vers un index Recherche Azure

L’exemple suivant montre :

1.  Un service lié de type [AzureSearch](#linked-service-properties).
2.  Service lié de type [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureSearchIndex](#dataset-properties).
4.  Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) et [AzureSearchIndexSink](#copy-activity-properties).

L’exemple copie toutes les heures les données de série chronologique d’une base de données SQL Server locale vers un index Recherche Azure. Les propriétés JSON utilisées dans cet exemple sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données sur votre ordinateur local. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service lié Recherche Azure :**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**Service SQL Server lié**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Jeu de données d’entrée de SQL Server**

L’exemple suppose que vous avez créé une table « MyTable » dans SQL Server et qu’elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique. Vous pouvez interroger plusieurs tables au sein d’une même base de données en utilisant un jeu de données unique, mais une seule table doit être utilisée pour la propriété typeProperty tableName du jeu de données.

La définition de external sur true informe le service Data Factory que le jeu de données est externe à la Data Factory et non produite par une activité dans la Data Factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Jeu de données de sortie Recherche Azure :**

L’exemple copie des données vers un index Recherche Azure nommé **produits**. Data Factory ne crée pas l’index. Pour tester l’exemple, créez un index portant ce nom. Créez l’index Recherche Azure avec le même nombre de colonnes que dans le jeu de données d’entrée. De nouvelles entrées sont ajoutées toutes les heures à l’index Recherche Azure.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Activité de copie dans un pipeline avec une source SQL et un récepteur de l’index Recherche Azure :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **SqlSource** et le type **sink** est défini sur **AzureSearchIndexSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```

Si vous copiez des données d’un magasin de données cloud vers Recherche Azure, la propriété `executionLocation` est requise. À titre d’exemple, l’extrait de code JSON suivant montre la modification nécessaire dans `typeProperties` pour l’activité de copie. Consultez la section [Copier des données entre des banques de données cloud](data-factory-data-movement-activities.md#global) pour plus d’informations et les valeurs prises en charge.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Copier à partir d’une source cloud
Si vous copiez des données d’un magasin de données cloud vers Recherche Azure, la propriété `executionLocation` est requise. À titre d’exemple, l’extrait de code JSON suivant montre la modification nécessaire dans `typeProperties` pour l’activité de copie. Consultez la section [Copier des données entre des banques de données cloud](data-factory-data-movement-activities.md#global) pour plus d’informations et les valeurs prises en charge.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Vous pouvez également mapper les colonnes du jeu de données source aux colonnes du jeu de données récepteur dans la définition de l’activité de copie. Pour plus d’informations, consultez [Mappage de colonnes de jeux de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

* [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie.
