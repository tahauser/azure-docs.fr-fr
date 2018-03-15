---
title: "Copier des données de sources ODBC à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de sources OData vers des banques de données réceptrices prises en charge."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ab29338b5ce2090166e75f3860744562aa9f6b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copier des données depuis/vers des banques de données ODBC à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-odbc-connector.md)
> * [Version 2 - Préversion](connector-odbc.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers une banque de données ODBC. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur ODBC dans V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une source ODBC vers toute banque de données réceptrice prise en charge, ou à partir de toute banque de données source prise en charge vers un récepteur ODBC. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur ODBC prend en charge la copie de données depuis/vers **toute banque de données compatible ODBC** à l’aide d’une authentification **De base** ou **Anonyme**.

## <a name="prerequisites"></a>configuration requise

Pour utiliser ce connecteur ODBC, vous devez :

- Configurer un Runtime d’intégration autohébergé. Pour plus d’informations, consultez l’article [Runtime d’intégration autohébergé](create-self-hosted-integration-runtime.md).
- Installer le pilote ODBC pour la banque de données sur l’ordinateur exécutant le runtime d’intégration.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur ODBC.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié ODBC sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Odbc** | OUI |
| connectionString | Chaîne de connexion sans la partie contenant les informations d’identification. Vous pouvez spécifier la chaîne de connexion avec un modèle comme `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, ou utiliser le nom de source de données (DSN) du système que vous avez configuré sur la machine de l’Integration Runtime avec `"DSN=<name of the DSN on IR machine>;"` (vous devez toujours spécifier la partie informations d’identification dans le service lié en conséquence).<br>Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md).| OUI |
| authenticationType | Type d’authentification utilisé pour se connecter au magasin de données ODBC.<br/>Valeurs autorisées : **De base** et **Anonyme**. | OUI |
| userName | Spécifiez le nom d’utilisateur si vous utilisez l’authentification de base. | Non  |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez défini pour le nom d’utilisateur. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Non  |
| credential | Partie de la chaîne de connexion contenant les informations d’accès, spécifiée dans un format de valeurs de propriété spécifique au pilote. Exemple : `"RefreshToken=<secret refresh token>;"`. Marquez ce champ comme SecureString. | Non  |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Un Runtime d’intégration autohébergé est nécessaire comme indiqué dans [Prérequis](#prerequisites). |OUI |

**Exemple 1 : utilisation d’une authentification de base**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : utilisation d’une authentification anonyme**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section contient la liste des propriétés prises en charge par le jeu de données ODBC.

Pour copier des données depuis/vers une banque de données ODBC, affectez la valeur **RelationalTable** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable** | OUI |
| TableName | Nom de la table dans le magasin de données ODBC. | Non pour la source (si « query » est spécifié dans la source de l’activité) ;<br/>Oui pour le récepteur |

**Exemple**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source ODBC.

### <a name="odbc-as-source"></a>ODBC en tant que source

Pour copier des données d’une banque de données compatible ODBC, définissez **RelationalSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **RelationalSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC en tant que récepteur

Pour copier des données vers une banque de données compatible ODBC, définissez le type de récepteur dans l’activité de copie sur **OdbcSink**. Les propriétés prises en charge dans la section **sink** (récepteur) de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **OdbcSink**. | OUI |
| writeBatchTimeout |Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer.<br/>Valeurs autorisées : timespan. Exemple : « 00:30:00 » (30 minutes). |Non  |
| writeBatchSize |Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize<br/>Valeurs autorisées : integer (nombre de lignes). |Non (la valeur par défaut est 0, détectée automatiquement) |
| preCopyScript |Spécifiez une requête SQL pour l’activité de copie à exécuter avant l’écriture de données dans la banque de données à chaque exécution. Vous pouvez utiliser cette propriété pour nettoyer des données préchargées. |Non  |

> [!NOTE]
> Pour la valeur de « writeBatchSize », si celle-ci n’est pas définie (détectée automatiquement), l’activité de copie détecte d’abord si le pilote prend en charge les opérations par lot, puis définit la valeur sur 10 000 si c’est le cas, ou sur 1 dans le cas contraire. Si vous définissez explicitement une valeur différente de 0, l’activité de copie respecte la valeur et le runtime échoue si le pilote ne prend pas en charge les opérations par lot.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>Source IBM Informix

Vous pouvez copier des données à partir d’une base de données IBM Informix à l’aide du connecteur ODBC générique.

Configurez un runtime d’intégration auto-hébergé sur une machine ayant accès à votre banque de données. L’Integration Runtime utilise le pilote ODBC pour Informix pour se connecter à la banque de données. Par conséquent, installez le pilote s’il ne l’est pas encore sur la même machine. Par exemple, vous pouvez utiliser le pilote « IBM INFORMIX ODBC DRIVER (64 bits) ». Pour plus d’informations, voir la section [Conditions préalables](#prerequisites).

Avant d’utiliser la source Informix dans une solution de fabrique de données, vérifiez si l’Integration Runtime peut se connecter à la banque de données en suivant les instructions de la section [Résoudre les problèmes de connectivité](#troubleshoot-connectivity-issues).

Créez un service lié ODBC pour lier une banque de données IBM Informix à une fabrique de données Azure comme dans l’exemple suivant :

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pour une présentation détaillée de l'utilisation de banques de données ODBC en tant que banques de données de sources/réceptrices dans une opération de copie, lisez l'article depuis le début.

## <a name="microsoft-access-source"></a>Source Microsoft Access

Vous pouvez copier des données à partir d’une base de données Microsoft Access à l’aide du connecteur ODBC générique.

Configurez un runtime d’intégration auto-hébergé sur une machine ayant accès à votre banque de données. L’Integration Runtime utilise le pilote ODBC pour Microsoft Access pour se connecter à la banque de données. Par conséquent, installez le pilote s’il ne l’est pas encore sur la même machine. Pour plus d’informations, voir la section [Conditions préalables](#prerequisites).

Avant d’utiliser la source Microsoft Access dans une solution de fabrique de données, vérifiez si l’Integration Runtime peut se connecter à la banque de données en suivant les instructions de la section [Résoudre les problèmes de connectivité](#troubleshoot-connectivity-issues).

Créez un service lié ODBC pour lier une base de données Microsoft Access à une fabrique de données Azure comme dans l’exemple suivant :

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pour une présentation détaillée de l'utilisation de banques de données ODBC en tant que banques de données de sources/réceptrices dans une opération de copie, lisez l'article depuis le début.

## <a name="ge-historian-source"></a>Source GE Historian

Vous pouvez copier des données à partir de GE Historian à l’aide du connecteur ODBC générique.

Configurez un runtime d’intégration auto-hébergé sur une machine ayant accès à votre banque de données. Le runtime d’intégration utilise le pilote ODBC pour GE Historian pour se connecter à la banque de données. Par conséquent, installez le pilote s’il ne l’est pas encore sur la même machine. Pour plus d’informations, voir la section [Conditions préalables](#prerequisites).

Avant d’utiliser la source GE Historian dans une solution de fabrique de données, vérifiez si l’Integration Runtime peut se connecter à la banque de données en suivant les instructions de la section [Résoudre les problèmes de connectivité](#troubleshoot-connectivity-issues).

Créez un service lié ODBC pour lier une base de données Microsoft Access à une fabrique de données Azure comme dans l’exemple suivant :

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pour une présentation détaillée de l'utilisation de banques de données ODBC en tant que banques de données de sources/réceptrices dans une opération de copie, lisez l'article depuis le début.

## <a name="sap-hana-sink"></a>Récepteur SAP HANA

>[!NOTE]
>Pour copier des données d’une banque de données SAP HANA, reportez-vous à la documentation du [connecteur SAP HANA](connector-sap-hana.md) natif. Pour copier des données vers SAP HANA, suivez cette instruction pour utiliser un connecteur ODBC. Notez que les services liés pour les connecteurs SAP HANA et ODBC sont de types différents et qu’ils ne peuvent donc pas être réutilisées.
>

Vous pouvez copier des données vers une base de données SAP HANA à l’aide du connecteur ODBC générique.

Configurez un runtime d’intégration auto-hébergé sur une machine ayant accès à votre banque de données. Le runtime d’intégration utilise le pilote ODBC pour SAP HANA pour se connecter à la banque de données. Par conséquent, installez le pilote s’il ne l’est pas encore sur la même machine. Pour plus d’informations, voir la section [Conditions préalables](#prerequisites).

Avant d’utiliser le récepteur SAP HANA dans une solution de fabrique de données, vérifiez si l’Integration Runtime peut se connecter à la banque de données en suivant les instructions de la section [Résoudre les problèmes de connectivité](#troubleshoot-connectivity-issues).

Créez un service lié ODBC pour lier une banque de données SAP HANA à une fabrique de données Azure comme dans l’exemple suivant :

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Pour une présentation détaillée de l'utilisation de banques de données ODBC en tant que banques de données de sources/réceptrices dans une opération de copie, lisez l'article depuis le début.

## <a name="troubleshoot-connectivity-issues"></a>Résoudre les problèmes de connectivité

Pour résoudre les problèmes de connexion, utilisez l’onglet **Diagnostics** du **Gestionnaire de configuration du runtime d’intégration**.

1. Lancez le **Gestionnaire de configuration du runtime d’intégration**.
2. Basculez vers l’onglet **Diagnostics** .
3. Dans la section « Tester la connexion », sélectionnez le **type** de banque de données (service lié).
4. Spécifiez la **chaîne de connexion** utilisée pour se connecter à la banque de données, choisissez l’**authentification**, puis entrez le **nom d’utilisateur**, le **mot de passe** et/ou les **informations d’identification**.
5. Cliquez sur **Tester la connexion** pour tester la connexion au magasin de données.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).