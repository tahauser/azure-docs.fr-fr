---
title: "Activité de copie dans Azure Data Factory | Microsoft Docs"
description: "Découvrez l’activité de copie dans Azure Data Factory que vous pouvez utiliser pour copier des données d’une banque de données source prise en charge vers une banque de données réceptrice prise en charge."
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
ms.date: 11/22/2017
ms.author: jingwang
ms.openlocfilehash: a2f370998ea219f9d36a6cda26405b6023666f92
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="copy-activity-in-azure-data-factory"></a>Activité de copie dans Azure Data Factory

## <a name="overview"></a>Vue d'ensemble

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-data-movement-activities.md)
> * [Version 2 - Préversion](copy-activity-overview.md)

Dans Azure Data Factory, vous pouvez utiliser l’activité de copie pour copier des données entre des banques de données locales et dans cloud. Une fois les données copiées, elles peuvent être transformées et analysées plus avant. Vous pouvez également utiliser l’activité de copie pour publier les résultats de transformation et d’analyse pour l’aide à la décision (BI) et l’utilisation d’application.

![Rôle d’activité de copie](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est en disponibilité générale, voir [Activité de copie dans V1](v1/data-factory-data-movement-activities.md).

L’activité de copie est exécutée sur un [runtime d’intégration](concepts-integration-runtime.md). Pour un scénario de copie des données différent, une version différente du runtime d’intégration peut être utilisée :

* Lors de la copie de données entre banques de données accessibles publiquement, l’activité de copie peut être dynamisée par un **runtime d’intégration Azure** qui est sécurisé, fiable et évolutif et [disponible globalement](concepts-integration-runtime.md#integration-runtime-location).
* Lors de la copie de données entre banques de données locales ou en réseau avec contrôle d’accès (par exemple, Réseau virtuel Microsoft Azure), vous devez configurer un **runtime intégré auto-hébergé** pour dynamiser la copie des données.

Un runtime d’intégration doit être associé à chaque banque de données source et réceptrice. Découvrez plus de détails sur la manière dont l’activité de copie [détermine le runtime intégré à utiliser](concepts-integration-runtime.md#determining-which-ir-to-use).

Pour copier des données d’une source vers un récepteur, l’activité de copie suit les étapes suivantes. Le service qui alimente l’activité de copie :

1. Lit les données d’une banque de données source.
2. Effectue les opérations de sérialisation/désérialisation, de compression/décompression, de mappage de colonnes, etc. Il effectue ces opérations en se basant sur les configurations du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie.
3. Écrit les données dans la banque de données réceptrice/de destination.

![Présentation de l’activité de copie](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Banques de données et formats pris en charge

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formats de fichiers pris en charge

Vous pouvez utiliser l’activité de copie pour **copier des fichiers en l'état** entre deux banques de données de fichiers, auquel cas les données sont copiées efficacement sans aucune sérialisation/désérialisation.

L’activité de copie peut également lire et écrire dans des fichiers de formats **Texte, JSON, Avro, ORC et Parquet**, et les codecs de compression **GZip, Deflate, BZip2 et ZipDeflate** sont pris en charge. Pour plus d’informations, consultez [Formats de fichier et de compression pris en charge](supported-file-formats-and-compression-codecs.md).

Par exemple, vous pouvez effectuer les activités de copie suivantes :

* Copier les données dans le SQL Server local et les écrire dans Azure Data Lake Store au format ORC.
* Copier des fichiers au format texte (CSV) provenant d’un système de fichiers local et les écrire dans des objets blob Azure au format Avro.
* Copier les fichiers compressés depuis le système de fichiers local, les décompresser, puis accéder à Azure Data Lake Store.
* Copier des données au format texte compressé GZip (CSV) provenant d’objets blob Azure et les écrire dans une base de données SQL Azure.

## <a name="supported-regions"></a>Régions prises en charge

Le service qui propose l’activité de copie est disponible mondialement, dans les régions et zones géographiques répertoriées dans [Emplacement du runtime d’intégration](concepts-integration-runtime.md#integration-runtime-location). La topologie globalement disponible garantit le déplacement efficace des données en évitant généralement les sauts entre régions. Consultez la section [Services par région](https://azure.microsoft.com/regions/#services) pour connaître la disponibilité de Data Factory et du déplacement des données dans une région.

## <a name="configuration"></a>Configuration

Pour utiliser l’activité de copie dans Azure Data Factory, vous devez :

1. **Créer des services liés pour les banques de données source et réceptrice.** Pour connaître la configuration et les propriétés prises en charge, voir la section « Propriétés du service lié » de l’article relatif au connecteur. La liste des connecteurs pris en charge figure dans la section [Banques de données et formats pris en charge](#supported-data-stores-and-formats).
2. **Créer des jeux de données pour les banques de données source et réceptrice.** Pour connaître la configuration et les propriétés prises en charge, voir la section « Propriétés du jeu de données » des articles relatifs aux connecteurs source et récepteur.
3. **Créer un pipeline avec une activité de copie.** La section suivante fournit un exemple.  

### <a name="syntax"></a>Syntaxe

Le modèle suivant d’activité de copie contient une liste exhaustive des propriétés prises en charge. Spécifiez celles qui correspondent à votre scénario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Détails de la syntaxe

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété type d’une activité de copie doit être définie sur **Copy** | Oui |
| inputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données sources. L’activité de copie ne prend en charge qu’une seule entrée. | Oui |
| outputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données du récepteur. L’activité de copie ne prend en charge qu’une seule sortie. | Oui |
| typeProperties | Groupe de propriétés pour configurer l’activité de copie. | Oui |
| source | Spécifiez le type de source de la copie et les propriétés correspondantes concernant la façon d’extraire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | Oui |
| sink | Spécifiez le type de récepteur de copie et les propriétés correspondantes concernant la manière d’écrire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | Oui |
| translator | Spécifiez des mappages de colonnes explicites de la source au récepteur. S’applique lorsque le comportement de copie par défaut ne peut pas répondre à vos besoins.<br/><br/>Découvrez plus de détails sur le [Mappage de schéma et de type de données](copy-activity-schema-and-type-mapping.md). | Non |
| cloudDataMovementUnits | Spécifiez la puissance du [runtime d’intégration Azure](concepts-integration-runtime.md) pour dynamiser la copie des données.<br/><br/>Découvrez plus de détails sur les [Unités de déplacement de données cloud](copy-activity-performance.md). | Non |
| parallelCopies | Spécifiez le parallélisme que l’activité de copie doit utiliser lors de la lecture des données de la source et l’écriture des données sur le récepteur.<br/><br/>Découvrez plus de détails sur la [Copie parallèle](copy-activity-performance.md#parallel-copy). | Non |
| enableStaging<br/>stagingSettings | Choisissez cette option pour placer les données dans un stockage blob intermédiaire au lieu de les copier des données directement de la source au récepteur.<br/><br/>Découvrez les scénarios et des détails de configuration utiles d’une [Copie intermédiaire](copy-activity-performance.md#staged-copy). | Non |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Choisissez comment gérer les lignes incompatibles lors de la copie de données de la source vers le récepteur.<br/><br/>Découvrez plus de détails sur la [Tolérance de panne](copy-activity-fault-tolerance.md). | Non |

## <a name="monitoring"></a>Surveillance

Les détails de l’exécution de l’activité copie et les caractéristiques de performances sont retournés dans le résultat d’exécution de l’activité copie -> section Sortie. Voici une liste exhaustive. Découvrez comment surveiller l’exécution de l’activité dans la [section relative à la surveillance du démarrage rapide](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run). Vous pouvez comparer les performances et la configuration de votre scénario aux [performances de référence](copy-activity-performance.md#performance-reference) de l’activité de copie testée en interne.

| Nom de la propriété  | Description | Unité |
|:--- |:--- |:--- |
| dataRead | Taille des données lues à partir de la source | Valeur Int64 en octets |
| dataWritten | Taille des données écrites dans le récepteur | Valeur Int64 en octets |
| rowsCopied | Nombre de lignes copiées (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsSkipped | Nombre de lignes incompatibles ignorées. Vous pouvez activer la fonctionnalité en définissant « enableSkipIncompatibleRow » sur true. | Valeur Int64 (aucune unité) |
| throughput | Taux de transfert des données | Nombre à virgule flottante exprimé en Ko/s |
| copyDuration | Durée de la copie | Valeur Int32 en secondes |
| sqlDwPolyBase | Si PolyBase est utilisé lors de la copie de données dans SQL Data Warehouse. | Boolean |
| redshiftUnload | Si UNLOAD est utilisé lors de la copie de données à partir de Redshift. | Boolean |
| hdfsDistcp | Si DistCp est utilisé lors de la copie de données à partir de HDFS. | Boolean |
| effectiveIntegrationRuntime | Affichez la ou les infrastructures Integration Runtime permettant de dynamiser l’exécution d’activité au format « `<IR name> (<region if it's Azure IR>)` ». | Texte (chaîne) |
| usedCloudDataMovementUnits | Unités de déplacement de données cloud effectives pendant la copie. | Valeur Int32 |
| redirectRowPath | Chemin d’accès du journal des lignes incompatibles ignorées dans le stockage blob que vous configurez sous « redirectIncompatibleRowSettings ». Voir exemple ci-dessous. | Texte (chaîne) |
| billedDuration | Durée est facturée pour le déplacement des données. | Valeur Int32 en secondes |

```json
"output": {
    "dataRead": 1024,
    "dataWritten": 2048,
    "rowsCopies": 100,
    "rowsSkipped": 2,
    "throughput": 1024.0,
    "copyDuration": 3600,
    "redirectRowPath": "https://<account>.blob.core.windows.net/<path>/<pipelineRunID>/",
    "redshiftUnload": true,
    "sqlDwPolyBase": true,
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 8,
    "billedDuration": 28800
}
```

## <a name="schema-and-data-type-mapping"></a>Mappage du schéma et du type de données

Voir la section [Mappage du schéma et du type de données](copy-activity-schema-and-type-mapping.md) qui décrit la manière dont l’activité de copie mappe vos données source au récepteur.

## <a name="fault-tolerance"></a>Tolérance de panne

Par défaut, l’activité de copie arrête la copie de données et retourne une erreur quand elle rencontre des données incompatibles entre la source et le récepteur. Vous pouvez définir une configuration explicite pour ignorer et journaliser les lignes incompatibles et ne copier que les données compatibles pour assurer la réussite de la copie. Pour plus de détails, voir [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md).

## <a name="performance-and-tuning"></a>Performances et réglage

Consultez [Guide des performances et de l’optimisation de l’activité de copie](copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

## <a name="next-steps"></a>Étapes suivantes
Voir les procédures de démarrage rapide, didacticiels et exemples suivants :

- [Copier des données d’un emplacement vers un autre dans le même Stockage Blob Azure](quickstart-create-data-factory-dot-net.md)
- [Copier des données de Stockage Blob Azure vers Microsoft Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copier des données d’une base de données SQL Server locale vers Azure](tutorial-hybrid-copy-powershell.md)
