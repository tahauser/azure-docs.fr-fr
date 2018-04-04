---
title: storage-files-create-storage-account-portal
description: Création d'un compte de stockage pour Azure Files
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
Un compte de stockage est un pool partagé de stockage dans lequel vous pouvez déployer un partage de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

Pour créer un compte de stockage :

1. Dans le menu de gauche, cliquez sur **+** pour créer une ressource.
2. Tapez **compte de stockage** dans la zone de recherche (1) et sélectionnez **Compte de stockage - blob, fichier, table, file d’attente** (2), puis cliquez sur **Créer**.
    ![Capture d’écran de ce à quoi doit ressembler l’entrée Compte de stockage dans la zone de recherche des ressources](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Dans **Nom**, tapez *mystorageacct* suivi de quelques nombres au hasard jusqu’à obtenir une marque verte, signe que le nom est unique. Un nom du compte de stockage doit être écrit entièrement en minuscules et être unique. Notez le nom de votre compte de stockage. Il vous servira plus loin dans ce didacticiel. 
4. Dans **Modèle de déploiement**, laissez la valeur par défaut de **Resource manager**. Pour en savoir plus sur les différences entre les déploiements Azure Resource Manager et classique, consultez [Comprendre les modèles de déploiement et l’état de vos ressources](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. Dans **Type de compte**, sélectionnez **StorageV2**. Pour en savoir sur les différents types de comptes de stockage, consultez [Comprendre les comptes de stockage Azure](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. Dans **Performances**, laissez la valeur par défaut de **Stockage standard**. Azure Files prend actuellement en charge le stockage standard. Même si vous sélectionnez le stockage premium, votre partage de fichiers est stocké dans un stockage standard.
7. Dans **Réplication**, sélectionnez *Stockage localement redondant (LRS)*. 
8. Dans **Transfert sécurisé requis**, nous vous recommandons de toujours sélectionner **Activé**. Pour en savoir plus sur cette option, consultez [Comprendre le chiffrement en transit](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. Dans **Abonnement**, sélectionnez l’abonnement dans lequel créer le compte de stockage. Si vous n’avez qu’un abonnement, il est proposé par défaut.
10. Dans **Groupe de ressources**, sélectionnez **Créer** et entrez *myResourceGroup* comme nom.
11. Dans **Emplacement**, sélectionnez **Est des États-Unis**.
12. Dans **Réseaux virtuels**, laissez l’option par défaut *Désactivé*. 
13. Sélectionnez **Épingler au tableau de bord** pour que le compte de stockage soit plus facile à trouver.
14. Lorsque vous avez terminé, cliquez sur **Créer** pour commencer le déploiement.