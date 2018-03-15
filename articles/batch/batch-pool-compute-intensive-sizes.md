---
title: "Utiliser des machines virtuelles Azure nécessitant beaucoup de ressources système avec Batch | Microsoft Docs"
description: Comment tirer parti des tailles de machines virtuelles compatibles RDMA ou GPU dans les pools Azure Batch
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: 5a73e926b5979e573ccb0402ff2d23eae2463232
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Utiliser des instances compatibles RDMA ou GPU dans les pools Batch

Pour exécuter certains travaux Batch, vous souhaiterez peut-être tirer parti des tailles de machines virtuelles Azure conçues pour le calcul à grande échelle. Par exemple, pour exécuter des [charges de travail MPI](batch-mpi.md) multi-instances, vous pouvez choisir des tailles de série A8, A9 ou H qui ont une interface réseau pour l’accès direct à la mémoire à distance (RDMA). Ces tailles se connectent à un réseau InfiniBand pour la communication entre les nœuds, ce qui peut accélérer les applications MPI. Pour les applications CUDA, vous pouvez choisir des tailles de série N qui incluent des cartes d’unité de traitement graphique (GPU) NVIDIA Tesla.

Cet article fournit des conseils et des exemples pour utiliser certaines des tailles spécialisées d’Azure dans les pools Batch. Pour obtenir des informations générales et en savoir plus sur les caractéristiques techniques, consultez :

