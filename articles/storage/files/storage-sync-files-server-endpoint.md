---
title: "Ajouter/supprimer un point de terminaison de serveur Azure File Sync (préversion) | Microsoft Docs"
description: "Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 1619b3c67fb68f05c4af999a38794e4a52c22264
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Ajouter/supprimer un point de terminaison de serveur Azure File Sync (préversion)
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Un *point de terminaison de serveur* représente un emplacement spécifique sur un *serveur inscrit*, comme un dossier sur un volume de serveur ou la racine du volume. Plusieurs points de terminaison de serveur peuvent se trouver sur le même volume si leurs espaces de noms ne se chevauchent pas (par exemple, F:\sync1 et F:\sync2). Vous pouvez configurer des stratégies de hiérarchisation cloud individuellement pour chaque point de terminaison de serveur. Si vous ajoutez un emplacement de serveur avec un ensemble de fichiers existants comme point de terminaison de serveur à un groupe de synchronisation, ces fichiers sont fusionnés avec tout autre fichier déjà présent sur les autres points de terminaison dans le groupe de synchronisation.

Consultez [Guide pratique pour déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md) pour plus d’informations le déploiement de bout en bout d’Azure File Sync.

## <a name="prerequisites"></a>Conditions préalables
Pour créer un point de terminaison de serveur, vous devez d’abord vérifier que les critères suivants sont remplis : 
- L’agent de synchronisation de fichiers Azure est installé le serveur et ce dernier a été inscrit. Vous trouverez des instructions pour l’installation de l’agent de synchronisation de fichiers Azure dans l’article [Inscrire/désinscrire un serveur de la synchronisation de fichiers Azure (préversion)](storage-sync-files-server-registration.md). 
- Vérifiez qu’un service de synchronisation de stockage a été déployé. Consultez [Guide pratique pour déployer la synchronisation de fichiers Azure (préversion)](storage-sync-files-deployment-guide.md) pour plus d’informations sur le déploiement d’un service de synchronisation de stockage. 
- Vérifiez qu’un groupe de synchronisation a été déployé. Découvrez comment [Créer un groupe de synchronisation](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Vérifiez que le serveur est connecté à Internet et qu’Azure est accessible. Nous utilisons le port 443 pour toutes les communications entre le serveur et notre service.

## <a name="add-a-server-endpoint"></a>Ajouter un point de terminaison de serveur
Pour ajouter un point de terminaison de serveur, accédez au groupe de synchronisation souhaité, puis sélectionnez « Ajouter un point de terminaison de serveur ».

![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Les informations suivantes sont requises sous l’option **Ajouter un point de terminaison de serveur** :

- **Serveur inscrit** : nom du serveur ou du cluster sur lequel créer le point de terminaison de serveur.
- **Chemin d’accès** : chemin sur le serveur Windows Server à synchroniser en tant qu’élément du groupe de synchronisation.
- **Hiérarchisation cloud** : commutateur activant ou désactivant la synchronisation cloud, qui permet de hiérarchiser les fichiers rarement ouverts ou utilisés dans Azure Files.
- **Espace libre du volume** : quantité d’espace libre à réserver sur le volume sur lequel se trouve le point de terminaison de serveur. Par exemple, si l’espace libre du volume est définie sur 50 % sur un volume avec un point de terminaison de serveur unique, environ la moitié de la quantité de données sera hiérarchisée dans Azure Files. Que la hiérarchisation cloud soit activée ou non, le partage de fichiers Azure dispose toujours d’une copie complète des données dans le groupe de synchronisation.

Sélectionnez **Créer** pour ajouter le point de terminaison de serveur. Les fichiers situés dans un espace de noms d’un groupe de synchronisation seront désormais synchronisés. 

## <a name="remove-a-server-endpoint"></a>Supprimer un point de terminaison de serveur
Lorsque la hiérarchisation cloud est activée pour un point de terminaison de serveur, elle *hiérarchise* les fichiers dans des partages de fichiers Azure. Cela permet aux partages de fichiers locaux de faire office de cache, au lieu d’effectuer une copie complète du jeu de données, et d’utiliser efficacement l’espace sur le serveur de fichiers. Toutefois, si un point de terminaison de serveur est supprimé alors que des fichiers hiérarchisés sont toujours localement sur le serveur, ces fichiers deviennent inaccessibles. Par conséquent, si vous voulez un accès continu aux fichiers, vous devez rappeler tous les fichiers hiérarchisés dans Azure Files avant de poursuivre la désinscription. 

Cette opération peut être effectuée avec l’applet de commande PowerShell comme indiqué ci-dessous :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si le volume local qui héberge le serveur n’a pas assez d’espace libre pour rappeler toutes les données hiérarchisées, l’applet de commande `Invoke-StorageSyncFileRecall` échoue.  

Pour supprimer le point de terminaison de serveur

1. Accédez au service de synchronisation de stockage auquel votre serveur est inscrit.
2. Accédez au groupe de synchronisation souhaité.
3. Supprimez le point de terminaison de serveur souhaité du groupe de synchronisation dans le service de synchronisation de stockage. Pour ce faire, vous pouvez cliquer avec le bouton droit sur le point de terminaison de serveur concerné dans le volet Groupe de synchronisation.

    ![Suppression d’un point de terminaison de serveur d’un groupe de synchronisation](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Étapes suivantes
- [Inscrire/désinscrire un serveur de la synchronisation de fichiers Azure (préversion)](storage-sync-files-server-registration.md)
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
