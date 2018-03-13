---
title: "Copier des données d’un serveur SFTP à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez le connecteur MySQL dans Azure Data Factory, qui permet de copier des données d’un serveur SFTP vers une banque de données réceptrice prise en charge."
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 55379add493224770ca7e0e26fd607cd0a2cf892
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copier des données d’un serveur SFTP à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-sftp-connector.md)
> * [Version 2 - Préversion](connector-sftp.md)

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’un serveur SFTP. Il s’appuie sur l’article [présentant une vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en Disponibilité générale, consultez [Connecteur SFTP dans V1](v1/data-factory-sftp-connector.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier les données d’un serveur SFTP dans toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur SFTP prend en charge ce qui suit :

- Copie de fichiers en utilisant une authentification **De base** ou **SshPublicKey**.
- Copie de fichiers en l'état ou analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques de SFTP.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié SFTP sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur **Sftp**. |OUI |
| host | Nom ou adresse IP du serveur SFTP. |OUI |
| port | Port sur lequel le serveur SFTP écoute.<br/>Valeurs autorisées : integer, la valeur par défaut est **22**. |Non  |
| skipHostKeyValidation | Spécifiez s’il faut ignorer la validation de la clé hôte.<br/>Valeurs autorisées : **true**, **false** (par défaut).  | Non  |
| hostKeyFingerprint | Spécifiez l’empreinte de la clé hôte. | Oui, si la valeur de « skipHostKeyValidation » est définie sur false.  |
| authenticationType | Spécification du type d’authentification.<br/>Valeurs autorisées : **De base**, **SshPublicKey**. Reportez-vous aux sections [Utilisation de l’authentification par clé publique SSH](#using-basic-authentication) et [Utilisation de l’authentification par clé publique SSH](#using-ssh-public-key-authentication) portant respectivement sur des propriétés supplémentaires et des exemples JSON. |OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non  |

### <a name="using-basic-authentication"></a>Utilisation de l’authentification de base

Pour utiliser l’authentification de base, définissez la propriété « authenticationType » sur **De base** et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur SFTP présentées dans la dernière section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Utilisateur ayant accès au serveur SFTP. |OUI |
| password | Mot de passe de l’utilisateur (nom d’utilisateur). Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | OUI |

**Exemple :**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
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

### <a name="using-ssh-public-key-authentication"></a>Utilisation de l’authentification par clé publique SSH

Pour utiliser l’authentification par clé publique SSH, définissez la propriété « authenticationType » sur **De base** et spécifiez les propriétés suivantes en plus des propriétés génériques du connecteur SFTP présentées dans la dernière section :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| userName | Utilisateur ayant accès au serveur SFTP |OUI |
| privateKeyPath | Spécifiez le chemin absolu au fichier de clé privée auquel le runtime d’intégration peut accéder. S’applique uniquement quand un type auto-hébergé du runtime d’intégration est spécifié dans « connectVia ». | Spécifiez soit la propriété `privateKeyPath`, soit la propriété `privateKeyContent`.  |
| privateKeyContent | Contenu de clé privée SSH encodé en Base64. La clé privée SSH doit être au format OpenSSH. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Spécifiez soit la propriété `privateKeyPath`, soit la propriété `privateKeyContent`. |
| passPhrase | Spécifiez la phrase secrète/le mot de passe pour déchiffrer la clé privée si le fichier de clé est protégé par une phrase secrète. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). | Oui, si le fichier de clé privée est protégé par une phrase secrète. |

> [!NOTE]
> Le connecteur SFTP prend en charge la clé OpenSSH RSA/DSA. Assurez-vous que le contenu de votre keyfile commence par «-----BEGIN [RSA/DSA] PRIVATE KEY-----». Si le fichier de clé privée est un fichier au format ppk, utilisez l’outil Putty pour effectuer la conversion du format .ppk vers le format OpenSSH. 

**Exemple 1 : authentification SshPublicKey à l’aide du chemin du fichier de clé privée**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple 2 : authentification SshPublicKey à l’aide du contenu de clé privée**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les jeux de données. Cette section fournit la liste des propriétés prises en charge par le jeu de données SFTP.

Pour copier des données de SFTP, affectez la valeur **FileShare** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **FileShare** |OUI |
| folderPath | Chemin d'accès au dossier. Par exemple : dossier/sous-dossier / |OUI |
| fileName | Si vous souhaitez copier à partir d’un fichier spécifique, spécifiez le nom de celui-ci dans **folderPath**. Si vous ne spécifiez aucune valeur pour cette propriété, le jeu de données pointe vers tous les fichiers du dossier en tant que source. |Non  |
| fileFilter | Spécifiez un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers. S’applique uniquement lorsque fileName n’est pas spécifié. <br/><br/>Les caractères génériques autorisés sont : `*` (plusieurs caractères) et `?` (caractère unique).<br/>- Exemple 1 : `"fileFilter": "*.log"`<br/>- Exemple 2 : `"fileFilter": 2017-09-??.txt"` |Non  |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser des fichiers d’un format spécifique, les types de formats de fichier pris en charge sont les suivants : **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Les types pris en charge sont : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Les niveaux pris en charge sont **Optimal** et **Fastest**. |Non  |

**Exemple :**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source SFTP.

### <a name="sftp-as-source"></a>SFTP en tant que source

Pour copier des données de SFTP, définissez **FileSystemSource** comme type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur **FileSystemSource** |OUI |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non  |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
