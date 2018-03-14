---
title: Charges de travail de conteneur sur Azure Batch | Microsoft Docs
description: "Découvrez comment exécuter des applications à partir d’images conteneur sur Azure Batch."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 02/26/2018
ms.author: danlep
ms.openlocfilehash: a26d786ffcb74bb28fb9bd065e49398d52d2b662
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Exécuter des applications de conteneur sur Azure Batch

Azure Batch vous permet d’exécuter et de mettre à l’échelle de très nombreux travaux informatiques par lots sur Azure. Jusqu'à présent, les tâches de traitement par lots étaient exécutées directement sur des machines virtuelles dans un pool de traitement par lots. Désormais, vous pouvez configurer un pool de traitement par lots pour exécuter des tâches dans des conteneurs Docker.

L’utilisation de conteneurs permet d’exécuter simplement des tâches par lot sans avoir à gérer les packages d’applications et les dépendances. Les conteneurs déploient les applications sous la forme d’unités légères, portables et autonomes pouvant s’exécuter dans différents environnements. Par exemple, vous pouvez générer et tester localement un conteneur de test, puis télécharger l’image conteneur vers un registre dans Azure ou ailleurs. Le modèle de déploiement de conteneur permet de s’assurer que l’environnement d’exécution de votre application est toujours correctement installé et configuré, où que votre application soit hébergée. Ce didacticiel vous montre comment utiliser le kit de développement logiciel Batch .NET pour créer un pool de nœuds de calcul prenant en charge les tâches en cours d’exécution du conteneur, et comment exécuter des tâches du conteneur dans le pool.

Cet article suppose une connaissance des concepts du conteneur Docker et de la manière de créer un pool et un travail de traitement par lots et le travail avec le kit de développement logiciel SDK .NET. Les extraits de code sont destinés à être utilisés dans une application cliente similaire à l’[exemple DotNetTutorial](batch-dotnet-get-started.md) et sont des exemples de code dont vous auriez besoin pour prendre en charge des applications de conteneur dans Batch.


## <a name="prerequisites"></a>Prérequis

* Versions du SDK : le SDK prend en charge des images conteneur dans les versions suivantes :
    * API REST (version : 6.0 du 01/09/2017)
    * Kit de développement logiciel Batch .NET SDK (version 8.0.0)
    * Kit de développement logiciel Batch Python (version 4.0)
    * Kit de développement logiciel Batch Java (version 3.0)
    * Kit de développement logiciel Batch Node.js (version 3.0)

* Comptes : sur votre compte Azure, vous devez créer un compte Batch et, éventuellement, un compte de stockage à usage général.

* Une image de machine virtuelle prise en charge. Les conteneurs sont uniquement pris en charge dans les pools créés lors de la configuration des machines virtuelles à partir d’images détaillées dans la section « Images de machines virtuelles prises en charge ».

