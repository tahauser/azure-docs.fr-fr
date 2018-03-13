---
title: "Créer une machine virtuelle sur le Portail Azure | Microsoft Docs"
description: "Création d'une machine virtuelle Windows dans le portail Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 99a67821ab926983205e2327c428e854d20a0cf5
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Créer une machine virtuelle exécutant Windows dans le portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](tutorial.md)
> * [PowerShell : Déploiement classique](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [suivre ces étapes à l’aide du modèle de déploiement Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à l’aide du **Portail Azure**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ce didacticiel vous montre comment créer une machine virtuelle Azure exécutant Windows sur le Portail Azure. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau Windows peuvent être accessibles aux abonnés MSDN.

Cette section vous montre comment utiliser le **Tableau de bord** sur le Portail Azure pour sélectionner, puis créer la machine virtuelle.

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](createupload-vhd.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez [les différentes façons de créer une machine virtuelle Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"></a>Créer la machine virtuelle
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer une machine virtuelle à l’aide du modèle de déploiement Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sur le Portail Azure.
* Connectez-vous à votre machine virtuelle. Pour obtenir des instructions, consultez [Connexion à une machine virtuelle exécutant Windows Server](connect-logon.md).
* Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez la page [Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement Classic](attach-disk.md).
