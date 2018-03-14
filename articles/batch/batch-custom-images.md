---
title: "Approvisionner des pools Azure Batch à partir d’images personnalisées | Microsoft Docs"
description: "Vous pouvez créer un pool Batch à partir d’une image personnalisée pour approvisionner les nœuds qui contiennent les logiciels et les données dont vous avez besoin pour votre application. Les images personnalisées sont un moyen efficace de configurer les nœuds de calcul pour exécuter vos charges de travail Batch."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: danlep
ms.openlocfilehash: 63a567e9fdfef8dfceb275953cc0ac606355ea30
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Utiliser une image personnalisée managée pour créer un pool de machines virtuelles 

Quand vous créez un pool Azure Batch à l’aide de Configuration de la machine virtuelle, vous spécifiez une image de machine virtuelle qui fournit le système d’exploitation pour chaque nœud de calcul dans le pool. Vous pouvez créer un pool de machines virtuelles avec une image Place de Marché Azure ou avec une image personnalisée (image de machine virtuelle que vous avez créée et configurée vous-même). L’image personnalisée doit être une ressource *d’image managée* dans le même abonnement et la même région Azure que le compte Batch.

## <a name="why-use-a-custom-image"></a>Pourquoi utiliser une image personnalisée ?
Quand vous fournissez une image personnalisée, vous contrôlez la configuration du système d’exploitation et le type de système d’exploitation et de disques de données à utiliser. Votre image personnalisée peut inclure des applications et des données de référence qui deviennent disponibles sur tous les nœuds du pool Batch dès qu’ils sont approvisionnés.

L’utilisation d’une image personnalisée réduit le temps de préparation des nœuds de calcul du pool appelés à exécuter votre charge de travail Batch. Même si vous pouvez utiliser une image de la Place de Marché et installer les logiciels sur chaque nœud de calcul après l’approvisionnement, l’utilisation d’une image personnalisée peut être plus efficace.

Le recours à une image personnalisée configurée pour votre scénario peut offrir plusieurs avantages :

- **Configurer le système d’exploitation**. Vous pouvez effectuer une configuration spéciale du système d’exploitation sur le disque du système d’exploitation de l’image personnalisée. 
- **Préinstaller des applications.** Vous pouvez créer une image personnalisée avec des applications préinstallées sur le disque du système d’exploitation, ce qui est plus efficace et moins sujet aux erreurs que l’installation d’applications après l’approvisionnement des nœuds de calcul à l’aide de StartTask.
- **Réduire la durée nécessaire au redémarrage des machines virtuelles.** L’installation d’application nécessite généralement un redémarrage de la machine virtuelle, ce qui prend du temps. Vous pouvez réduire la durée de redémarrage en préinstallant les applications. 
- **Copier de très grandes quantités de données une seule fois.** Vous pouvez intégrer les données statiques à l’image personnalisée managée en les copiant sur les disques de données d’une image managée. Cette opération ne doit être effectuée qu’une seule fois et rend les données accessibles à chaque nœud du pool.
- **Choix des types de disques.** Vous pouvez créer une image personnalisée managée à partir d’un disque dur virtuel, d’un disque managé d’une machine virtuelle Azure, d’une capture instantanée de ces disques ou de votre propre installation de Windows ou Linux que vous avez configurée. Vous pouvez utiliser du stockage premium pour le disque du système d’exploitation et le disque de données.
- **Croissance des pools.** Quand vous utilisez une image personnalisée managée pour créer un pool, celui-ci peut atteindre n’importe quelle taille demandée. Vous n’avez pas besoin d’effectuer des copies des disques durs virtuels d’objets blob d’images pour satisfaire le nombre de machines virtuelles. 


## <a name="prerequisites"></a>Prérequis

