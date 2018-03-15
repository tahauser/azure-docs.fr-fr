---
title: "Copier des données depuis/vers Azure Data Lake Store à l’aide d’Azure Data Lake Store | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge vers Azure Data Lake Store (ou) à partir d’Azure Data Lake Store vers des banques de données réceptrices prises en charge."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: d4f5123ff47bbe1e4d88acdaef004dcecd2f3512
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="copy-data-to-or-from-azure-data-lake-store-by-using-azure-data-factory"></a>Copier des données depuis/vers Azure Data Lake Store à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-azure-datalake-connector.md)
> * [Version 2 - Préversion](connector-azure-data-lake-store.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données depuis/vers Azure Data Lake Store. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur Azure Data Lake Store dans V1](v1/data-factory-azure-datalake-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de toute banque de données source prise en charge vers Azure Data Lake Store, ou d’Azure Data Lake Store vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur Azure Data Lake Store prend en charge ce qui suit :

- Copie de fichiers à l’aide de l’authentification de **principal du service** ou de l’authentification **MSI (Managed Service Identity)**.
- Copie de fichiers en l'état ou analyse/génération de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Si vous copiez des données à l’aide du runtime d’intégration auto-hébergé, configurez le pare-feu d’entreprise pour autoriser le trafic sortant vers `<ADLS account name>.azuredatalakestore.net` et `login.microsoftonline.com/<tenant>/oauth2/token` sur le port 443. Ce dernier est le service d’émission de jeton de sécurité (STS) Azure avec lequel le runtime d’intégration doit communiquer pour obtenir le jeton d’accès.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques d’Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Azure Data Lake Store :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur : **AzureDataLakeStore**. | OUI |
| dataLakeStoreUri | Informations à propos du compte Azure Data Lake Store. Cette information prend un des formats suivants : `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | OUI |
| subscriptionId | ID d’abonnement Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| nom_groupe_ressources | Nom du groupe de ressources Azure auquel appartient le compte Data Lake Store. | Requis pour le récepteur |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

Pour accéder à d’autres propriétés et à des exemples JSON concernant ces deux types d’authentification, reportez-vous aux sections suivantes :

- [Utilisation de l’authentification de principal de service](#using-service-principal-authentication)
- [Utilisation de l’authentification MSI (Managed Service Identity)](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Utilisation de l’authentification de principal de service

Pour utiliser une authentification du principal du service, inscrivez une entité d’application dans Azure Active Directory (Azure AD) et donnez-lui accès à Data Lake Store. Consultez la page [Authentification de service à service](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

- ID de l'application
- Clé de l'application
- ID client

>[!IMPORTANT]
> Veillez à accorder l’autorisation appropriée au principal de service dans Azure Data Lake Store :
>- **Pour la source**, dans Explorateur de données -> Accès, accordez au moins une autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers/sous-dossiers ou une autorisation **Lecture** pour copier un seul fichier ; et choisissez d’ajouter en tant **qu’entrée d’autorisation d’accès et d’autorisation par défaut**. Aucune exigence sur le contrôle d’accès au niveau du compte (gestion des identités et des accès (IAM)).
>- **Pour le récepteur**, dans Explorateur de données -> Accès, accordez au moins une autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier, puis choisissez d’ajouter en tant **qu’entrée d’autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez Azure IR pour copier (la source et le récepteur sont tous les deux dans le cloud), dans Access Control (IAM), accordez au moins le rôle **Lecteur** pour que Data Factory puisse détecter la région Data Lake Store. Si vous souhaitez éviter ce rôle IAM, [créez un runtime Azure IR](create-azure-integration-runtime.md#create-azure-ir) de manière explicite avec l’emplacement de votre Data Lake Store, puis associez-le au service lié Data Lake Store, comme dans l’exemple suivant.

Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| servicePrincipalId | Spécifiez l’ID client de l’application. | OUI |
| servicePrincipalKey | Spécifiez la clé de l’application. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |
| locataire | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | OUI |

**Exemple :**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Utilisation de l’authentification MSI (Managed Service Identity)

Une fabrique de données peut être associée à une [identité de service managé](data-factory-service-identity.md), représentant la fabrique de données en question. Vous pouvez utiliser directement cette identité de service pour l’authentification Data Lake Store, ce qui revient à utiliser votre propre principal de service. Cela permet à la fabrique désignée d’accéder aux données et de les copier depuis/vers votre Data Lake Store.

Pour utiliser l’authentification MSI (Managed Service Identity) :

1. [Récupérez l’identité de service de la fabrique de données](data-factory-service-identity.md#retrieve-service-identity) en copiant la valeur « ID d’application de l’identité du service » générée en même temps que votre fabrique.
2. Accordez l’accès Data Lake Store au service d’identité comme vous le feriez pour un principal de service en suivant les remarques ci-dessous.

>[!IMPORTANT]
> Veillez à accorder l’autorisation appropriée à l’identité de service de la fabrique de données dans Azure Data Lake Store :
>- **Pour la source**, dans Explorateur de données -> Accès, accordez au moins une autorisation **Lecture + Exécution** pour lister et copier les fichiers dans les dossiers/sous-dossiers ou une autorisation **Lecture** pour copier un seul fichier ; et choisissez d’ajouter en tant **qu’entrée d’autorisation d’accès et d’autorisation par défaut**. Aucune exigence sur le contrôle d’accès au niveau du compte (gestion des identités et des accès (IAM)).
>- **Pour le récepteur**, dans Explorateur de données -> Accès, accordez au moins une autorisation **Écriture + Exécution** pour créer des éléments enfants dans le dossier, puis choisissez d’ajouter en tant **qu’entrée d’autorisation d’accès et entrée d’autorisation par défaut**. Si vous utilisez Azure IR pour copier (la source et le récepteur sont tous les deux dans le cloud), dans Access Control (IAM), accordez au moins le rôle **Lecteur** pour que Data Factory puisse détecter la région Data Lake Store. Si vous souhaitez éviter ce rôle IAM, [créez un runtime Azure IR](create-azure-integration-runtime.md#create-azure-ir) de manière explicite avec l’emplacement de votre Data Lake Store, puis associez-le au service lié Data Lake Store, comme dans l’exemple suivant.

Dans Azure Data Factory, il n’est pas nécessaire de spécifier de propriétés en dehors des informations générales Data Lake Store du service lié.

**Exemple :**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données Azure Data Lake Store.

Pour copier des données depuis/vers Azure Data Lake Store, définissez la propriété type du jeu de données sur **AzureDataLakeStoreFile**. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **AzureDataLakeStoreFile** |OUI |
| folderPath | Chemin d'accès du conteneur et du dossier dans le stockage de fichier. Exemple : dossierracine/sous-dossier / |OUI |
| fileName | Si vous souhaitez copier depuis/vers un fichier spécifique, spécifiez le nom de celui-ci dans **folderPath**. Si vous ne spécifiez aucune valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier.<br/><br/>Lorsque fileName n'est pas spécifié pour un jeu de données de sortie et que **preserveHierarchy** n’est pas spécifié dans le récepteur d’activité, l’activité de copie génère automatiquement le nom de fichier au format suivant : `Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]`. Par exemple : `Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz`. |Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non  |

**Exemple :**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par Azure Data Lake en tant que source et récepteur.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store en tant que source

Pour copier des données d’Azure Data Lake Store, définissez **AzureDataLakeStoreSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **AzureDataLakeStoreSource** |OUI |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store en tant que récepteur

Pour copier des données dans Azure Data Lake Store, définissez **AzureDataLakeStoreSink** comme type de récepteur dans l’activité de copie. Les propriétés prises en charge dans la section **sink** (récepteur) sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du récepteur d’activité de copie doit être définie sur **AzureDataLakeStoreSink** |OUI |
| copyBehavior | Définit le comportement de copie lorsque la source est constituée de fichiers d’une banque de données basée sur un fichier.<br/><br/>Les valeurs autorisées sont les suivantes :<br/><b>- PreserveHierarchy (par défaut)</b> : conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><b>- FlattenHierarchy</b> : tous les fichiers du dossier source figurent dans le premier niveau du dossier cible. Le nom des fichiers cibles est généré automatiquement. <br/><b>- MergeFiles</b> : fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier/d’objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement. | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>exemples de valeurs recursive et copyBehavior

Cette section décrit le comportement résultant de l’opération de copie pour différentes combinaisons de valeurs recursive et copyBehavior.

| recursive | copyBehavior | Structure du dossier source | Cible obtenue |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5. |
| true |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec le nom de fichier généré automatiquement |
| false |preserveHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles | Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Fichier5 | Le dossier cible Dossier1 est créé et structuré comme suit<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec le nom de fichier généré automatiquement. nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
