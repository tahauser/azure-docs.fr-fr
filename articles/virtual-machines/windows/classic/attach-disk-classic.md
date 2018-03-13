---
title: "Attacher un disque à une machine virtuelle Azure Classic | Microsoft Docs"
description: "Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique et l'initialiser."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 8219ee8bbaa3d53f95e441c934a0524666593c7b
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique

Cet article vous explique comment attacher des disques nouveaux et existants, créés avec le modèle de déploiement Classic, à une machine virtuelle Windows avec le Portail Azure.



Vous pouvez également [attacher un disque de données à une machine virtuelle Linux dans le portail Azure](../../linux/attach-disk-portal.md).

Avant d’attacher un disque, lisez les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Pour utiliser le stockage Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.

Vous pouvez également [attacher un disque de données à l’aide de PowerShell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Sélectionnez la machine virtuelle dans la ressource figurant sur le tableau de bord.
3. Dans le volet gauche situé sous **Paramètres**, cliquez sur **Disques**.

    ![Ouverture des paramètres d’un disque](./media/attach-disk/virtualmachinedisks.png)

Continuez en suivant les instructions pour attacher un [nouveau disque](#option-1-attach-a-new-disk) ou un [disque existant](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Option 1 : attacher et initialiser un nouveau disque

1. Dans le panneau **Disques**, cliquez sur **Attacher un nouveau disque**.
2. Passez en revue les paramètres par défaut, mettez-les à jour en fonction des besoins, puis cliquez sur **OK**.

   ![Examen des paramètres d’un disque](./media/attach-disk/attach-new.png)

3. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

### <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, voir [Connexion à une machine virtuelle Azure exécutant Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

    ![Ouvrir le gestionnaire de serveur](../media/attach-disk-portal/fileandstorageservices.png)

3. Sélectionnez **Disques**.
4. La section **Disques** répertorie les disques. En règle générale, une machine virtuelle contient le disque 0, le disque 1 et le disque 2. Le disque 0 est le disque du système d’exploitation, le disque 1 le disque temporaire et le disque 2 le disque de données qui vient d’être attaché à la machine virtuelle. Le disque de données liste la Partition sous le nom **Inconnu**.

 Cliquez avec le bouton droit sur le disque et sélectionnez **Initialiser**.

5. Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l'avertissement et initialiser le disque. Une fois l’opération terminée, la partition sera répertoriée comme **GPT**. Cliquez de nouveau avec le bouton droit sur le disque et sélectionnez **Nouveau volume**.

6. Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l'assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.

    ![Volume correctement initialisé](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Option 2 : attacher un disque existant
1. Dans le panneau **Disques**, cliquez sur **Attacher un disque existant**.
2. Sous **Attacher un disque existant**, cliquez sur **Emplacement**.

   ![Attachement d’un disque existant](./media/attach-disk/attachexistingdisksettings.png)
3. Sous **Comptes de stockage**, sélectionnez le compte et le conteneur dans lequel figure le fichier .vhd.

   ![Recherche d’emplacement VHD](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Sélectionnez le fichier .vhd.
5. Sous **Attacher un disque existant**, le fichier que vous venez de sélectionner est répertorié sous **Fichier VHD**. Cliquez sur **OK**.
6. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## <a name="use-trim-with-standard-storage"></a>Utilisation de TRIM avec le stockage standard

Si vous utilisez le stockage standard (disque dur), vous devez activer la fonction TRIM. TRIM ignore les blocs inutilisés sur le disque afin que vous soyez facturé uniquement pour le stockage que vous utilisez réellement. La fonction TRIM peut réduire les coûts, notamment les blocs inutilisés qui résultent de la suppression de fichiers volumineux.

Vous pouvez exécuter cette commande pour vérifier le paramètre TRIM. Ouvrez une invite de commandes sur votre machine virtuelle Windows et saisissez :

```
fsutil behavior query DisableDeleteNotify
```

Si la commande renvoie 0, TRIM est bien activé. Si la valeur 1 est renvoyée, exécutez la commande suivante pour activer TRIM :
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Étapes suivantes
Si votre application doit utiliser le lecteur D: pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Ressources supplémentaires
[À propos des disques et VHD pour machines virtuelles](../../virtual-machines-linux-about-disks-vhds.md)
