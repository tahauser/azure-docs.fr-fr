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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: faad821d406ac155516696c1207c8c9deef8fdab
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Activité de copie dans Azure Data Factory

## <a name="overview"></a>Vue d'ensemble

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-data-movement-activities.md)
> * [Version 2 - Préversion](copy-activity-overview.md)

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

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type d’une activité de copie doit être définie sur **Copy** | OUI |
| inputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données sources. L’activité de copie ne prend en charge qu’une seule entrée. | OUI |
| outputs | Spécifiez le jeu de données que vous avez créé qui pointe vers les données du récepteur. L’activité de copie ne prend en charge qu’une seule sortie. | OUI |
| typeProperties | Groupe de propriétés pour configurer l’activité de copie. | OUI |
| source | Spécifiez le type de source de la copie et les propriétés correspondantes concernant la façon d’extraire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | OUI |
| sink | Spécifiez le type de récepteur de copie et les propriétés correspondantes concernant la manière d’écrire les données.<br/><br/>Découvrez plus de détails dans la section « Propriétés de l’activité de copie » de l’article sur le connecteur répertorié dans [Banques de données et formats pris en charge](#supported-data-stores-and-formats). | OUI |
| translator | Spécifiez des mappages de colonnes explicites de la source au récepteur. S’applique lorsque le comportement de copie par défaut ne peut pas répondre à vos besoins.<br/><br/>Découvrez plus de détails sur le [Mappage de schéma et de type de données](copy-activity-schema-and-type-mapping.md). | Non  |
| cloudDataMovementUnits | Spécifiez la puissance du [runtime d’intégration Azure](concepts-integration-runtime.md) pour dynamiser la copie des données.<br/><br/>Découvrez plus de détails sur les [Unités de déplacement de données cloud](copy-activity-performance.md). | Non  |
| parallelCopies | Spécifiez le parallélisme que l’activité de copie doit utiliser lors de la lecture des données de la source et l’écriture des données sur le récepteur.<br/><br/>Découvrez plus de détails sur la [Copie parallèle](copy-activity-performance.md#parallel-copy). | Non  |
| enableStaging<br/>stagingSettings | Choisissez cette option pour placer les données dans un stockage blob intermédiaire au lieu de les copier des données directement de la source au récepteur.<br/><br/>Découvrez les scénarios et des détails de configuration utiles d’une [Copie intermédiaire](copy-activity-performance.md#staged-copy). | Non  |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Choisissez comment gérer les lignes incompatibles lors de la copie de données de la source vers le récepteur.<br/><br/>Découvrez plus de détails sur la [Tolérance de panne](copy-activity-fault-tolerance.md). | Non  |

## <a name="monitoring"></a>Surveillance

Vous pouvez surveiller l’exécution de l’activité de copie dans l’interface utilisateur « Créer et surveiller » d’Azure Data Factory ou par programmation. Vous pouvez ensuite comparer les performances et la configuration de votre scénario aux [performances de référence](copy-activity-performance.md#performance-reference) de l’activité de copie testée en interne.

### <a name="monitor-visually"></a>Surveiller visuellement

Pour surveiller visuellement l’exécution de l’activité de copie, accédez à votre fabrique de données -> **Créer et surveiller** -> **onglet Surveiller**. Une liste d’exécutions de pipeline s’affiche avec un lien « Afficher les exécutions d’activité » dans la colonne  **Actions**. 

![Surveiller des exécutions de pipelines](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Cliquez pour afficher la liste des activités dans cette exécution de pipeline. Dans la colonne **Actions** figurent des liens vers l’entrée, la sortie, les erreurs (si l’exécution de l’activité de copie échoue) et les détails de l’activité de copie.

![Surveiller des exécutions d’activités](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Cliquez sur le lien « **Détails** » sous **Actions** pour afficher les détails et les caractéristiques de performances de l’exécution de l’activité de copie. Parmi les informations répertoriées figurent le volume/les lignes/les fichiers de données copiés de la source vers le récepteur, le débit, les étapes effectuées avec la durée correspondante, et les configurations utilisées pour votre scénario de copie.

**Exemple : copier d’Amazon S3 vers Azure Data Lake Store**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Exemple : copier d’Azure SQL Database vers Azure SQL Data Warehouse à l’aide de la copie intermédiaire**
![Surveiller les détails de l’exécution d’activité](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Surveiller par programmation

Les détails de l’exécution de l’activité de copie et les caractéristiques de performances sont également retournés dans le résultat d’exécution de l’activité copie -> section Sortie. Voici une liste exhaustive ; seuls les détails applicables à votre scénario de copie seront affichés. Découvrez comment surveiller l’exécution de l’activité dans la [section relative à la surveillance du démarrage rapide](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nom de la propriété  | Description | Unité |
|:--- |:--- |:--- |
| dataRead | Taille des données lues à partir de la source | Valeur Int64 en **octets** |
| dataWritten | Taille des données écrites dans le récepteur | Valeur Int64 en **octets** |
| filesRead | Nombre de fichiers copiés lors de la copie de données à partir du stockage de fichier. | Valeur Int64 (aucune unité) |
| filesWritten | Nombre de fichiers copiés lors de la copie de données vers le stockage de fichier. | Valeur Int64 (aucune unité) |
| rowsCopied | Nombre de lignes copiées (non applicable pour une copie binaire). | Valeur Int64 (aucune unité) |
| rowsSkipped | Nombre de lignes incompatibles ignorées. Vous pouvez activer la fonctionnalité en définissant « enableSkipIncompatibleRow » sur true. | Valeur Int64 (aucune unité) |
| throughput | Taux de transfert des données | Nombre à virgule flottante exprimé en **Ko/s** |
| copyDuration | Durée de la copie | Valeur Int32 en secondes |
| sqlDwPolyBase | Si PolyBase est utilisé lors de la copie de données dans SQL Data Warehouse. | Booléen |
| redshiftUnload | Si UNLOAD est utilisé lors de la copie de données à partir de Redshift. | Booléen |
| hdfsDistcp | Si DistCp est utilisé lors de la copie de données à partir de HDFS. | Booléen |
| effectiveIntegrationRuntime | Affichez la ou les infrastructures Integration Runtime permettant de dynamiser l’exécution d’activité au format « `<IR name> (<region if it's Azure IR>)` ». | Texte (chaîne) |
| usedCloudDataMovementUnits | Unités de déplacement de données cloud effectives pendant la copie. | Valeur Int32 |
| usedParallelCopies | Nombre effectif de parallelCopies pendant la copie. | Valeur Int32|
| redirectRowPath | Chemin d’accès du journal des lignes incompatibles ignorées dans le stockage blob que vous configurez sous « redirectIncompatibleRowSettings ». Voir exemple ci-dessous. | Texte (chaîne) |
| executionDetails | Détails supplémentaires sur les étapes effectuées lors de l’activité de copie, ainsi que les étapes correspondantes, la durée, les configurations utilisées, et ainsi de suite. Il n’est pas recommandé d’analyser cette section, car elle peut changer. | Tableau |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mappage du schéma et du type de données

Voir la section [Mappage du schéma et du type de données](copy-activity-schema-and-type-mapping.md) qui décrit la manière dont l’activité de copie mappe vos données source au récepteur.

## <a name="fault-tolerance"></a>Tolérance de panne

Par défaut, l’activité de copie arrête la copie de données et retourne une erreur quand elle rencontre des données incompatibles entre la source et le récepteur. Vous pouvez définir une configuration explicite pour ignorer et journaliser les lignes incompatibles et ne copier que les données compatibles pour assurer la réussite de la copie. Pour plus de détails, voir [Tolérance de panne de l’activité de copie](copy-activity-fault-tolerance.md).

## <a name="performance-and-tuning"></a>Performances et réglage

Consultez [Guide des performances et de l’optimisation de l’activité de copie](copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

## <a name="incremental-copy"></a>Copie incrémentielle 
Data Factory version 2 prend en charge les scénarios de copie incrémentielle de données delta d’une banque de données source vers une banque de données de destination. Consultez [Didacticiel : Copier de façon incrémentielle des données](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Lire et écrire des données partitionnées
Dans la version 1, Azure Data Factory prenait en charge la lecture et l’écriture de données partitionnées à l’aide des variables système SliceStart/SliceEnd/WindowStart/WindowEnd. Dans la version 2, ce comportement est obtenu à l’aide d’un paramètre de pipeline ayant comme valeur une heure de début ou une heure planifiée de déclencheur. Pour plus d’informations, consultez la page [Guide pratique pour lire ou écrire des données partitionnées](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>étapes suivantes
Voir les procédures de démarrage rapide, didacticiels et exemples suivants :

- [Copier des données d’un emplacement vers un autre dans le même Stockage Blob Azure](quickstart-create-data-factory-dot-net.md)
- [Copier des données de Stockage Blob Azure vers Microsoft Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copier des données d’une base de données SQL Server locale vers Azure](tutorial-hybrid-copy-powershell.md)
