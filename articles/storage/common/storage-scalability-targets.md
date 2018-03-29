---
title: Objectifs de performance et d’extensibilité Azure Storage | Microsoft Docs
description: Obtenez plus d’informations sur les objectifs d’extensibilité et de performances pour Azure Storage, y compris la capacité, le taux de demande et la bande passante entrante et sortante pour les comptes de stockage standard et premium. Découvrez les objectifs de performances des partitions dans chacun des services Azure Storage.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: e393bb9e7615b893699caf5a931ede5803046892
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Objectifs de performance et évolutivité d'Azure Storage
## <a name="overview"></a>Vue d'ensemble
Cet article décrit les rubriques liées à la scalabilité et aux performances de Stockage Azure. Pour prendre connaissance des autres informations relatives aux limites Azure, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](../../azure-subscription-service-limits.md).

> [!NOTE]
> Tous les comptes de stockage s’exécutent sur la nouvelle topologie de réseau plat et prennent en charge les objectifs de scalabilité et de performances décrits dans cet article, quel que soit le moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Azure Storage et sur son extensibilité, consultez le billet de blog [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application. Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.
> 
> Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Stockage Azure commence à renvoyer des codes d’erreur 503 (Serveur occupé) ou 500 (Délai d’expiration de l’opération). Si ces erreurs se produisent, alors votre application doit utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.
> 
> 

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification des licences en volume, consultez la page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="scalability-targets-for-a-storage-account"></a>Objectifs d’évolutivité de compte de stockage
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Blob Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files
Pour plus d’informations sur les objectifs de scalabilité et de performances des fichiers Azure et d’Azure File Sync, consultez [Objectifs de scalabilité et de performances des fichiers Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle d’Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage File d’attente Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Objectifs de mise à l’échelle du stockage Table Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Voir aussi
* [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md)
* [Réplication Azure Storage](../storage-redundancy.md)
* [Liste de contrôle des performances et de l’évolutivité de Microsoft Azure Storage](../storage-performance-checklist.md)
* [Microsoft Azure Storage : service de stockage sur le cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

