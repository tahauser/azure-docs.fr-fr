---
title: "Jeux de données et services liés dans Azure Data Factory | Microsoft Docs"
description: "Apprenez-en davantage sur les jeux de données et les services liés dans Azure Data Factory. Les services liés lient le calcul et les banques de données à une fabrique de données. Les jeux de données représentent des données d’entrée/sortie."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: bfc95588378466fe1e83bcc4e899eca6b66b358a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Jeux de données et services liés dans Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-create-datasets.md)
> * [Version 2 - Préversion](concepts-datasets-linked-services.md)

Cet article décrit les jeux de données, comment ils sont définis au format JSON et comment ils sont utilisés dans les pipelines Azure Data Factory V2. 

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Jeux de données dans Data Factory V1](v1/data-factory-create-datasets.md).

Si vous débutez avec Data Factory, consultez [Présentation d’Azure Data Factory](introduction.md) pour obtenir une vue d’ensemble. 

## <a name="overview"></a>Vue d’ensemble
Une fabrique de données peut avoir un ou plusieurs pipelines. Un **pipeline** constitue un regroupement logique d’**activités** qui exécutent ensemble une tâche. Les activités d’un pipeline définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’un serveur SQL Server local vers un stockage Blob Azure. Ensuite, vous pouvez utiliser une activité Hive qui exécute un script Hive sur un cluster Azure HDInsight pour traiter les données du stockage Blob afin de produire des données de sortie. Enfin, vous pouvez utiliser une deuxième activité de copie pour copier les données de sortie dans Azure SQL Data Warehouse sur lequel des solutions de génération de rapports décisionnelles sont développées. Pour plus d’informations sur les pipelines et les activités, voir [Pipelines et activités dans Azure Data Factory](concepts-pipelines-activities.md).

À présent, un **jeu de données** est une vue de données nommée qui pointe ou fait référence simplement aux données que vous souhaitez utiliser dans vos **activités** en tant qu’entrées et sorties. Les jeux de données identifient les données dans différents magasins de données, par exemple des tables, des fichiers, des dossiers et des documents. Par exemple, un jeu de données d’objets blob Azure spécifie le conteneur et le dossier du Stockage Blob à partir duquel l’activité doit lire les données.

Avant de créer un jeu de données, vous devez créer un **service lié** pour lier votre banque de données à la fabrique de données. Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Considérez les choses de la façon suivante : le jeu de données représente la structure des données à l’intérieur des magasins de données liés, et le service lié définit la connexion à la source de données. Par exemple, un service lié de stockage Azure relie un compte de stockage à la fabrique de données. Un jeu de données d'objets blob représente le conteneur d’objets blob et le dossier à l’intérieur de ce compte de stockage Azure contenant les objets blob d’entrée à traiter.

Voici un exemple de scénario. Pour copier des données d’un stockage Blob Azure dans une base de données SQL, vous créez deux services liés : le stockage Azure et la base de données SQL Azure. Créez ensuite deux jeux de données : le jeu de données d’objets blob Azure (qui fait référence au service lié de stockage Azure) et le jeu de données de table SQL Azure (qui fait référence au service lié pour base de données SQL Azure). Les services liés de stockage Azure et de base de données SQL Azure contiennent des chaînes de connexion utilisées par la fabrique de données pendant l’exécution pour se connecter à votre stockage Azure et à la base de données SQL Azure, respectivement. Le jeu de données d’objets blob Azure spécifie le conteneur d’objets blob et le dossier d’objets blob qui contient les objets blob d’entrée dans votre stockage Blob. Le jeu de données de table SQL Azure spécifie la table SQL dans votre base de données SQL dans laquelle les données doivent être copiées.

Le diagramme suivant montre la relation entre le pipeline, l’activité, le jeu de données et le service lié dans la fabrique de données :

