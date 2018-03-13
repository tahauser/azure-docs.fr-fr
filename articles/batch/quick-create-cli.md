---
title: "Démarrage rapide Azure - Exécution d’un travail Batch - CLI"
description: "Apprenez rapidement à exécuter un travail Batch avec l’interface de ligne de commande Azure."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/16/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8d0e827dd3658d711de3830453c92af581786ad0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Démarrage rapide : exécution de votre premier travail Batch avec l’interface de ligne de commande Azure

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce démarrage rapide montre comment utiliser l’interface de ligne de commande Azure pour créer un compte Batch, un *pool* de nœuds de calcul (machines virtuelles) et un *travail* qui exécute des *tâches* sur le pool. Chaque exemple de tâche exécute une commande de base sur un des nœuds du pool. À l’issue de ce démarrage rapide, vous maîtriserez les concepts clés du service Batch et serez prêt à essayer Azure Batch avec des charges de travail plus réalistes à plus grande échelle.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.20 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Vous pouvez lier un compte de stockage à usage général Azure avec votre compte Batch. Bien que ce ne soit pas obligatoire pour ce démarrage rapide, le compte de stockage est utile pour déployer des applications et stocker des données d’entrée et de sortie pour la plupart des charges de travail réelles. Créez un compte de stockage dans votre groupe de ressources avec la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Création d’un compte Batch

Créez un compte Batch avec la commande [az batch account create](/cli/azure/batch/account#az_batch_account_create). Vous avez besoin d’un compte pour créer des ressources de calcul (pools de nœuds de calcul) et des travaux Batch.

L’exemple suivant crée un compte Batch nommé *mybatchaccount* dans *myResourceGroup* et lie le compte de stockage que vous avez créé.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Pour créer et gérer des pools de calcul et des travaux, vous devez vous authentifier avec Azure Batch. Connectez-vous au compte avec la commande [az batch account login](/cli/azure/batch/account#az_batch_account_login). Une fois que vous êtes connecté, vos commandes `az batch` utilisent ce contexte de compte.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Création d’un pool de nœuds de calcul

Maintenant que vous avez un compte Batch, créez un pool d’exemple de nœuds de calcul Linux à l’aide de la commande [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create). L’exemple suivant crée un pool appelé *mypool* avec 2 nœuds de taille *Standard_A1_v2* exécutant Ubuntu 16.04 LTS. La taille de nœud suggérée offre un bon compromis entre performances et coûts pour cet exemple rapide.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04.0-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

Azure Batch crée le pool immédiatement, mais prend quelques minutes pour allouer et démarrer les nœuds de calcul. Pendant ce temps, le pool est à l’état `resizing`. Pour afficher l’état du pool, exécutez la commande [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show). Cette commande affiche toutes les propriétés du pool, vous pouvez également rechercher des propriétés spécifiques. La commande suivante obtient l’état de l’allocation du pool :

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Continuez les étapes suivantes pour créer un travail et des tâches durant la modification de l’état du pool. Le pool est prêt à exécuter des tâches lorsque l’état de l’allocation est `steady` et que tous les nœuds sont en cours d’exécution. 

## <a name="create-a-job"></a>Création d’un travail

Maintenant que vous disposez d’un pool, créez un travail à exécuter sur celui-ci.  Un travail Batch est un groupe logique d’une ou de plusieurs tâches. Un travail inclut les paramètres communs aux tâches, tels que la priorité et le pool pour exécuter des tâches. Créez un travail Batch à l’aide de la commande [az batch job create](/cli/azure/batch/job#az_batch_job_create). L’exemple suivant crée un travail *myjob* sur le pool *mypool*. Dans un premier temps, le travail n’a aucune tâche.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Création de tâches

Utilisez maintenant la commande [az batch task create](/cli/azure/batch/task#az_batch_task_create) pour créer des tâches à exécuter dans le travail. Dans cet exemple, vous créez quatre tâches identiques. Chaque tâche exécute une `command-line` pour afficher les variables d’environnement Azure Batch sur un nœud de calcul, puis attend 90 secondes. Lorsque vous utilisez Azure Batch, cette ligne de commande se trouve là où vous spécifiez votre application ou votre script. Azure Batch fournit plusieurs façons de déployer des applications et des scripts sur des nœuds de calcul.

Le script Bash suivant crée 4 tâches parallèles (*mytask1* à *mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

La sortie de commande affiche les paramètres pour chaque tâche. Azure Batch distribue les tâches pour les nœuds de calcul.

## <a name="view-task-status"></a>Affichage de l’état de la tâche

Après avoir créé une tâche, Azure Batch la met en file d’attente pour l’exécuter sur le pool. Une fois qu’un nœud est disponible pour l’exécuter, la tâche s’exécute.

Utilisez la commande [az batch task show](/cli/azure/batch/task#az_batch_task_show) pour afficher l’état des tâches Azure Batch. L’exemple suivant affiche des détails sur *mytask1* en cours d’exécution sur un des nœuds du pool.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

La sortie de la commande inclut de nombreux détails, mais notez le `exitCode` de la ligne de commande de la tâche et le `nodeId`. Un `exitCode` de 0 indique que la ligne de commande de la tâche s’est correctement effectuée. Le `nodeId` indique l’ID du nœud de pool sur lequel la tâche a été exécutée.

## <a name="view-task-output"></a>Afficher les sorties des tâches

Pour répertorier les fichiers créés par une tâche sur un nœud de calcul, utilisez la commande [az batch task file list](/cli/azure/batch/task#az_batch_task_file_list). La commande suivante répertorie les fichiers créés par *mytask1* : 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

Le résultat ressemble à ce qui suit :

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Pour télécharger un des fichiers de sortie dans un répertoire local, utilisez la commande [az batch task file download](/cli/azure/batch/task#az_batch_task_file_download). Dans cet exemple, la sortie de la tâche est au format `stdout.txt`. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Vous pouvez afficher le contenu de `stdout.txt` dans un éditeur de texte. Le contenu affiche les variables d’environnement Azure Batch qui sont définies sur le nœud. Lorsque vous créez vos propres travaux Batch, vous pouvez référencer ces variables d’environnement dans des lignes de commande de tâche, ainsi que dans les applications et les scripts exécutés par les lignes de commande.

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask3/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjob
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-1392786932_2-20171026t223740z
AZ_BATCH_POOL_ID=mypool-linux
AZ_BATCH_TASK_ID=mytask3
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous souhaitez poursuivre les exemples et didacticiels Azure Batch, utilisez le compte Batch et le compte de stockage lié créés dans ce démarrage rapide. Il n’existe aucun frais pour le compte Batch proprement dit.

Vous êtes facturé pour les pools pendant que les nœuds sont en cours d’exécution, même si aucun travail n’est planifié. Lorsque vous n’avez plus besoin d’un pool, supprimez-le avec la commande [az batch pool delete](/cli/azure/batch/pool#az_batch_pool_delete) :

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le compte Batch, les pools et toutes les ressources associées. Supprimez les ressources comme suit :

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un compte Batch, un pool Batch et un travail Batch. Le travail a exécuté des tâches d’exemple et vous avez affiché des sorties créées sur un des nœuds. Maintenant que vous maîtriserez les concepts clés du service Batch, vous êtres prêt à essayer Azure Batch avec des charges de travail plus réalistes à plus grande échelle. Pour plus d’informations sur Microsoft Azure Batch, consultez les didacticiels Azure Batch. 


> [!div class="nextstepaction"]
> [Didacticiels Azure Batch](./tutorial-parallel-dotnet.md)
