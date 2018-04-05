---
title: Variables d’environnement de nœud de calcul Azure Batch | Microsoft Docs
description: Référence de variable d’environnement de nœud de calcul pour Azure Batch Analytics.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: danlep
ms.openlocfilehash: ca8d6a6484cd1f145e7d807681bf2d012f2399e0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-batch-compute-node-environment-variables"></a>Variables d’environnement de nœud de calcul Azure Batch
Le [service Azure Batch](https://azure.microsoft.com/services/batch/) définit les variables d’environnement suivantes sur les nœuds de calcul. Vous pouvez référencer ces variables d’environnement dans des lignes de commande de tâche, ainsi que dans les programmes et les scripts exécutés par les lignes de commande.

Pour plus d’informations sur l’utilisation de variables d’environnement, voir [Paramètres d’environnement des tâches](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Visibilité des variables d’environnement

Ces variables d’environnement sont disponibles uniquement dans le contexte de **l’utilisateur de la tâche**, c’est-à-dire le compte d’utilisateur sur le nœud sous lequel une tâche est exécutée. Vous ne les voyez *pas* si vous vous [connectez à distance](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) à un nœud de calcul via RDP (Remote Desktop Protocol) ou SSH (Secure Shell), et répertoriez les variables d’environnement. Cela est dû au fait que le compte d’utilisateur qui est utilisé pour la connexion à distance n’est pas le même que le compte utilisé par la tâche.

## <a name="command-line-expansion-of-environment-variables"></a>Expansion par ligne de commande des variables d’environnement

Les lignes de commande exécutées par des tâches sur des nœuds de calcul ne s’exécutent pas sous un interpréteur de commandes. Elle ne peuvent donc pas tirer parti en mode natif de fonctionnalités de l’interpréteur de commandes telle que l’expansion de variables d’environnement (qui inclut le `PATH`). Pour tirer parti de telles fonctionnalités, vous devez **appeler l’interpréteur de commandes** dans la ligne de commande. Par exemple, lancez `cmd.exe` sur des nœuds de calcul Windows ou `/bin/sh` sur des nœuds Linux :

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Variables d’environnement

| Nom de la variable                     | Description                                                              | Disponibilité | Exemples |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Nom du compte Batch auquel la tâche appartient.                  | Toutes les tâches.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | Sous-répertoire du [répertoire de travail de la tâche][files_dirs] dans lequel les certificats sont stockés pour les nœuds de calcul Linux. Notez que cette variable d’environnement ne s’applique pas aux nœuds de calcul Windows.                                                  | Toutes les tâches.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | ID du travail auquel la tâche appartient. | Toutes les tâches, sauf la tâche de démarrage. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Chemin d’accès complet du [répertoire de la tâche][files_dirs] de préparation du travail sur le nœud. | Toutes les tâches, sauf la tâche de démarrage et la tâche de préparation du travail. Disponible uniquement si la tâche est configurée avec une tâche de préparation du travail. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_DIR   | Chemin d’accès complet du [répertoire de travail de la tâche][files_dirs] de préparation du travail sur le nœud. | Toutes les tâches, sauf la tâche de démarrage et la tâche de préparation du travail. Disponible uniquement si la tâche est configurée avec une tâche de préparation du travail. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | ID du nœud auquel la tâche est affectée. | Toutes les tâches. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Chemin d’accès complet de la racine de tous les [répertoires Batch][files_dirs] sur le nœud. | Toutes les tâches. | C:\user\tasks |
| AZ_BATCH_NODE_ROOT_DIR        | Chemin d’accès complet du [répertoire partagé][files_dirs] sur le nœud. Toutes les tâches qui s’exécutent sur un nœud ont accès en lecture/écriture à ce répertoire. Les tâches qui s’exécutent sur d’autres nœuds n’ont pas accès à distance à ce répertoire (il ne s’agit pas d’un répertoire réseau « partagé »). | Toutes les tâches. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Chemin d’accès complet du [répertoire de la tâche de démarrage][files_dirs] sur le nœud. | Toutes les tâches. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID du pool sur lequel la tâche s’exécute. | Toutes les tâches. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Chemin d’accès complet du [répertoire de la tâche][files_dirs] sur le nœud. Ce répertoire contient les fichiers `stdout.txt` et `stderr.txt` pour la tâche et le répertoire AZ_BATCH_TASK_WORKING_DI. | Toutes les tâches. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID de la tâche en cours. | Toutes les tâches, sauf la tâche de démarrage. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Chemin d’accès complet du [répertoire de travail de la tâche][files_dirs] sur le nœud. La tâche en cours d’exécution dispose d’un accès en lecture/écriture à ce répertoire. | Toutes les tâches. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Liste des nœuds et nombre de cœurs par nœud alloués à une [tâche multi-instance][multi_instance]. Les nœuds et les cœurs sont répertoriés au format `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, où le nombre de nœuds est suivi d’une ou plusieurs adresses IP de nœud et du nombre de cœurs pour chacun. |  Tâche principale multi-instance et tâches subordonnées. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Liste des nœuds affectés à une [tâche multi-instance][multi_instance] au format `nodeIP;nodeIP`. | Tâche principale multi-instance et tâches subordonnées. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Liste des nœuds affectés à une [tâche multi-instance][multi_instance] au format `nodeIP,nodeIP`. | Tâche principale multi-instance et tâches subordonnées. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | Adresse IP et port du nœud de calcul sur lequel s’exécute la tâche principale d’une [tâche multi-instance][multi_instance]. | Tâche principale multi-instance et tâches subordonnées. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Le chemin d’accès du répertoire est identique pour la tâche principale et chaque tâche subordonnée d’une [tâche multi-instance][multi_instance]. Le chemin d’accès existe sur chaque nœud sur lequel la tâche multi-instance s’exécute, et est accessible en lecture/écriture pour les commandes de la tâche en cours d’exécution sur ce nœud (tant la [commande coordination][coord_cmd] que la [commande application][app_cmd]). Des tâches subordonnées ou une tâche principale qui s’exécutent sur d’autres nœuds n’ont pas accès à distance à ce répertoire (il ne s’agit pas d’un répertoire réseau « partagé »). | Tâche principale multi-instance et tâches subordonnées. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Spécifie si le nœud actuel est le nœud principal pour une [tâche multi-instance][multi_instance]. Les valeurs possibles sont `true` et `false`.| Tâche principale multi-instance et tâches subordonnées. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Si `true`, le nœud actuel est un nœud dédié. Si `false`, c’est un [nœud basse priorité](batch-low-pri-vms.md). | Toutes les tâches. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
