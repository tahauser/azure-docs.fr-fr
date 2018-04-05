---
title: Exécuter des travaux Azure Batch de bout en bout sans écrire de code (préversion) | Microsoft Docs
description: Créez des modèles de fichier pour Azure CLI afin de créer des pools, travaux et tâches Batch.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: c991a1535b82a76a3d0b9bdbf4ede8f3e22bc866
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Utiliser des modèles d’interface CLI Azure Batch et le transfert de fichiers (préversion)

Vous pouvez utiliser l’interface Azure CLI pour exécuter des travaux Batch sans écrire de code.

Créez et utilisez des modèles de fichier avec Azure CLI pour créer des pools, travaux et tâches Batch. Les fichiers d’entrée des travaux peuvent être facilement chargés sur le compte de stockage associé au compte Batch et aux fichiers de sortie de travaux téléchargés.

## <a name="overview"></a>Vue d'ensemble

Une extension de l’interface Azure CLI permet aux utilisateurs qui ne sont pas des développeurs d’utiliser Batch de bout en bout. Un pool peut être créé, les données d’entrée chargées, les travaux et tâches associées créés et les données de sortie produites téléchargées. Aucun code n’est nécessaire, car l’interface CLI est utilisée directement ou intégrée à des scripts.

Les modèles Batch s’appuient sur la [prise en charge actuelle de Batch dans l’interface Azure CLI](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) qui permet aux fichiers JSON de spécifier des valeurs de propriété pour la création de pools, travaux, tâches et autres éléments. Avec les modèles Batch, les fonctionnalités suivantes sont ajoutées à celles déjà disponibles avec les fichiers JSON :

-   Des paramètres peuvent être définis. Lorsque le modèle est utilisé, seules les valeurs de paramètre sont spécifiées pour créer l’élément et les autres valeurs de propriété d’élément sont spécifiées dans le corps du modèle. Un utilisateur qui comprend Batch et les applications exécutées par Batch peut créer des modèles, en spécifiant les valeurs de propriété des pools, des travaux et des tâches. Un utilisateur moins familiarisé avec Batch et/ou les applications doit uniquement spécifier les valeurs des paramètres définis.

-   Les fabriques de tâches de travail créent une ou plusieurs tâches associées à un travail sans avoir à créer des définitions de tâches, simplifiant considérablement les soumissions de travaux.


Des fichiers de données d’entrée doivent être fournis pour les travaux et des fichiers de données de sortie sont souvent produits. Par défaut, un compte de stockage est associé à chaque compte Batch et les fichiers peuvent être facilement transférés vers ou à partir de ce compte de stockage à l’aide de l’interface CLI, sans que du codage ou des informations d’identification de stockage ne soient requis.

