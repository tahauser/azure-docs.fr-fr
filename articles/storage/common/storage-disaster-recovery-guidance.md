---
title: Que faire en cas de panne d’Azure Storage | Microsoft Docs
description: Que faire en cas de panne d’Azure Storage
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 3c313025917bba06675d3b2d844a6740fab89fbc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Que faire en cas de panne d’Azure Storage
Microsoft s’engage à déployer tous les efforts nécessaires pour garantir en permanence la disponibilité de ses services. Il arrive parfois que des phénomènes incontrôlables entraînent des interruptions de service non planifiés dans une ou plusieurs régions. Pour vous aider à faire face à ces rares occurrences, vous trouverez ici quelques conseils généraux pour les services Azure Storage.

## <a name="how-to-prepare"></a>Préparation
Il est essentiel que chaque client veille à élaborer son propre plan de récupération d’urgence. L’effort de récupération suite à une panne de stockage implique généralement l’intervention du personnel opérationnel ainsi que l’application de procédures automatisées afin de rétablir le bon fonctionnement de vos applications. Reportez-vous à la documentation Azure ci-dessous pour créer votre propre plan de récupération d’urgence :

* [Liste de contrôle de disponibilité](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Conception d’applications résilientes pour Azure](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Service Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)
* [Réplication Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Service Azure Backup](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Mode de détection
Pour déterminer l’état du service Azure, il est recommandé de s’abonner au [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Que faire en cas de panne d’Azure Storage
Si un ou plusieurs services Azure Storage sont provisoirement indisponibles dans une ou plusieurs régions, deux options s’offrent à vous. Si vous souhaitez un accès immédiat à vos données, envisagez l’Option 2.

### <a name="option-1-wait-for-recovery"></a>Option 1: attente de récupération
Dans ce cas, aucune action n’est requise de votre part. Nous travaillons assidûment à la restauration de la disponibilité du service Azure. Vous pouvez analyser l’état actuel du service dans le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Option 2: copie de données à partir de la base de données secondaire
Si vous avez choisi le [stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recommandé) pour vos comptes de stockage, vous bénéficierez d’un accès en lecture à vos données à partir de la région secondaire. Vous pouvez utiliser des outils tels que [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) et la [bibliothèque de déplacement des données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) pour copier des données de la région secondaire dans un autre compte de stockage situé dans une région non compromise, puis pointer vos applications vers ce compte de stockage pour bénéficier d’un accès en lecture et en écriture.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Que se passe-t-il en cas de basculement d’Azure Storage ?
Si vous avez choisi le [stockage géo-redondant (GRS)](storage-redundancy-grs.md) ou le [stockage géo-redondant avec accès en lecture (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (recommandé), Azure Storage conservera vos données dans deux régions (primaire et secondaire). Azure Storage conserve constamment plusieurs réplicas de vos données dans les deux régions.

Lorsqu’un sinistre régional affecte votre région primaire, nous allons tout d’abord tenter de restaurer le service dans cette région. Selon de la nature de l’incident et son impact, il peut arriver dans de rares circonstances que ne soyons pas en mesure de restaurer la région primaire. À ce stade, nous procéderons à un basculement géographique. La réplication des données entre les régions est un processus asynchrone qui peut impliquer un certain délai ; il est donc possible que les modifications qui n’ont pas encore été répliquées dans la région secondaire soient perdues. Vous pouvez interroger [« l’heure de dernière synchronisation » de votre compte de stockage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) pour obtenir des détails sur l’état de la réplication.

Quelques points relatifs à l’expérience de basculement géographique du stockage :

* Le basculement géographique du stockage est déclenché uniquement par l’équipe Azure Storage et ne nécessite donc aucune intervention du client.
* Vos points de terminaison de service de stockage existant pour les objets blob, les tables, les files d’attente et les fichiers restent les mêmes après le basculement ; l’entrée DNS fournie par Microsoft devra être mise à jour pour basculer de la région primaire à la région secondaire.  Microsoft effectue cette mise à jour automatiquement dans le cadre du processus de basculement géographique.
* Avant et pendant le basculement géographique, vous n’avez pas d’accès en écriture à votre compte de stockage en raison de l’impact de l’incident, mais vous pouvez toujours lire les données à partir de la base de données secondaire si votre compte de stockage a été configuré en tant que RA-GRS.
* Une fois le basculement géographique effectué et les modifications DNS propagées, vous bénéficiez de nouveau d’un accès en lecture-écriture à votre compte de stockage. L’emplacement pointé est votre point de terminaison secondaire précédent. 
* Notez que vous bénéficierez d’un accès en écriture si un stockage GRS ou RA-GRS est configuré pour le compte de stockage. 
* Vous pouvez interroger [« l’heure du dernier basculement géographique » de votre compte de stockage](https://msdn.microsoft.com/library/azure/ee460802.aspx) pour obtenir plus de détails.
* Après le basculement, votre compte de stockage sera entièrement opérationnel, mais dans un état « dégradé », car il sera en réalité hébergé dans une région autonome sans aucune géoréplication possible. Pour atténuer ce risque, nous restaurerons la région primaire d’origine et effectuerons une géo-restauration afin de restaurer l’état d’origine. Si la région primaire d’origine est irrécupérable, nous allouerons une autre région secondaire.
  Pour plus d’informations sur l’infrastructure de géo-réplication d’Azure Storage, consultez l’article sur le blog de l’équipe Azure Storage relatif aux [options de redondance et à RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Meilleures pratiques pour la protection de vos données
Il existe des approches recommandées pour sauvegarder régulièrement vos données de stockage.

* Disques de machine virtuelle : utilisez le [service Azure Backup](https://azure.microsoft.com/services/backup/) pour sauvegarder les disques de machine virtuelle utilisées par vos machines virtuelles Azure.
* Objets blob de blocs : créez un [instantané](https://msdn.microsoft.com/library/azure/hh488361.aspx) de chaque objet blob de blocs ou copiez les objets blob vers un autre compte de stockage dans une autre région à l’aide d’[AzCopy](storage-use-azcopy.md), d’[Azure PowerShell](storage-powershell-guide-full.md) ou de la [bibliothèque de déplacement de données Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Tables: utilisez [AzCopy](storage-use-azcopy.md) pour exporter les données de table vers un autre compte de stockage dans une autre région.
* Fichiers : utilisez [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) pour copier vos fichiers vers un autre compte de stockage dans une autre région.

Pour plus d’informations sur la création d’applications tirant pleinement parti de la fonctionnalité RA-GRS, consultez [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../storage-designing-ha-apps-with-ragrs.md).

