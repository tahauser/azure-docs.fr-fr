---
title: "Déplacement de grandes quantités de données vers/à partir d’un stockage cloud dans Azure | Microsoft Docs"
description: "Une vue d’ensemble des différentes méthodes de déplacement des données vers et à partir du stockage Azure."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: 980e4675c2d2e88716a3133abb027988aecd538f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="moving-data-to-and-from-azure-storage"></a>Transfert de données vers et à partir d’Azure Storage
Si vous voulez déplacer des données locales vers Azure Storage (ou inversement), il existe plusieurs façons de procéder. L’approche la mieux adaptée à vos besoins dépend de votre scénario. Cet article fournit un aperçu des différents scénarios et des offres appropriées pour chacun d’eux.

## <a name="building-applications"></a>Création d’applications
Si vous créez une application, le développement avec l’API REST ou l’une de nos nombreuses bibliothèques clientes est un excellent moyen de déplacer des données vers et à partir d’Azure Storage.

Azure Storage fournit de riches bibliothèques clientes pour .NET, iOS, Java, Android, la plateforme Windows universelle, Xamarin, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes offrent des fonctionnalités avancées telles que la logique de nouvelle tentative, la journalisation et les téléchargements parallèles. Vous pouvez également développer votre application directement avec l’API REST, qui peut être appelée à l’aide de n’importe quel langage permettant de créer des requêtes HTTP/HTTPS.

Consultez [Prise en main d’Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) pour en savoir plus.

En outre, nous proposons la [bibliothèque de déplacement des données Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) , une bibliothèque conçue pour la copie de données hautes performances vers et depuis Azure. Veuillez vous reporter à la [documentation](https://github.com/Azure/azure-storage-net-data-movement) de la bibliothèque de déplacement des données pour en savoir plus. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Affichage/utilisation rapide de vos données
Si vous cherchez un moyen simple d’afficher vos données Azure Storage tout en ayant la possibilité de charger et télécharger vos données, utilisez un Explorateur de stockage Azure.

Consultez notre liste d’ [Explorateurs du stockage Azure](../storage-explorers.md) pour en savoir plus.

## <a name="system-administration"></a>Administration système
Si vous préférez ou devez utiliser un utilitaire de ligne de commande (par exemple, les administrateurs système), voici quelques options intéressantes :

### <a name="azcopy"></a>AzCopy
AzCopy est un utilitaire de ligne de commande conçu pour la copie de données hautes performances vers ou à partir d’Azure Storage. Vous pouvez également copier des données au sein d’un compte de stockage ou d’un compte de stockage à l’autre. AzCopy est disponible sur [Windows](storage-use-azcopy.md) et sur [Linux](storage-use-azcopy-linux.md).

Consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) ou [Transfert de données avec AzCopy sur Linux](storage-use-azcopy-linux.md) pour en savoir plus.

### <a name="azure-powershell"></a>Azure PowerShell
Le module Azure PowerShell fournit des applets de commande pour la gestion des services sur Azure. Il s’agit d’un interpréteur en ligne de commande basé sur les tâches et d’un langage de génération de scripts conçu spécialement pour l’administration de systèmes.

Consultez [Utilisation d’Azure PowerShell avec Azure Storage](storage-powershell-guide-full.md) pour en savoir plus.

### <a name="azure-cli"></a>Azure CLI
L’interface CLI Azure offre un ensemble de commandes multiplateforme open source pour l’utilisation des services Azure. L’interface CLI Azure est disponible sur Windows, OS X et Linux.

Consultez [Utilisation de l’interface CLI Azure avec Azure Storage](../storage-azure-cli.md) pour en savoir plus.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Déplacement de grandes quantités de données avec un réseau lent
L’un des plus grands défis du déplacement de grandes quantités de données est le temps de transfert. Si vous voulez déplacer des données vers/à partir d’Azure Storage sans vous préoccuper des coûts de réseaux ou de l’écriture du code, Azure Import/Export est une solution appropriée.

Consultez [Azure Import/Export](../storage-import-export-service.md) pour en savoir plus.

## <a name="backing-up-your-data"></a>Sauvegarde de vos données
Si vous devez simplement sauvegarder vos données dans Azure Storage, Azure Backup est la meilleure option. Il s’agit d’une solution puissante pour la sauvegarde des données locales et des machines virtuelles Azure.

Consultez [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) pour en savoir plus.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Accès à vos données localement et à partir du cloud
Si vous avez besoin d’une solution pour accéder à vos données localement et à partir du cloud, utilisez la solution hybride de stockage cloud d’Azure, StorSimple. Cette solution se compose d’un appareil physique StorSimple qui stocke intelligemment les données fréquemment utilisées sur des disques SSD, les données occasionnellement utilisées sur des disques durs et les données inactives/de sauvegarde/d’archivage sur Azure Storage.

Consultez [StorSimple](../../storsimple/storsimple-overview.md) pour en savoir plus.

## <a name="recovering-your-data"></a>Récupération de vos données
Quand vous disposez d’applications et de charges de travail locales, vous avez besoin d’une solution qui permet à votre entreprise de continuer à fonctionner en cas de sinistre. Azure Site Recovery gère la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Les données répliquées sont stockées dans Azure Storage, de sorte que vous n’avez plus besoin d’un centre de données sur site secondaire.

Consultez [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) pour en savoir plus.
### <a name="moving-data-faq"></a>FAQ sur le déplacement des données :
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Puis-je migrer des disques durs virtuels d’une région à une autre sans copie ?
La seule façon de copier des disques durs virtuels entre des régions consiste à copier les données entre les comptes de stockage dans chaque région. Pour ce faire, vous pouvez utiliser AZCopy. Pour en savoir plus, consultez Transfert de données avec l’utilitaire de ligne de commande AzCopy. Pour les très grandes quantités de données, vous pouvez également utiliser Azure Import/Export. Consultez [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) pour en savoir plus.
