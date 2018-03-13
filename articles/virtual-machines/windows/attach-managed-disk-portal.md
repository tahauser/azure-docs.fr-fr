---
title: "Attacher un disque de données géré à une machine virtuelle Windows - Azure | Microsoft Docs"
description: "Découvrez comment attacher un nouveau disque de données géré à une machine virtuelle Windows dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 603d1c423ff2039915bdd3d5ed4a79b78d491edc
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Attachement d’un disque de données géré à une machine virtuelle Windows dans le portail Azure

Cet article explique comment attacher un nouveau disque de données géré à des machines virtuelles Windows dans le portail Azure. Avant cela, passez en revue les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](sizes.md).
* Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.

Vous pouvez également [attacher un disque de données à l’aide de PowerShell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Ajouter un disque de données
1. Dans le menu de gauche, cliquez sur **Machines virtuelles**.
2. Sélectionnez la machine virtuelle dans la liste.
3. Sur la page de la machine virtuelle, cliquez sur **Disques**.
4. Sur la page **Disques**, cliquez sur **+ Ajouter un disque de données**.
5. Dans la liste déroulante du nouveau disque, sélectionnez **Créer un disque**.
6. Sur la page **Créer un disque managé**, tapez le nom du disque et ajustez les autres paramètres si nécessaire. Lorsque vous avez terminé, cliquez sur **Créer**.
7. Sur la page **Disques**, cliquez sur **Enregistrer** pour enregistrer la nouvelle configuration de disque de la machine virtuelle.
6. Une fois qu’Azure l’a créé et attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle, sous **Disques de données**.


## <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

1. Connectez-vous à la machine virtuelle.
1. Cliquez sur le menu Démarrer dans la machine virtuelle, tapez sur **diskmgmt.msc** et appuyez sur **Entrée**. Le composant logiciel enfichable Gestion des disques s’ouvre.
2. Le composant Gestion des disques identifie que votre nouveau disque n’est pas initialisé et affiche la fenêtre **Initialiser le disque**.
3. Vérifiez que le nouveau disque est sélectionné, puis cliquez sur **OK** pour l’initialiser.
4. Le nouveau disque apparaît comme **non alloué**. Cliquez avec le bouton droit n’importe où sur le disque, puis sélectionnez **Nouveau volume simple**. **L’Assistant Nouveau volume simple** s’ouvre.
5. Exécutez l’Assistant en conservant tous les paramètres par défaut. Lorsque vous avez terminé, sélectionnez **Terminer**.
6. Fermez Gestion des disques.
7. Une fenêtre contextuelle s’affiche et vous permet de formater le nouveau disque pour que vous puissiez l’utiliser. Cliquez sur **Formater le disque**.
8. Dans la boîte de dialogue **Formater un nouveau disque**, vérifiez les paramètres puis cliquez sur **Démarrer**.
9. Un message vous informe que le formatage des disques va effacer toutes les données. Cliquez sur **OK**.
10. Lorsque le formatage est terminé, cliquez sur **OK**.

## <a name="use-trim-with-standard-storage"></a>Utilisation de TRIM avec le stockage standard

Si vous utilisez le stockage standard (disque dur), vous devez activer la fonction TRIM. TRIM ignore les blocs inutilisés sur le disque afin que vous soyez facturé uniquement pour le stockage que vous utilisez réellement. Vous pouvez ainsi faire des économies si vous créez des fichiers volumineux, puis les supprimez. 

Vous pouvez exécuter cette commande pour vérifier le paramètre TRIM. Ouvrez une invite de commandes sur votre machine virtuelle Windows et saisissez :

```
fsutil behavior query DisableDeleteNotify
```

Si la commande renvoie 0, TRIM est bien activé. Si la valeur 1 est renvoyée, exécutez la commande suivante pour activer TRIM :
```
fsutil behavior set DisableDeleteNotify 0
```

Après la suppression de données de votre disque, vous pouvez vous assurer du bon vidage des opérations TRIM en exécutant la défragmentation avec TRIM :

```
defrag.exe <volume:> -l
```

Pour vous assurer que les blocs de données inutilisés sont bien effacés sur tout le volume, vous pouvez également le formater.

## <a name="next-steps"></a>Étapes suivantes
Si votre application doit utiliser le lecteur D: pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
