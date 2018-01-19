---
title: "Créer une machine virtuelle Windows à partir d’un disque dur virtuel spécialisé dans le portail Azure | Microsoft Docs"
description: "Créez une machine virtuelle Windows à partir d’un disque dur virtuel dans le portail Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Créer une machine virtuelle à partir d’un disque dur virtuel dans le portail Azure


Il existe plusieurs façons de créer une machine virtuelle dans Azure. Si vous souhaitez réutiliser un disque dur virtuel existant ou copier le disque dur virtuel à utiliser à partir d’une machine virtuelle existante, vous pouvez créer une machine virtuelle en attachant le disque dur virtuel en tant que disque du système d’exploitation. Ce processus *attache* le disque dur virtuel à une nouvelle machine virtuelle en tant que disque du système d’exploitation.

Vous pouvez également créer une machine virtuelle à partir du disque dur virtuel d’une machine virtuelle qui a été supprimée. Par exemple, si vous avez une machine virtuelle Azure qui ne fonctionne pas correctement, vous pouvez la supprimer et créer une autre machine virtuelle à partir du disque dur virtuel. Vous pouvez réutiliser le même disque dur virtuel, ou créer une copie du disque dur virtuel en créant un instantané, puis en créant un disque managé à partir de l’instantané. Cette méthode nécessite quelques étapes supplémentaires, mais elle vous permet de conserver le disque dur virtuel d’origine et fournit également un instantané de secours au besoin.

Vous avez une machine virtuelle locale que vous souhaitez utiliser pour créer une machine virtuelle dans Azure. Vous pouvez charger le disque dur virtuel et l’attacher à une nouvelle machine virtuelle. Pour charger un disque dur virtuel, vous devez utiliser PowerShell ou un autre outil pour pouvoir le charger dans un compte de stockage, puis créer un disque managé à partir du disque dur virtuel. Pour plus d’informations, consultez [Charger un disque dur virtuel spécialisé](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Si vous souhaitez utiliser une machine virtuelle ou un disque dur virtuel pour créer plusieurs machines virtuelles, ne choisissez pas cette méthode. Pour les déploiements plus importants, vous devez [créer une image](capture-image-resource.md), puis [utiliser cette image pour créer plusieurs machines virtuelles](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copier un disque

Créez un instantané, puis créez un disque à partir de cet instantané. Vous pouvez ainsi conserver le disque dur virtuel d’origine comme disque de secours.

1. Dans le menu de gauche, cliquez sur **Toutes les ressources**.
2. Dans la liste déroulante **Tous les types**, désélectionnez l’option **Tout sélectionner**, puis faites défiler la liste vers le bas et sélectionnez **Disques** pour rechercher les disques disponibles.
3. Cliquez sur le disque que vous souhaitez utiliser. La page **Vue d’ensemble** du disque s’ouvre.
4. Dans la page Vue d’ensemble, dans le menu en haut, cliquez sur **+ Créer un instantané**. 
5. Tapez le nom du nouvel instantané.
6. Choisissez un **groupe de ressources** pour l’instantané. Vous pouvez utiliser un groupe de ressources existant ou en créer un.
7. Choisissez le stockage à utiliser : standard (HDD) ou Premium (SDD).
8. Quand vous avez terminé, cliquez sur **Créer** pour créer l’instantané.
9. Une fois que vous avez créé l’instantané, cliquez sur **+ Créer une ressource** dans le menu de gauche.
10. Dans la barre de recherche, tapez **disque managé** et sélectionnez **Disques managés** dans la liste.
11. Dans la page **Disques managés**, cliquez sur **Créer**.
12. Tapez le nom du disque.
13. Choisissez un **groupe de ressources** pour le disque. Vous pouvez utiliser un groupe de ressources existant ou en créer un. Ce groupe de ressources est également celui où vous créez la machine virtuelle à partir du disque.
14. Choisissez le stockage à utiliser : standard (HDD) ou Premium (SDD).
15. Dans **Type de source**, assurez-vous que l’option **Instantané** est sélectionnée.
16. Dans la liste déroulante **Capture instantanée source**, sélectionnez l’instantané que vous souhaitez utiliser.
17. Effectuez tout autre changement nécessaire, puis cliquez sur **Créer** pour créer le disque.

## <a name="create-a-vm-from-a-disk"></a>Créer une machine virtuelle à partir d’un disque

Une fois que vous avez le disque dur virtuel du disque managé que vous souhaitez utiliser, vous pouvez créer la machine virtuelle dans le portail.

1. Dans le menu de gauche, cliquez sur **Toutes les ressources**.
2. Dans la liste déroulante **Tous les types**, désélectionnez l’option **Tout sélectionner**, puis faites défiler la liste vers le bas et sélectionnez **Disques** pour rechercher les disques disponibles.
3. Cliquez sur le disque que vous souhaitez utiliser. La page **Vue d’ensemble** du disque s’ouvre.
Dans la page Vue d’ensemble, assurez-vous que **État du disque** affiche **Non attaché**. Si ce n’est pas le cas, essayez de détacher le disque de la machine virtuelle ou de supprimer la machine virtuelle pour libérer le disque.
4. Dans le menu en haut du volet, cliquez sur **+ Créer une machine virtuelle**.
5. Dans la page **De base** pour la nouvelle machine virtuelle, tapez un nom, puis sélectionnez un groupe de ressources existant ou créez-en un.
6. Dans la page **Taille**, sélectionnez une taille de machine virtuelle, puis cliquez sur **Sélectionner**.
7. Dans la page **Paramètres**, vous pouvez laisser le portail créer automatiquement toutes les nouvelles ressources, ou sélectionner vous-même un **réseau virtuel** et un **groupe de sécurité réseau** existants. Le portail crée toujours une carte réseau et une adresse IP publique pour la nouvelle machine virtuelle. 
8. Effectuez les changements voulus pour les options de surveillance et ajoutez les extensions dont vous avez besoin.
9. Une fois ces opérations effectuées, cliquez sur **OK**. 
10. Si la configuration de la machine virtuelle est validée, cliquez sur **OK** pour démarrer le déploiement.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également utiliser PowerShell pour [charger un disque dur virtuel dans Azure et créer une machine virtuelle spécialisée](create-vm-specialized.md).


