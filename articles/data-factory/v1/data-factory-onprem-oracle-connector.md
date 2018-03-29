---
title: Copier des données vers/à partir d’Oracle à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment copier des données vers et à partir d’une base de données Oracle locale à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 64e8a20f72d451908c12751c0f8062bf4ae86370
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Copier des données vers/à partir d’Oracle en local à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](data-factory-onprem-oracle-connector.md)
> * [Version 2 - Préversion](../connector-oracle.md)

> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service de fabrique de données, qui est une version d’évaluation, consultez l’article relatif au [connecteur Oracle dans V2](../connector-oracle.md).


Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour déplacer des données vers/à partir d’une base de données Oracle locale. Il s’appuie sur l’article [Activités de déplacement des données](data-factory-data-movement-activities.md), qui présente une vue d’ensemble du déplacement de données avec l’activité de copie.

## <a name="supported-scenarios"></a>Scénarios pris en charge
Vous pouvez copier des données **d’une base de données Oracle** vers les magasins de données suivants :

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Vous pouvez copier des données des magasins de données suivants **vers une base de données Oracle** :

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Prérequis

Data Factory prend en charge la connexion à des sources Oracle locales à l’aide de la passerelle de gestion des données. Consultez l’article [Passerelle de gestion de données](data-factory-data-management-gateway.md) pour en savoir plus sur la passerelle de gestion des données et l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour obtenir des instructions détaillées sur la configuration de la passerelle pour un pipeline de données afin de déplacer des données.

Une passerelle est requise même si la base de données Oracle est hébergée sur une machine virtuelle Azure IaaS. Vous pouvez installer la passerelle sur la même machine virtuelle IaaS que le magasin de données, ou sur une autre machine virtuelle pourvu que la passerelle puisse se connecter à la base de données.

> [!NOTE]
> Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.

## <a name="supported-versions-and-installation"></a>Versions prises en charge et installation
Ce connecteur Oracle prend en charge deux versions de pilotes :

- **Pilote Microsoft pour Oracle (recommandé)** : à compter de Data Management Gateway version 2.7, un pilote Microsoft pour Oracle est installé automatiquement avec la passerelle : il n’est donc pas nécessaire de gérer le pilote pour établir la connexion à Oracle. Vous pouvez également obtenir de meilleures performances de copie en utilisant ce pilote. Voici les versions de bases de données Oracle prises en charge :
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> Actuellement, le pilote Microsoft pour Oracle prend uniquement en charge la copie de données à partir d’Oracle, mais non l’écriture dans Oracle. Notez également que la fonctionnalité de connexion de test dans l’onglet Data Management Gateway Diagnostics (Diagnostics de passerelle de gestion des données) ne prend pas en charge ce pilote. Dans ce cas, vous pouvez valider la connectivité à l’aide de l’assistant de copie.
>

