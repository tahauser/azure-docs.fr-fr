---
title: Exécuter des tâches en parallèle pour utiliser les ressources de calcul de manière efficace - Azure Batch | Microsoft Docs
description: Améliorer l’efficacité et réduire les coûts en utilisant moins de nœuds de calcul et en exécutant des tâches simultanées sur chaque nœud dans un pool Azure Batch
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5106bbbb073908af7e7e8f045fa6fb60e8a306f4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Exécuter des tâches simultanément pour optimiser l’utilisation des nœuds de calcul Batch 

En exécutant simultanément plusieurs tâches sur chaque nœud de calcul dans votre pool Azure Batch, vous pouvez optimiser l’utilisation des ressources sur un plus petit nombre de nœuds du pool. Pour certaines charges de travail, vous obtiendrez ainsi des durées de travail réduites et un coût inférieur.

Alors que certains scénarios tirent parti du fait de dédier toutes les ressources d’un nœud disponibles à une seule tâche, certaines situations profitent de l’autorisation accordée à plusieurs tâches de partager ces ressources :

* **Réduction du transfert de données** lorsque les tâches sont en mesure de partager des données. Dans ce scénario, vous pouvez considérablement réduire les frais de transfert de données en copiant les données partagées vers un plus petit nombre de nœuds et en exécutant les tâches en parallèle sur chaque nœud. Cela s'applique surtout si les données à copier sur chaque nœud doivent être transférées entre des régions géographiques.
* **Optimisation de l’utilisation de la mémoire** lorsque les tâches nécessitent une grande quantité de mémoire, mais seulement pendant de courtes périodes et à des moments variables au cours de l’exécution. Vous pouvez employer des nœuds de calcul moins nombreux mais de plus grande taille, avec plus de mémoire pour gérer efficacement ces pics. De cette façon, ces nœuds ont plusieurs tâches exécutées en parallèle sur chaque nœud, mais chaque tâche bénéficie de la mémoire abondante des nœuds à des moments différents.
* **Atténuation des limites au nombre de nœuds** lorsque la communication entre les nœuds est requise au sein d’un pool. Actuellement, les pools configurés pour la communication entre les nœuds sont limités à 50 nœuds de calcul. Si chaque nœud dans un pool de ce type est capable d’exécuter des tâches en parallèle, un plus grand nombre de tâches peuvent être exécutées simultanément.
* **Réplication d'un cluster de calcul local**, comme lorsque vous déplacez un environnement de calcul vers Azure pour la première fois. Si cette configuration exécute actuellement plusieurs tâches par nœud de calcul, vous pouvez augmenter le nombre maximal de tâches de nœud pour refléter plus précisément cette configuration.

## <a name="example-scenario"></a>Exemple de scénario
Comme exemple d’illustration des avantages de l’exécution de tâches parallèles, supposons que votre application de tâche soit dotée de critères de processeur et de mémoire tels que des nœuds [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) sont suffisants. Cependant, pour terminer le travail dans le délai imparti, 1 000 nœuds de ce type sont nécessaires.

Au lieu d’utiliser les nœuds Standard\_D1 avec 1 cœur de processeur, vous pouvez utiliser des nœuds [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) avec 16 cœurs chacun, et activer l’exécution de tâches parallèles. Vous pouvez donc utiliser *16 fois moins de nœuds* : à la place des 1 000 nœuds, seuls 63 sont requis. En outre, si des fichiers d’application volumineux ou des données de référence sont requis pour chaque nœud, l’efficacité et la durée du travail sont encore améliorées, car les données ne sont copiées que sur 63 nœuds.

## <a name="enable-parallel-task-execution"></a>Activer l’exécution des tâches parallèles
Vous configurez les nœuds de calcul pour l’exécution des tâches parallèles au niveau du pool. Avec la bibliothèque Batch .NET, définissez la propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] lorsque vous créez un pool. Si vous utilisez l’API REST Batch, définissez l’élément [maxTasksPerNode][rest_addpool] dans le corps de la requête lors de la création du pool.

Azure Batch vous permet de définir un nombre maximum de tâches par nœud allant jusqu'à quatre fois (4x) le nombre de cœurs de nœud. Par exemple, si le pool est configuré avec des nœuds de grande taille (quatre cœurs), alors la valeur `maxTasksPerNode` peut être définie sur 16. Pour plus d’informations sur le nombre de cœurs pour chacune des tailles de nœud, consultez [Tailles de services Cloud](../cloud-services/cloud-services-sizes-specs.md). Pour plus d’informations sur les limites du service, consultez [Quotas et les limites pour le service Azure Batch](batch-quota-limit.md).