- **Une ressource d’image managée**. Pour créer un pool de machines virtuelles à l’aide d’une image personnalisée, vous devez créer une ressource d’image managée dans le même abonnement et la même région Azure que le compte Batch. Pour connaître les options de préparation d’image managée, consultez la section suivante.
- **Authentification Azure Active Directory (AAD)**. L’API du client Batch doit utiliser l’authentification AAD. La prise en charge d’Azure Batch pour AAD est documentée dans [Authentifier les solutions de service Batch avec Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Préparer une image personnalisée
Vous pouvez préparer une image managée à partir d’un disque dur virtuel, à partir d’une machine virtuelle Azure avec des disques managés, ou à partir d’une capture instantanée de machine virtuelle. 

Lors de la préparation de votre image, gardez à l’esprit les points suivants :

* Vérifiez que l’image de base du système d’exploitation utilisée pour approvisionner les pools Batch ne comporte pas d’extensions Azure préinstallées, telle que l’extension Script personnalisé. Si l’image contient une extension préinstallée, Azure peut rencontrer des problèmes de déploiement de la machine virtuelle.
* Vérifiez que l’image du système d’exploitation de base que vous fournissez utilise le lecteur temporaire par défaut. L’agent de nœud Batch s’attend actuellement à ce que le lecteur temporaire par défaut soit utilisé.
* La ressource d’image managée référencée par un pool Batch ne peut pas être supprimée pendant toute la durée de vie du pool. Si la ressource d’image managée est supprimée, la taille du pool ne peut plus augmenter. 

### <a name="to-create-a-managed-image"></a>Pour créer une image managée
Vous pouvez utiliser n’importe quel disque de système d’exploitation Windows ou Linux existant préparé pour créer une image managée. Par exemple, si vous voulez utiliser une image locale, chargez le disque local sur un compte Stockage Azure qui relève du même abonnement et de la même région que votre compte Batch en utilisant AzCopy ou un autre outil de chargement. Pour obtenir des instructions détaillées sur le chargement d’un disque dur virtuel et la création d’une image managée, consultez les conseils pour les machines virtuelles [Windows](../virtual-machines/windows/upload-generalized-managed.md) ou [Linux](../virtual-machines/linux/upload-vhd.md).

Vous pouvez également préparer une image managée à partir d’une machine virtuelle Azure nouvelle ou existante, ou à partir d’une capture instantanée de machine virtuelle. 

* Si vous créez une machine virtuelle, vous pouvez utiliser une image de la Place de Marché Azure comme image de base pour votre image managée et la personnaliser. 

* Si vous prévoyez de capturer l’image à l’aide du portail, assurez-vous que la machine virtuelle est créée avec un disque managé. Il s’agit du paramètre de stockage par défaut quand vous créez une machine virtuelle.

* Une fois que la machine virtuelle s’exécute, connectez-la via le protocole RDP (pour Windows) ou SSH (pour Linux). Le cas échéant, installez les logiciels nécessaires ou copiez les données souhaitées, puis généralisez la machine virtuelle.  

Pour connaître les étapes nécessaires pour généraliser une machine virtuelle Azure et créer une image managée, consultez les conseils pour les machines virtuelles [Windows](../virtual-machines/windows/capture-image-resource.md) ou [Linux](../virtual-machines/linux/capture-image.md).

En fonction de la façon dont vous prévoyez de créer un pool Batch avec l’image, vous avez besoin de l’identificateur suivant pour l’image :

* Si vous envisagez de créer un pool avec l’image à l’aide des API Batch, vous avez besoin de **l’ID de ressource** de l’image, qui est au format `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Si vous envisagez d’utiliser le portail, vous avez besoin du **nom** de l’image. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Créer un pool à partir d’une image personnalisée dans le portail

Une fois que vous avez enregistré votre image personnalisée et que vous connaissez son nom ou son ID de ressource, vous pouvez créer un pool Batch à partir de cette image. Les étapes suivantes montrent comment créer un pool à partir du portail Azure.

> [!NOTE]
> Si vous créez le pool à l’aide des API Batch, vérifiez que l’identité que vous utilisez pour l’authentification AAD dispose des autorisations d’accès à la ressource d’image. Consultez [Authentifier des solutions de service Batch avec Active Directory](batch-aad-auth.md).
>

1. Accédez à votre compte  Batch dans le portail Azure. Ce compte doit relever du même abonnement et de la même région que le groupe de ressources contenant l’image personnalisée. 
2. Dans la fenêtre **Paramètres** située à gauche, sélectionnez l’élément de menu **Pools**.
3. Dans la fenêtre **Pools**, sélectionnez la commande **Ajouter**.
4. Dans la fenêtre **Ajouter un pool**, sélectionnez **Image personnalisée (Linux/Windows)** dans la liste déroulante **Type d’image**. Dans la liste déroulante **Image de machine virtuelle personnalisée**, sélectionnez le nom de l’image (forme abrégée de l’ID de ressource).
5. Sélectionnez le **Serveur de publication/Offre/Référence (SKU)** correspondant à votre image personnalisée.
6. Spécifiez les autres paramètres obligatoires, notamment **Taille de nœud**, **Nœuds dédiés cibles** et **Nœuds basse priorité** et éventuellement les paramètres facultatifs.

    Par exemple, pour une image personnalisée Microsoft Windows Server Datacenter 2016, la fenêtre **Ajouter un pool** s’affiche comme suit :

    ![Ajouter un pool à partir d’une image Windows personnalisée](media/batch-custom-images/add-pool-custom-image.png)
  
Pour vérifier si un pool existant est basé sur une image personnalisée, consultez la propriété **Système d’exploitation** dans la section récapitulative des ressources de la fenêtre **Pool**. Si le pool a été créé à partir d’une image personnalisée, sa valeur est **Image de machine virtuelle personnalisée**.

Toutes les images personnalisées associées à un pool sont affichées dans la fenêtre **Propriétés** de ce dernier.
 
## <a name="next-steps"></a>étapes suivantes

- Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