* Si vous fournissez une image personnalisée, votre application doit utiliser l’authentification Azure Active Directory (Azure AD) afin d’exécuter des charges de travail basées sur le conteneur. Si vous utilisez une image de la Place de marché Azure, vous n’avez pas besoin d’authentification Azure AD ; l’authentification par clé partagée fonctionnera. La prise en charge d’Azure Batch pour Azure AD est documentée dans [Authentifier les solutions de service Batch avec Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Images de machines virtuelles prises en charge

Vous devez fournir une image Windows ou Linux pour créer un pool de nœuds de calcul de machines virtuelles.

### <a name="windows-images"></a>Images Windows

Pour les charges de travail de conteneur Windows, Batch prend actuellement en charge les images que vous créez à partir de machines virtuelles exécutant Docker sur Windows, ou bien vous pouvez utiliser Windows Server 2016 Datacenter avec des images Containers de la Place de marché Azure. Cette image est compatible avec l’`batch.node.windows amd64`ID de référence de l’agent de nœud. Le type de conteneur pris en charge est actuellement limité à Docker.

### <a name="linux-images"></a>Images Linux

Pour les charges de travail de conteneur Linux, Batch ne prend actuellement en charge que les images personnalisées que vous créez à partir de machines virtuelles exécutant Docker sur les distributions Linux suivantes : Ubuntu 16.04 LTS ou CentOS 7.3. Si vous souhaitez fournir votre propre image Linux personnalisée, consultez les instructions dans [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).

Vous pouvez utiliser [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Si vous souhaitez tirer parti des performances du GPU de taille Azure CN ou NV VM, vous devez installer les pilotes NVIDIA sur l’image. En outre, vous devez installer et exécuter l’utilitaire moteur Docker pour GPU NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

Pour accéder au réseau RDMA Azure, utilisez des machines virtuelles des tailles suivantes : A8, A9, H16r, H16mr ou NC24r. Les pilotes RDMA nécessaires sont installés dans les images CentOS 7.3 HPC et Ubuntu 16.04 LTS à partir de la Place de marché Azure. Une configuration supplémentaire peut être nécessaire pour exécuter des charges de travail MPI. Voir [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limites

* Batch prend en charge RDMA uniquement pour les conteneurs exécutés sur les pools Linux.


## <a name="authenticate-using-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory

Si vous utilisez une image de machine virtuelle personnalisée pour créer le pool Batch, votre application cliente doit s’authentifier à l’aide de l’authentification intégrée Azure AD (l’authentification par clé partagée ne fonctionne pas). Avant d’exécuter l’application, veillez à l’enregistrer dans Azure AD pour lui donner une identité et spécifier ses autorisations pour d’autres applications.

En outre, lorsque vous utilisez une image de machine virtuelle personnalisée, vous devez accorder le contrôle d’accès IAM à l’application pour accéder à l’image de machine virtuelle. Dans le portail Azure, ouvrez **Toutes les ressources**, sélectionnez l’image conteneur et, à partir de la section **Contrôle d’accès (IAM)** du panneau de l’image, cliquez sur **Ajouter**. Dans le panneau **Ajouter des autorisations**, spécifiez un **rôle**, dans **Attribuer l’accès**, sélectionnez **un utilisateur, un groupe ou une application Azure AD**, puis, dans  **Sélectionner**, entrez le nom de l’application.

Dans votre application, passez un jeton d’authentification Azure AD lorsque vous créez le client Batch à l’aide de [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), comme décrit dans [Authentification de solutions de service Batch avec Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Faire référence à une image de machine virtuelle pour la création d’un pool

Dans votre code d’application, fournissez une référence à l’image de machine virtuelle à utiliser pour créer les nœuds de calcul du pool. Pour ce faire, vous devez créer un objet [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Vous pouvez spécifier l’image à utiliser de l’une des manières suivantes :

* Si vous utilisez une image personnalisée, fournissez un identificateur de ressource Azure Resource Manager pour l’image de machine virtuelle. L’identificateur de l’image a un format de chemin d’accès, comme indiqué dans l’exemple suivant :

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Pour obtenir cet ID de l’image à partir du portail Azure, ouvrez **Toutes les ressources**, sélectionnez l’image personnalisée et, à partir de la section **Vue d’ensemble** du panneau d’image, copiez le chemin d’accès dans l’**ID de ressource**.

* Si vous utilisez une image de la [Place de Marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based), fournissez un groupe de paramètres décrivant l’image : éditeur, type d’offre, référence (SKU) et version de l’image, comme indiqué dans la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images) :

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Configuration du conteneur pour le pool Batch

Un pool Batch est une collection de nœuds de calcul sur lesquels Batch exécute les tâches d’un travail. Lorsque vous créez le pool, vous lui fournissez la configuration de la machine virtuelle pour les nœuds de calcul. L’objet [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) contient une référence à l’objet [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration). Pour activer les charges de travail de conteneur sur le pool, spécifiez les `ContainerConfiguration` paramètres. C’est également dans la configuration de la machine virtuelle que vous spécifiez la référence de l’image et la référence SKU de l’agent de nœud de l’image, comme indiqué dans les exemples suivants.

Il existe plusieurs options pour la création d’un pool. Vous pouvez créer un pool avec ou sans images conteneur prérécupérées. 

Le processus d’extraction (ou prérécupération) vous permet de précharger les images conteneur à partir du Hub Docker ou d’un autre registre de conteneurs sur Internet. L’avantage de la prérécupération d’images conteneur est que, lors de la première exécution des tâches, ces dernières ne doivent pas attendre que l’image conteneur soit téléchargée. La configuration du conteneur extrait des images conteneur vers les machines virtuelles une fois le pool créé. Les tâches exécutées sur le pool peuvent ensuite référencer la liste des images et des options d’exécution du conteneur.



### <a name="pool-without-prefetched-container-images"></a>Pool sans images conteneur prérécupérées

Pour configurer le pool sans images conteneur prérécupérées, définissez des objets `ContainerConfiguration` et `VirtualMachineConfiguration`, comme indiqué dans l’exemple suivant. Dans cet exemple et les suivants, on suppose que vous utilisez une image Ubuntu 16.04 LTS personnalisée et que Docker Engine est installé.

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Prérécupérer des images pour la configuration du conteneur

Pour prérécupérer des images conteneur sur le pool, ajoutez la liste d’images conteneur (`containerImageNames`) à `ContainerConfiguration` et donnez-lui un nom. Dans l’exemple suivant, on suppose que vous utilisez une image Ubuntu 16.04 LTS personnalisée, que vous prérécupérez une image TensorFlow sur le [Hub Docker](https://hub.docker.com) et que vous lancez TensorFlow dans le cadre d’une tâche de démarrage.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Prérécupérer des images à partir d’un registre de conteneurs privé

Vous pouvez également prérécupérer des images conteneur en vous authentifiant sur un serveur de registres de conteneurs privé. Dans l’exemple suivant, les objets `ContainerConfiguration` et `VirtualMachineConfiguration` utilisent une image Ubuntu 16.04 LTS personnalisée et prérécupèrent une image TensorFlow privée dans un registre de conteneurs Azure privé.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Paramètres de conteneur pour la tâche

Lorsque vous configurez des tâches à exécuter sur les nœuds de calcul, vous devez spécifier les paramètres spécifiques aux conteneurs, tels que des options d’exécution des tâches, des images à utiliser et un registre.

Pour configurer les paramètres spécifiques au conteneur, utilisez la propriété ContainerSettings des classes de tâches. Ces paramètres sont définis par la classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Si vous exécutez des tâches sur des images conteneur, la [tâche de cloud](/dotnet/api/microsoft.azure.batch.cloudtask) et la [tâche du gestionnaire de travaux](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) nécessitent des paramètres de conteneur. Toutefois, la [tâche de démarrage](/dotnet/api/microsoft.azure.batch.starttask), la [tâche de préparation du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) et la [tâche de mise en production du travail](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) ne nécessitent pas de paramètres de conteneur (autrement dit, elles peuvent s’exécuter dans un contexte de conteneur ou directement sur le nœud).

Lorsque vous configurez les paramètres de conteneur, tous les répertoires récursifs sous le `AZ_BATCH_NODE_ROOT_DIR` (la racine des répertoires Azure Batch sur le nœud) et toutes les variables d’environnement des tâches sont mappes dans le conteneur, et la ligne de commande des tâches est exécutée dans le conteneur.

L’exemple de code dans [Prérécupérer des images pour la configuration du conteneur](#prefetch-images-for-container-configuration) a montré comment spécifier une configuration de conteneur pour une tâche de démarrage. L’exemple de code suivant montre comment spécifier la configuration du conteneur pour une tâche de cloud :

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Étapes suivantes

* Consultez également la boîte à outils [Batch Shipyard](https://github.com/Azure/batch-shipyard) pour faciliter le déploiement de charges de travail de conteneur sur Azure Batch par le biais de [recettes Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Pour plus d’informations sur l’installation et l’utilisation de Docker CE sur Linux, consultez la documentation [Docker](https://docs.docker.com/engine/installation/).

* Pour en savoir plus sur l’utilisation d’images personnalisées, voir [Utiliser une image personnalisée managée pour créer un pool de machines virtuelles](batch-custom-images.md).