> [!TIP]
> Veillez à prendre en compte la valeur `maxTasksPerNode` lors de la construction d’une [formule de mise à l’échelle automatique][enable_autoscaling] pour votre pool. Par exemple, une formule qui évalue `$RunningTasks` pourrait être considérablement affectée par une augmentation des tâches par nœud. Consultez [Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch](batch-automatic-scaling.md) pour plus d’informations.
>
>

## <a name="distribution-of-tasks"></a>Répartition des tâches
Lorsque les nœuds de calcul d’un pool peuvent exécuter des tâches simultanément, il est important de spécifier comment vous souhaitez que les tâches soient réparties entre les nœuds du pool.

La propriété [CloudPool.TaskSchedulingPolicy][task_schedule] vous permet de spécifier que les tâches doivent être affectées uniformément entre tous les nœuds du pool (« propagation »). Vous pouvez également spécifier qu'autant de tâches que possible doivent être attribuées à chaque nœud avant que les tâches ne soient attribuées à un autre nœud du pool (« compression »).

Pour illustrer l’importance de cette fonctionnalité, examinons le pool de nœuds [Standard_Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) (dans l’exemple ci-dessus) configuré avec une propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net] d’une valeur de 16. Si la propriété [CloudPool.TaskSchedulingPolicy][task_schedule] est configurée avec une propriété [ComputeNodeFillType][fill_type] de type *Pack*, l’utilisation des 16 cœurs de chaque nœud est optimisée et un [pool de mise à l’échelle automatique](batch-automatic-scaling.md) est autorisé pour nettoyer les nœuds inutilisés du pool (nœuds sans aucune tâche affectée). Ceci limite l'utilisation des ressources et permet d'économiser de l'argent.

## <a name="batch-net-example"></a>Exemple .NET Batch
Cet extrait de code de l’API [Batch .NET][api_net] illustre une demande de création d’un pool contenant quatre grands nœuds avec un maximum de quatre tâches par nœud. Une stratégie de planification de tâche est également spécifiée ; elle remplira chaque nœud de tâches avant d'attribuer des tâches à un autre nœud du pool. Pour plus d’informations sur l’ajout de pools à l’aide de l’API Batch .NET, consultez [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "large",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemple REST Batch
Cet extrait de code de l’API [REST Batch][api_rest] illustre une demande de création d’un pool contenant deux grands nœuds avec un maximum de quatre tâches par nœud. Pour plus d’informations sur l’ajout de pools à l’aide de l’API REST, consultez la page [Ajout d’un pool à un compte][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Vous ne pouvez définir l’élément `maxTasksPerNode` et la propriété [MaxTasksPerComputeNode][maxtasks_net] qu’au moment de la création du pool. Ils ne peuvent pas être modifiés après qu'un pool a déjà été créé.
>
>

## <a name="code-sample"></a>Exemple de code
Le projet [ParallelNodeTasks][parallel_tasks_sample] sur GitHub illustre l’utilisation de la propriété [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Cette application de console en C# utilise la bibliothèque [Batch .NET][api_net] pour créer un pool avec un ou plusieurs nœuds de calcul. Elle exécute un nombre configurable de tâches sur ces nœuds pour simuler la charge variable. La sortie de l'application spécifie quels nœuds ont exécuté chaque tâche. L'application fournit également un résumé des paramètres du travail et sa durée. La partie Résumé de la sortie de deux exécutions différentes de l’exemple d’application apparaît ci-dessous.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La première exécution de l'exemple d'application montre qu'avec un nœud unique dans le pool et le paramètre par défaut d'une tâche par nœud, la durée du travail dépasse 30 minutes.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

La deuxième exécution de l'exemple montre une diminution significative de la durée du travail. Cela est dû au fait que le pool a été configuré avec quatre tâches par nœud, ce qui permet l'exécution de tâches parallèles pour terminer le travail en un quart du temps, environ.

> [!NOTE]
> Les durées des tâches dans les résumés ci-dessus n’incluent pas le temps de création du pool. Chacune des tâches ci-dessus a été envoyée à des pools créés précédemment dont les nœuds de calcul étaient à l’état *Inactif* au moment de l’envoi.
>
>

## <a name="next-steps"></a>Étapes suivantes
### <a name="batchlabs-heat-map"></a>Carte thermique BatchLabs
[BatchLabs][batch_labs] est un outil client autonome, gratuit et doté de nombreuses fonctionnalités, aidant à créer, déboguer et surveiller les applications Azure Batch. BatchLabs contient une fonctionnalité *Carte thermique* qui fournit une visualisation de l’exécution des tâches. Lorsque vous exécutez l’exemple d’application [ParallelTasks][parallel_tasks_sample], utilisez la fonctionnalité Carte thermique pour visualiser l’exécution de tâches parallèles sur chaque nœud.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchLabs/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

