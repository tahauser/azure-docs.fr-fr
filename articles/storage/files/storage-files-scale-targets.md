---
title: "Objectifs de performance et d’extensibilité d'Azure Files | Microsoft Docs"
description: "Obtenez plus d’informations sur les objectifs d’extensibilité et de performances pour Azure Files, y compris la capacité, le taux de demandes et les limites de bande passante entrante et sortante."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité d'Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers entièrement gérés dans le cloud, accessibles via le protocole SMB standard. Cet article présente les objectifs de performance et d’extensibilité pour Azure Files et Azure File Sync (préversion).

Les objectifs de performance et d’extensibilité répertoriés ici sont des objectifs haut de gamme, mais ils peuvent être affectés par d'autres variables de votre déploiement. Par exemple, le débit d’un fichier peut également être limité par votre bande passante réseau disponible, et pas seulement par les serveurs qui hébergent le service Azure Files. Nous vous recommandons vivement de tester votre modèle d’utilisation pour déterminer si l’extensibilité et les performances d'Azure Files répondent à vos besoins. Nous sommes résolus à augmenter ces limites au fil du temps. N’hésitez pas à nous faire part de vos remarques, soit dans les commentaires ci-dessous ou sur [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), concernant les limites que vous aimeriez voir augmenter.

## <a name="azure-storage-account-scale-targets"></a>Objectifs de mise à l'échelle d'un compte de stockage Azure
La ressource parente d’un partage de fichiers Azure est un compte de stockage Azure. Un compte de stockage représente un pool de stockage dans Azure, qui peut être utilisé par plusieurs services de stockage, notamment Azure Files, pour stocker les données. Les autres services qui stockent les données dans des comptes de stockage Azure Blob, Azure Queue et Azure Table. Les objectifs suivants s’appliquent à tous les services de stockage qui stockent les données dans un compte de stockage :

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> L’utilisation d'un compte de stockage à partir d’autres services de stockage affecte les partages de fichiers Azure dans votre compte de stockage. Par exemple, si vous atteignez la capacité de compte de stockage maximale avec un stockage Azure Blob, vous ne pourrez plus créer des fichiers sur votre partage de fichiers Azure même si sa taille est inférieure à la taille maximale de partage.

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle Azure File Sync
Avec Azure File Sync, nous nous sommes efforcés de proposer un usage illimité, mais cela n’est pas toujours possible. Le tableau ci-dessous indique les limites de nos tests et les limites matérielles de nos objectifs :

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Voir aussi
- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Objectifs d'extensibilité et de performance pour d'autres services de stockage](../common/storage-scalability-targets.md)