- **Fournisseur de données Oracle pour .NET :** vous pouvez également choisir d’utiliser le fournisseur de données Oracle pour copier des données à partir de ou vers Oracle. Ce composant est inclus dans [Oracle Data Access Components for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installez la version appropriée (32/64 bits) sur l’ordinateur sur lequel la passerelle est installée. [Oracle Data Provider .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) peut accéder à Oracle Database 10g Release 2 ou version ultérieure.

    Si vous choisissez « Installation XCopy », suivez les étapes dans le fichier readme.htm. Nous vous recommandons de choisir le programme d’installation avec interface utilisateur (et pas le programme d’installation XCopy).

    Après avoir installé le fournisseur, **redémarrez** le service hôte de la passerelle de gestion des données sur votre ordinateur à l’aide de l’applet Services (ou) du Gestionnaire de configuration de la passerelle de gestion des données.  

Si vous utilisez l’Assistant Copie pour créer le pipeline de copie, le type de pilote sera déterminé automatiquement. Le pilote Microsoft est utilisé par défaut, sauf la version de votre passerelle est antérieure à 2.7 ou si vous choisissez Oracle comme récepteur.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui déplace les données vers/depuis une base de données Oracle locale à l’aide de différents outils/API.

Le moyen le plus simple de créer un pipeline consiste à utiliser **l’Assistant de copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Vous pouvez également utiliser les outils suivants pour créer un pipeline : le **portail Azure**, **Visual Studio**, **Azure PowerShell**, le **modèle Azure Resource Manager**, l’**API .NET** et l’**API REST**. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [didacticiel sur l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Que vous utilisiez des outils ou des API, la création d’un pipeline qui déplace les données d’un magasin de données source vers un magasin de données récepteur implique les étapes suivantes :

1. Création d'une **fabrique de données**. Une fabrique de données peut contenir un ou plusieurs pipelines. 
2. Création de **services liés** pour lier les magasins de données d’entrée et de sortie à votre fabrique de données. Par exemple, si vous copiez des données d’une base de données Oracle vers Stockage Blob Azure, vous créez deux services liés pour lier votre base de données Oracle et votre compte de stockage Azure à votre fabrique de données. Pour les propriétés du service lié qui sont propres à Oracle, consultez la section [Propriétés du service lié](#linked-service-properties).
3. Création de **jeux de données** pour représenter les données d’entrée et de sortie de l’opération de copie. Dans l’exemple mentionné à la dernière étape, vous créez un jeu de données pour spécifier la table de votre base de données Oracle qui doit contenir les données d’entrée. Ensuite, vous créez un autre jeu de données pour spécifier le conteneur d’objets blob et le dossier qui contient les données copiées à partir de la base de données Oracle. Pour plus d’informations sur les propriétés de jeu de données qui sont propres à Oracle, consultez la section [Propriétés du jeu de données](#dataset-properties).
4. Création d’un **pipeline** avec une activité de copie qui utilise un jeu de données en tant qu’entrée et un jeu de données en tant que sortie. Dans l’exemple mentionné plus haut, vous utilisez OracleSource comme source et BlobSink comme récepteur pour l’activité de copie. De la même façon, si vous copiez des données du Stockage Blob Azure vers une base de données Oracle, vous utilisez BlobSource et OracleSink dans l’activité de copie. Pour les propriétés d’activité de copie qui sont spécifiques d’une base de données Oracle, consultez la section [Propriétés de l’activité de copie](#copy-activity-properties). Pour plus d’informations sur l’utilisation d’un magasin de données comme source ou comme récepteur, cliquez sur le lien de la section précédente correspondant à votre magasin de données. 

Lorsque vous utilisez l’Assistant, les définitions JSON de ces entités Data Factory (services liés, jeux de données et pipeline) sont automatiquement créées pour vous. Lorsque vous utilisez des outils/API (à l’exception de l’API .NET), vous devez définir ces entités Data Factory au format JSON.  Pour obtenir des exemples comportant des définitions JSON pour les entités Data Factory utilisées pour copier les données vers ou à partir d’une base de données Oracle locale, consultez la section [Exemples JSON](#json-examples-for-copying-data-to-and-from-oracle-database) de cet article.

Les sections suivantes fournissent des informations sur les propriétés JSON utilisées pour définir les entités Data Factory :

## <a name="linked-service-properties"></a>Propriétés du service lié
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Oracle.

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| Type |Le type de propriété doit être défini sur : **OnPremisesOracle** |OUI |
| driverType | Spécifiez le pilote à utiliser pour copier les données à partir de ou vers la base de données Oracle. Valeurs autorisées : **Microsoft** ou **ODP** (par défaut). Consultez la section [Version prise en charge et installation](#supported-versions-and-installation) sur les détails du pilote. | Non  |
| connectionString | Spécifier les informations requises pour la connexion à l’instance de base de données Oracle pour la propriété connectionString. | OUI |
| gatewayName | Nom de la passerelle utilisée pour se connecter au serveur Oracle local |OUI |

**Exemple : avec le pilote Microsoft**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Exemple : avec le pilote ODP**

Accédez à [ce site](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/) pour connaître les formats autorisés.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections telles que la structure, la disponibilité et la stratégie d’un jeu de données JSON sont similaires pour tous les types de jeux de données (Oracle, objet blob Azure, table Azure, etc.).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type OracleTable a les propriétés suivantes :

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| TableName |Nom de la table dans la base de données Oracle à laquelle le service lié fait référence. |Non (si **oracleReaderQuery** de **OracleSource** est spécifié) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et la stratégie sont disponibles pour tous les types d’activités.

> [!NOTE]
> L'activité de copie accepte uniquement une entrée et produit une seule sortie.

En revanche, les propriétés disponibles dans la section typeProperties de l’activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

### <a name="oraclesource"></a>OracleSource
Dans le cas d’une activité de copie, quand la source est de type **OracleSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilise la requête personnalisée pour lire des données. |Chaîne de requête SQL. Par exemple : select * from MyTable <br/><br/>Si non spécifié, l’instruction SQL est exécutée : select * from MyTable |Non (si **tableName** de **dataset** est spécifiée) |

### <a name="oraclesink"></a>OracleSink
**OracleSink** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Obligatoire |
| --- | --- | --- | --- |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. |intervalle de temps<br/><br/> Exemple : « 00:30:00 » (30 minutes). |Non  |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize |Nombre entier (nombre de lignes) |Non (valeur par défaut : 100) |
| sqlWriterCleanupScript |Spécifiez une requête pour exécuter l’activité de copie afin que les données d’un segment spécifique soient nettoyées. |Une instruction de requête. |Non  |
| sliceIdentifierColumnName |Spécifiez le nom de la colonne que l’activité de copie doit remplir avec l’identificateur de segment généré automatiquement, et qui est utilisée pour nettoyer les données d’un segment spécifique lors de la réexécution. |Nom d’une colonne avec le type de données binary(32). |Non  |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Exemples JSON pour copier des données vers et depuis une base de données Oracle
L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données entre une base de données Oracle et Azure Blob Storage. Toutefois, les données peuvent être copiées vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Exemple : copie de données d’Oracle vers Azure Blob

L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) comme source et [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) comme récepteur.

L’exemple copie toutes les heures les données d’une table d’une base de données Oracle locale vers un objet blob. Pour plus d’informations sur les diverses propriétés utilisées dans l’exemple, consultez la documentation dans les sections qui suivent les exemples.

**Service lié Oracle :**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Service lié Azure Blob Storage :**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Jeu de données d’entrée Oracle :**

L'exemple suppose que vous avez créé une table « MyTable » dans Oracle et qu'elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de « external » : « true» informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

**Jeu de données de sortie d'objet Blob Azure :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie et planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **OracleSource** et le type **sink** est défini sur **BlobSink**.  La requête SQL spécifiée pour la propriété **oracleReaderQuery** sélectionne les données de la dernière heure à copier.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
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

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Exemple : copie de données à partir d’Azure Blob vers Oracle
Cet exemple indique comment copier des données d’un stockage d’objets blob Azure vers une base de données Oracle locale. Toutefois, vous pouvez copier les données **directement** à partir des sources indiquées [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats), par le biais de l’activité de copie de Microsoft Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. un [jeu de données](data-factory-create-datasets.md) d'entrée de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) comme source et [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) comme récepteur.

L’exemple copie chaque heure les données d’une objet blob vers une table d’une base de données Oracle locale. Pour plus d’informations sur les diverses propriétés utilisées dans l’exemple, consultez la documentation dans les sections qui suivent les exemples.

**Service lié Oracle :**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Service lié Azure Blob Storage :**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Jeu de données d'entrée d'objet Blob Azure**

Les données sont récupérées à partir d’un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois et le jour de début et le nom de fichier utilise l’heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la fabrique de données et n’est pas produite par une activité dans la fabrique de données.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
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

**Jeu de données de sortie Oracle :**

L’exemple suppose que vous avez créé une table « MyTable » dans Oracle. Créez la table dans Oracle avec le même nombre de colonnes que le fichier CSV d’objets blob doit en contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Conseils de dépannage
### <a name="problem-1-net-framework-data-provider"></a>Problème 1 : Fournisseur de données .NET Framework

Le **message d’erreur** suivant s’affiche :

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Causes possibles :**

1. Le fournisseur de données .NET Framework pour Oracle n’a pas été installé.
2. Le fournisseur de données .NET Framework pour Oracle a été installé pour .NET Framework 2.0 et est introuvable dans les dossiers de .NET Framework 4.0.

**Résolution/solution de contournement :**

1. Si vous n’avez pas installé le fournisseur .NET pour Oracle, [installez-le](http://www.oracle.com/technetwork/topics/dotnet/downloads/) , puis réessayez.
2. Si vous obtenez le message d’erreur même après l’installation du fournisseur, procédez comme suit :
   1. Ouvrez le fichier machine.config de .NET 2.0 à partir du dossier : <system disk>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Recherchez **Fournisseur de données Oracle pour .NET**, et vous devriez trouver une entrée comme indiqué dans l’exemple suivant sous **system.data** -> **DbProviderFactories** : <add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Fournisseur de données Oracle pour .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />.
3. Copiez cette entrée dans le fichier machine.config dans le dossier v4.0 suivant : <system disk>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, et remplacez la version par 4.xxx.x.x.
4. Installez <Chemin d’installation d’ODP.NET>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll dans le Global Assembly Cache (GAC) en exécutant `gacutil /i [provider path]`.## Conseils de dépannage

### <a name="problem-2-datetime-formatting"></a>Problème 2 : Mise en forme de la date et de l’heure

Le **message d’erreur** suivant s’affiche :

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Résolution/solution de contournement :**

Vous devrez peut-être ajuster la chaîne de requête dans votre activité de copie en fonction de la manière dont les dates sont configurées dans votre base de données Oracle, comme indiqué dans l’exemple suivant (à l’aide de la fonction to_date) :

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Mappage de type pour Oracle
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement les types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Lors du déplacement de données à partir d’Oracle, les mappages suivants sont utilisés pour convertir le type de données Oracle en type .NET et vice versa.

| Type de données Oracle | Type de données .NET Framework. |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(seulement pris en charge sur Oracle 10g et les versions ultérieures en cas d’utilisation du pilote Microsoft) |
| CHAR |Chaîne |
| CLOB |Chaîne |
| DATE |Datetime |
| FLOAT |Décimale, chaîne (si précision > 28) |
| INTEGER |Décimale, chaîne (si précision > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |intervalle de temps |
| LONG |Chaîne |
| LONG RAW |Byte[] |
| NCHAR |Chaîne |
| NCLOB |Chaîne |
| NUMBER |Décimale, chaîne (si précision > 28) |
| NVARCHAR2 |Chaîne |
| RAW |Byte[] |
| ROWID |Chaîne |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Datetime |
| TIMESTAMP WITH TIME ZONE |Datetime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Chaîne |
| XML |Chaîne |

> [!NOTE]
> Les types de données **INTERVAL YEAR TO MONTH** et **INTERVAL DAY TO SECOND** ne sont pas pris en charge lors de l’utilisation du pilote Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapper les colonnes source aux colonnes du récepteur
Pour en savoir plus sur le mappage de colonnes du jeu de données source à des colonnes du jeu de données récepteur, voir [Mappage des colonnes d’un jeu de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lecture renouvelée de sources relationnelles
Lorsque vous copiez des données à partir de magasins de données relationnels, gardez à l’esprit la répétabilité de l’opération, afin d’éviter des résultats imprévus. Dans Azure Data Factory, vous pouvez réexécuter une tranche manuellement. Vous pouvez également configurer une stratégie de nouvelles tentatives pour un jeu de données, afin qu’une tranche soit réexécutée en cas de défaillance. Lorsqu’une tranche est réexécutée d’une manière ou d’une autre, vous devez vous assurer que les mêmes données sont lues et ce, quel que soit le nombre d’exécutions de la tranche. Voir [Lecture renouvelée de sources relationnelles](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.