* Tailles de machines virtuelles de calcul haute performance ([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tailles de machines virtuelles Linux compatibles GPU ([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Limites de compte et d’abonnement

* **Quotas et limites** : les [quotas de cœurs par compte Batch](batch-quota-limit.md#resource-quotas) peuvent limiter le nombre de nœuds d’une taille donnée que vous pouvez ajouter à un pool Batch. La probabilité d’atteindre un quota est plus grande quand vous choisissez des tailles de machines virtuelles compatibles RDMA, compatibles GPU, ou d’autres tailles de machines virtuelles multicœurs. 

  De plus, l’utilisation de certaines familles de machines virtuelles dans votre compte Batch, comme NCv2, NCv3 et ND, est restreinte en raison d’une capacité limitée. L’utilisation de ces familles est disponible uniquement en demandant une augmentation du quota (la valeur par défaut étant de 0 cœur).  

  Si nécessaire, [demandez une augmentation du quota](batch-quota-limit.md#increase-a-quota) sans frais.

* **Disponibilité de la région** - Les machines virtuelles nécessitant beaucoup de ressources système peuvent ne pas être disponibles dans les régions où vous créez vos comptes Batch. Pour vérifier qu’une taille est disponible, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Les dépendances

Les fonctionnalités RDMA et GPU des tailles nécessitant beaucoup de ressources système sont prises en charge uniquement sur certains systèmes d’exploitation. En fonction de votre système d’exploitation, vous devrez peut-être installer ou configurer des pilotes supplémentaires ou d’autres logiciels. Les tableaux suivants listent ces dépendances. Pour plus de détails, consultez les articles associés. Pour connaître les options de configuration des pools Batch, voir plus loin dans cet article.


### <a name="linux-pools---virtual-machine-configuration"></a>Pools Linux - Configuration de machine virtuelle

| Taille | Fonctionnalité | Systèmes d’exploitation | Logiciels requis | Paramètres de pool |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>HPC SUSE Linux Enterprise Server 12 ou<br/>HPC basé sur CentOS<br/>(Place de marché Azure) | Intel MPI 5 | Activer la communication entre les nœuds, désactiver l’exécution simultanée des tâches |
| [NC, NCv2, NCv3, série ND*](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla (varie selon la série) | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 ou 7.4, ou<br/>CentOS 7.3 ou 7.4<br/>(Place de marché Azure) | Pilotes NVIDIA CUDA Toolkit | N/A | 
| [Série NV](../virtual-machines/linux/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 ou<br/>CentOS 7.3<br/>(Place de marché Azure) | Pilotes NVIDIA GRID | N/A |

*La connectivité RDMA sur les machines virtuelles de série N prenant en charge RDMA peut nécessiter une [configuration supplémentaire](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) qui varie selon la distribution.



### <a name="windows-pools---virtual-machine-configuration"></a>Pools Windows - Configuration de machine virtuelle

| Taille | Fonctionnalité | Systèmes d’exploitation | Logiciels requis | Paramètres de pool |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, ou<br/>2012 (Place de Marché Azure) | Microsoft MPI 2012 R2 ou ultérieur, ou<br/> Intel MPI 5<br/><br/>Extension de machine virtuelle Azure HpcVMDrivers | Activer la communication entre les nœuds, désactiver l’exécution simultanée des tâches |
| [NC, NCv2, NCv3, série ND*](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla (varie selon la série) | Windows Server 2016 ou <br/>2012 R2 (Place de Marché Azure) | Pilotes NVIDIA Tesla ou pilotes CUDA Toolkit| N/A | 
| [Série NV](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 ou<br/>2012 R2 (Place de Marché Azure) | Pilotes NVIDIA GRID | N/A |

*La connectivité RDMA sur les machines virtuelles de série N prenant en charge RDMA est prise en charge sur Windows Server 2016 ou Windows Server 2012 R2 (à partir de la Place de Marché Microsoft Azure) avec l’extension HpcVMDrivers et Microsoft MPI ou Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Pools Windows - Configuration des services cloud

> [!NOTE]
> Les tailles de série N ne sont pas prises en charge dans les pools Batch avec la configuration de service cloud.
>

| Taille | Fonctionnalité | Systèmes d’exploitation | Logiciels requis | Paramètres de pool |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012, ou<br/>2008 R2 (Famille de système d’exploitation invité) | Microsoft MPI 2012 R2 ou ultérieur, ou<br/>Intel MPI 5<br/><br/>Extension de machine virtuelle Azure HpcVMDrivers | Activer la communication entre les nœuds,<br/> désactiver l’exécution simultanée des tâches |





## <a name="pool-configuration-options"></a>Options de configuration de pool

Pour configurer une taille de machine virtuelle spécialisée pour votre pool Batch, les outils et API Batch fournissent plusieurs options pour installer les logiciels ou les pilotes nécessaires, notamment :

* [Tâche de démarrage](batch-api-basics.md#start-task) : chargez un package d’installation en tant que fichier de ressources sur un compte de stockage Azure dans la même région que le compte Batch. Créez une ligne de commande de tâche de démarrage pour installer le fichier de ressources en mode silencieux quand le pool démarre. Pour plus d’informations, consultez la [documentation de l’API REST](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > La tâche de démarrage doit s’exécuter avec des autorisations élevées (d’administrateur) et doit attendre de réussir.
  >

* [Package d’application](batch-application-packages.md) : ajoutez un package d’installation compressé à votre compte Batch et configurez une référence de package dans le pool. Ce paramètre charge et décompresse le package sur tous les nœuds dans le pool. Si le package est un programme d’installation, créez une ligne de commande de tâche de démarrage pour effectuer une installation de l’application en mode silencieux sur tous les nœuds du pool. Le cas échéant, installez le package quand une tâche est planifiée pour s’exécuter sur un nœud.

* [Image de pool personnalisée](batch-custom-images.md) : créez une image personnalisée de machine virtuelle Windows ou Linux qui contient les pilotes, logiciels ou autres paramètres nécessaires pour la taille de machine virtuelle. 

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configure automatiquement le GPU et RDMA pour fonctionner de façon fluide avec les charges de travail en conteneur sur Azure Batch. Batch Shipyard est entièrement piloté par les fichiers de configuration. Il existe de nombreux exemples de recettes de configurations disponibles qui activent les charges de travail GPU et RDMA, comme la [recette CNTK GPU](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) qui préconfigure les pilotes GPU sur les machines virtuelles de série N et charge le logiciel Microsoft Cognitive Toolkit en tant qu’image Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Exemple : Microsoft MPI sur un pool de machines virtuelles A8

Pour exécuter des applications MPI Windows sur un pool de nœuds Azure A8, vous devez installer une implémentation MPI prise en charge. Voici des exemples d’étapes permettant d’installer [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) sur un pool Windows à l’aide d’un package d’application Batch.

1. Téléchargez le [package d’installation](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) pour obtenir la dernière version de Microsoft MPI.
2. Créez un fichier zip du package.
3. Chargez le package sur votre compte Batch. Pour connaître les étapes nécessaires, consultez les instructions relatives aux [packages d’applications](batch-application-packages.md). Spécifiez un ID d’application (par exemple *MSMPI*) et une version (par exemple *8.1*). 
4. À l’aide des API Batch ou du portail Azure, créez un pool dans la configuration de services cloud avec le nombre de nœuds et l’échelle souhaités. Le tableau suivant contient des exemples de paramètres pour configurer MPI en mode sans assistance à l’aide d’une tâche de démarrage :

| Paramètre | Valeur |
| ---- | ----- | 
| **Type d’image** | Cloud Services |
| **Famille de système d’exploitation** | Windows Server 2012 R2 (famille de système d’exploitation 4) |
| **Taille du nœud** | A8 Standard |
| **Communication entre les nœuds activée** | True |
| **Nombre maximal de tâches par nœud** | 1 |
| **Références du package d’application** | MSMPI |
| **Tâche de démarrage activée** | True<br>**Ligne de commande** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Identité de l’utilisateur** - Pool autouser, admin<br/>**Attente de la réussite** - True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Exemple : Pilotes NVIDIA Tesla sur un pool de machines virtuelles NC

Pour exécuter des applications CUDA sur un pool de nœuds Linux NC, vous devez installer CUDA Toolkit 9.0 sur les nœuds. La boîte à outils installe les pilotes NVIDIA Tesla GPU nécessaires. Voici des exemples d’étapes permettant de déployer une image personnalisée d’Ubuntu 16.04 LTS avec les pilotes GPU :

1. Déployez une machine virtuelle de série NC Azure exécutant Ubuntu 16.04 LTS. Par exemple, créez la machine virtuelle dans la région Sud-Centre des États-Unis. Assurez-vous que vous créez la machine virtuelle avec un disque géré.
2. Suivez les étapes pour vous connecter à la machine virtuelle et [installer les pilotes CUDA](../virtual-machines/linux/n-series-driver-setup.md).
3. Déprovisionnez l’agent Linux, puis [capturez l’image de machine virtuelle Linux](../virtual-machines/linux/capture-image.md).
4. Créez un compte Batch dans une région qui prend en charge des machines virtuelles NC.
5. En utilisant les API Batch ou le portail Azure, créez un pool [avec l’image personnalisée](batch-custom-images.md), et le nombre de nœuds et l’échelle souhaités. Le tableau suivant contient des exemples de paramètres de pool pour l’image :

| Paramètre | Valeur |
| ---- | ---- |
| **Type d’image** | Image personnalisée |
| **Image personnalisée** | Nom de l’image |
| **Référence de l’agent de nœud** | batch.node.ubuntu 16.04 |
| **Taille du nœud** | NC6 Standard |



## <a name="next-steps"></a>étapes suivantes

* Pour exécuter des travaux MPI sur un pool Azure Batch, consultez les exemples [Windows](batch-mpi.md) ou [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Pour obtenir des exemples de charges de travail GPU sur Batch, consultez les recettes [Batch Shipyard](https://github.com/Azure/batch-shipyard/).