Par exemple, [ffmpeg](http://ffmpeg.org/) est une application courante qui traite les fichiers audio et vidéo. L’interface CLI d’Azure Batch peut servir à appeler ffmpeg pour transcoder les fichiers vidéo sources dans différentes résolutions.

-   Un modèle de pool est créé. L’utilisateur qui crée le modèle sait comment appeler l’application ffmpeg et connaît les prérequis. Il spécifie le système d’exploitation approprié, la taille de machine virtuelle, la façon dont ffmpeg est installé (à partir d’un package d’application ou à l’aide d’un gestionnaire de package, par exemple) et d’autres valeurs de propriété du pool. Les paramètres sont créés de façon à ce que, lorsque le modèle est utilisé, seul l’ID du pool et le nombre de machines virtuelles doivent être spécifiés.

-   Un modèle de travail est créé. L’utilisateur qui crée le modèle sait comment appeler ffmpeg pour transcoder la vidéo source dans une résolution différente et spécifie la ligne de commande de la tâche. Il sait également qu’il y a un dossier contenant les fichiers vidéo sources, avec une tâche requise pour chaque fichier d’entrée.

-   Un utilisateur final avec un ensemble de fichiers vidéo à transcoder crée d’abord un pool à l’aide du modèle de pool, en spécifiant uniquement l’ID du pool et le nombre de machines virtuelles requises. Il peut ensuite charger les fichiers sources à transcoder. Un travail peut ensuite être soumis à l’aide du modèle de travail, en spécifiant uniquement l’ID du pool et l’emplacement des fichiers source chargés. Le travail Batch est créé, avec une tâche par fichier d’entrée généré. Enfin, les fichiers de sortie transcodés peuvent être téléchargés.

## <a name="installation"></a>Installation

Les fonctionnalités de transfert de fichiers et de modèle requièrent l’installation d’une extension.

Pour obtenir des instructions sur l’installation de l’interface Azure CLI, consultez la page [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Une fois que l’interface Azure CLI est installée, la dernière version de l’extension Batch peut être installée en suivant les commandes de l’interface de ligne de commande suivantes :

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.1/azure_batch_cli_extensions-2.0.1-py2.py3-none-any.whl
```

Pour plus d’informations sur l’extension Batch, consultez [Extensions Microsoft Azure Batch CLI pour Windows, Mac et Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Modèles

L’interface CLI Azure Batch autorise la création d’éléments tels que des pools, travaux et tâches en spécifiant un fichier JSON qui contient les valeurs et noms de propriétés. Par exemple : 

```azurecli
az batch pool create –-json-file AppPool.json
```

Les modèles Azure Batch sont semblables aux modèles Azure Resource Manager en termes de syntaxe et de fonctionnalités. Il s’agit de fichiers JSON qui contiennent des valeurs et noms de propriétés, mais qui intègrent en plus les concepts principaux suivants :

-   **Paramètres**

    -   Ils autorisent la spécification des valeurs de propriété dans une section Corps, seules les valeurs de paramètre devant être fournies lorsque le modèle est utilisé. Par exemple, la définition complète d’un pool peut être placée dans le corps et un seul paramètre défini pour l’ID du pool. Par conséquent, seule la chaîne d’ID du pool doit être fournie pour créer un pool.
        
    -   Le corps du modèle peut être créé par une personne connaissant Batch et les applications à exécuter dans celui-ci. Seules les valeurs des paramètres définis par l’auteur doivent être fournis lorsque le modèle est utilisé. Un utilisateur sans connaissance poussée de Batch ou des applications peut par conséquent utiliser les modèles.

-   **Variables**

    -   Ils autorisent la spécification de valeurs de paramètre simples ou complexes dans un seul emplacement et leur utilisation dans un ou plusieurs emplacements dans le corps du modèle. Des variables peuvent simplifier et réduire la taille du modèle, ainsi que faciliter sa gestion, en ayant un emplacement où modifier les propriétés dont la valeur peut changer.

-   **Constructions de niveau supérieur**

    -   Certaines constructions de niveau supérieur sont disponibles dans le modèle, mais ne sont pas encore disponibles dans les API Batch. Par exemple, une fabrique de tâches peut être définie dans un modèle de travail qui crée plusieurs tâches pour le travail à l’aide d’une définition de tâche commune. Ces constructions évitent de devoir écrire du code pour créer dynamiquement plusieurs fichiers JSON, par exemple un fichier par tâche, ainsi que de créer des fichiers de script afin d’installer des applications via un gestionnaire de package par exemple.

    -   À un moment donné, le cas échéant, ces constructions pourront être ajoutées au service Batch et disponibles dans les interfaces utilisateur, API Batch, etc.

### <a name="pool-templates"></a>Modèles de pool

Outre les fonctionnalités de modèle standard des paramètres et variables, les constructions de niveau supérieur suivantes sont prises en charge par le modèle de pool :

-   **Références de package**

    -   Autorise éventuellement la copie du logiciel sur les nœuds du pool à l’aide de gestionnaires de packages. Le gestionnaire de package et l’ID du package sont spécifiés. Être en mesure de déclarer un ou plusieurs packages évite de devoir créer un script qui obtient les packages requis, d’installer le script et de l’exécuter sur chaque nœud du pool.

Voici un exemple de modèle qui crée un pool de machines virtuelles Linux avec ffmpeg installé et qui nécessite seulement de spécifier une chaîne d’ID de pool et le nombre de machines virtuelles à fournir :

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04.0-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Si le fichier de modèle est nommé _pool-ffmpeg.json_, le modèle peut être appelé comme suit :

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Modèles de travail

Outre les fonctionnalités de modèle standard des paramètres et variables, les constructions de niveau supérieur suivantes sont prises en charge par le modèle de travail :

-   **Fabrique de tâches**

    -   Crée plusieurs tâches pour un travail à partir d’une définition de tâche. Trois types de fabrique de tâches sont pris en charge : balayage paramétrique, tâche par fichier et collection de tâches.

Voici un exemple de modèle qui crée un travail qui utilise ffmpeg pour transcoder des fichiers vidéo MP4 dans une des deux résolutions inférieures, avec une tâche créée par fichier vidéo source :

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Si le fichier de modèle est nommé _job-ffmpeg.json_, le modèle peut être appelé comme suit :

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Groupes de fichiers et transfert de fichiers

La plupart des travaux et des tâches requièrent les fichiers d’entrée et produisent des fichiers de sortie. Les fichiers d’entrée et de sortie doivent généralement être transférés, soit du client vers le nœud, soit du nœud vers le client. L’extension CLI d’Azure Batch met de côté le transfert de fichiers et utilise le compte de stockage créé par défaut pour chaque compte Batch.

Un groupe de fichiers correspond à un conteneur créé dans le compte de stockage Azure. Le groupe de fichiers peut contenir des sous-dossiers.

L’extension Batch CLI fourni des commandes permettant de charger des fichiers du client vers un groupe de fichiers spécifié et de télécharger les fichiers du groupe de fichiers spécifié vers le client.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Les modèles de pool et de travail permettent de spécifier les fichiers stockés dans des groupes de fichiers pour la copie sur les nœuds de pool ou le renvoi des nœuds de pool vers un groupe de fichiers. Par exemple, dans le modèle de travail spécifié précédemment, le groupe de fichiers « ffmpeg-input » est spécifié pour la fabrique de tâches en tant qu’emplacement des fichiers vidéo sources copiés sur le nœud pour le transcodage ; le groupe de fichiers « ffmpeg-output » est utilisé en tant qu’emplacement où les fichiers de sortie transcodés sont copiés à partir du nœud exécutant chaque tâche.

## <a name="summary"></a>Résumé

La prise en charge du transfert de fichiers et des modèles a seulement été ajoutée à l’interface Azure CLI pour l’instant. L’objectif est d’étendre le public pouvant utiliser Batch aux utilisateurs qui n’ont pas besoin de développer du code à l’aide des API Batch, tels que les chercheurs, les utilisateurs, etc. Sans effectuer de codage, les utilisateurs qui connaissent bien Azure, Batch et les applications peuvent créer des modèles pour la création de pools et de travaux. Avec les paramètres de modèle, les utilisateurs sans connaissance approfondie de Batch et des applications d’utiliser les modèles.

Testez l’extension Batch pour l’interface de ligne de commande Azure et faites-nous part de vos commentaires et de vos suggestions dans les commentaires de cet article ou via le [forum Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Étapes suivantes

- Consultez l’article du blog au sujet des modèles de lot : [Exécution des travaux Azure Batch à l’aide de l’interface de ligne de commande Azure - aucun code requis](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Une documentation détaillée sur l’installation et l’utilisation, des exemples et du code source sont disponibles dans le [dépôt GitHub Azure](https://github.com/Azure/azure-batch-cli-extensions).