![Relation entre le pipeline, l’activité, le jeu de données et les services liés](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Service lié JSON
Un service lié dans Data Factory se définit de la façon suivante au format JSON :

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
Nom | Nom du service lié. Voir [Azure Data Factory - Règles d’affectation des noms](naming-rules.md). |  OUI |
Type | Type du service lié. Par exemple : AzureStorage (magasin de données) ou AzureBatch (calcul). Consultez la description de typeProperties. | OUI |
typeProperties | Les propriétés type sont différentes pour chaque magasin de données et chaque unité de calcul. <br/><br/> Vous trouverez la liste des types de magasins de données pris en charge et leurs propriétés dans le tableau [type de jeu de données](#dataset-type) de cet article. Accédez à l’article sur le connecteur de magasin de données pour en savoir plus sur les propriétés de type propres à un magasin de données. <br/><br/> Vous trouverez la liste des types de calcul pris en charge et leurs propriétés sur la page [Services liés de calcul](compute-linked-services.md). | OUI |
connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser Azure Integration Runtime ou Integration Runtime auto-hébergé (si votre magasin de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non 

## <a name="linked-service-example"></a>Exemple de service lié
Le service lié suivant est un service lié Stockage Azure. Notez que le type est défini sur AzureStorage. Les propriétés de type du service lié Stockage Azure comprennent une chaîne de connexion. Le service Data Factory l’utilise pour se connecter au magasin de données à l’exécution. 

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>Jeu de données JSON
Un jeu de données dans la fabrique de données est défini au format JSON comme suit :

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
La table suivante décrit les propriétés dans le JSON ci-dessus :

Propriété | Description | Obligatoire |
-------- | ----------- | -------- |
Nom | Nom du jeu de données Voir [Azure Data Factory - Règles d’affectation des noms](naming-rules.md). |  OUI |
Type | Type du jeu de données. Spécifiez un des types pris en charge par la fabrique de données (par exemple : AzureBlob, AzureSqlTable). <br/><br/>Pour plus de détails, voir [Types de jeux de données](#dataset-types). | OUI |
structure | Schéma du jeu de données. Pour plus d’informations, consultez [Structure d’un jeu de données](#dataset-structure). | Non  |
typeProperties | Les propriétés de type sont différentes pour chaque type (par exemple : Blob Azure, table SQL Azure). Pour plus d’informations sur les types pris en charge et leurs propriétés, consultez [Type du jeu de données](#dataset-type). | OUI |

## <a name="dataset-example"></a>Exemple de jeu de données
Dans l’exemple suivant, le jeu de données représente une table nommée MyTable dans une base de données SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Notez les points suivants :

- Le type est défini sur AzureSQLTable.
- La propriété de type tableName (propre au type AzureSqlTable) est définie sur MyTable.
- linkedServiceName fait référence à un service lié de type AzureSqlDatabase, qui est défini dans l’extrait de code JSON suivant.

## <a name="dataset-type"></a>Type de jeu de données
Il existe différents types de jeux de données, selon la banque de données que vous utilisez. Consultez le tableau suivant pour obtenir la liste des magasins de données pris en charge par Data Factory. Cliquez sur un magasin de données pour savoir comment créer un service lié et un jeu de données pour ce magasin de données.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Dans l’exemple de la section précédente, le type du jeu de données est défini sur **AzureSqlTable**. De même, pour un jeu de données d’objets blob Azure, le type du jeu de données est défini sur **AzureBlob**, comme indiqué dans le JSON suivant :

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>Structure de jeu de données
La section **structure** est facultative. Elle définit le schéma du jeu de données en contenant une collection de noms et de types de données de colonnes. Vous utilisez la section structure pour fournir des informations sur le type utilisé pour convertir les types et colonnes de mappage depuis la source vers la destination. Dans l’exemple suivant, le jeu de données comporte trois colonnes : timestamp, projectname et pageviews. Elles sont respectivement de type String, String et Decimal.

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Chaque colonne de la structure contient les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
Nom | Nom de la colonne. | OUI
Type | Type de données de la colonne. | Non 
culture | Culture .NET à utiliser lorsque le type est un type .NET : `Datetime` ou `Datetimeoffset`. Par défaut, il s’agit de `en-us`. | Non 
format | Chaîne de format à utiliser lorsque le type est un type .NET : `Datetime` ou `Datetimeoffset`. | Non 

Les recommandations suivantes vous permettent de déterminer le moment auquel inclure les informations de structure et pour connaître les éléments à inclure dans la section **structure**.

- **Pour les sources de données structurées**, spécifiez la section structure uniquement si vous souhaitez mapper les colonnes sources aux colonnes du récepteur et que leurs noms ne sont pas identiques. Ce type de source de données structurées stocke les informations de schéma et de type de données, ainsi que les données elles-mêmes. SQL Server, Oracle et Azure SQL Database sont des exemples de sources de données structurées.<br/><br/>Comme les informations de type sont déjà disponibles pour les sources de données structurées, vous ne devez pas les inclure lorsque vous incluez la section structure.
- **Pour un schéma des sources de données de lecture (en particulier le stockage Blob)**, vous pouvez choisir de stocker des données sans les informations de type ou de schéma. Pour ces types de sources de données, incluez la structure lorsque vous souhaitez mapper les colonnes sources aux colonnes de récepteur. Incluez également la structure lorsque le jeu de données est une entrée d’une activité de copie et que les types de données du jeu de données source doivent être convertis en types natifs pour le récepteur.<br/><br/> Azure Data Factory prend en charge les valeurs suivantes pour fournir des informations de type dans la structure : `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan`. 

Apprenez-en davantage sur la manière dont la fabrique de données mappe les données sources au récepteur de [Mappage de schémas et de types]( copy-activity-schema-and-type-mapping.md) et quand spécifier des informations de structure.

## <a name="create-datasets"></a>Créez les jeux de données
Vous pouvez créer des jeux de données en utilisant l’un des outils ou kits de développement logiciel suivants : [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), modèle Azure Resource Manager et portail Azure

## <a name="v1-vs-v2-datasets"></a>Comparaison des jeux de données V1 et V2

Voici quelques différences entre les jeux de données Data Factory V1 et V2 : 

- La propriété externe n’est pas prise en charge dans V2. Elle est remplacée par un [déclencheur](concepts-pipeline-execution-triggers.md).
- Les propriétés de stratégie et de disponibilité ne sont pas prises en charge dans V2. L’heure de début d’un pipeline dépend des [déclencheurs](concepts-pipeline-execution-triggers.md).
- Les jeux de données étendus (définis dans un pipeline) ne sont pas pris en charge dans V2. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels suivants pour obtenir des instructions pas à pas sur la création de pipelines et de jeux de données à l’aide de l’un de ces outils ou Kits de développement logiciel (SDK). 

- [Démarrage rapide : créer une fabrique de données à l’aide de .NET](quickstart-create-data-factory-dot-net.md)
- [Démarrage rapide : créer une fabrique de données à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)
- [Démarrage rapide : créer une fabrique de données à l’aide d’une API REST](quickstart-create-data-factory-rest-api.md)
- Démarrage rapide : créer une fabrique de données à l’aide du portail Azure
