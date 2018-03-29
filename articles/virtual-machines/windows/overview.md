---
title: Vue d’ensemble des machines virtuelles Windows | Microsoft Docs
description: Apprenez à créer et à gérer des machines virtuelles Windows dans Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6ab964760ea06c2e82d0c9f69f89fe62dec8ceac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Vue d’ensemble des machines virtuelles Windows dans Azure

Les Machines Virtuelles Azure sont l’un des nombreux types de [ressources informatiques évolutives et à la demande](../../app-service/choose-web-site-cloud-service-vm.md) proposés par Azure. En règle générale, une machine virtuelle est mieux adaptée à vos besoins si vous devez surtout améliorer le contrôle de votre environnement informatique. Cet article vous informe sur les points à prendre en compte avant de créer une machine virtuelle, sur sa création et sur sa gestion.

Une machine virtuelle Azure vous donne la flexibilité de la virtualisation sans que vous ayez à acheter le matériel physique qui exécute la machine virtuelle ni à en assurer la maintenance. Toutefois, vous devez toujours assurer la maintenance de la machine virtuelle en effectuant des tâches comme la configuration, la mise à jour corrective et l’installation des logiciels qui s’exécutent dessus.

Les Machines Virtuelles Azure peuvent être utilisées de différentes manières. Voici quelques exemples :

* **Développement et test** : les machines virtuelles Azure permettent de créer rapidement et facilement un ordinateur avec des configurations spécifiques requises pour encoder et tester une application.
* **Applications dans le cloud** : la demande de votre application étant susceptible de fluctuer, il peut être économique de l’exécuter sur une machine virtuelle dans Azure. Vous payez pour des machines virtuelles supplémentaires lorsque vous en avez besoin et vous les arrêtez le reste du temps.
* **Centre de données étendu** : les machines virtuelles au sein d’un réseau virtuel Azure peuvent être facilement connectées au réseau de votre organisation.

Le nombre de machines virtuelles utilisées par votre application peut varier (montée en puissance et augmentation de la charge) pour répondre à vos besoins.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>À quoi dois-je penser avant de créer une machine virtuelle ?
Il existe toujours une multitude de [considérations liées à la conception](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) lorsque vous générez une infrastructure d’application dans Azure. Il est important de réfléchir à ces aspects des machines virtuelles avant de commencer :

* le nom de vos ressources d’application,
* l’emplacement de stockage des ressources,
* la taille de la machine virtuelle,
* le nombre maximal de machines virtuelles qui peuvent être créées,
* le système d’exploitation de la machine virtuelle,
* la configuration de la machine virtuelle après son démarrage
* et les ressources liées dont a besoin la machine virtuelle.

### <a name="naming"></a>Dénomination
Une machine virtuelle se voit affecter un [nom](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et possède un nom d’ordinateur configuré au sein du système d’exploitation. Le nom d’une machine virtuelle peut comprendre jusqu’à 15 caractères.

Si vous utilisez Azure pour créer le disque du système d’exploitation, le nom de l’ordinateur et celui de la machine virtuelle sont identiques. Si vous [chargez et utilisez votre propre image](upload-generalized-managed.md) qui contient un système d’exploitation précédemment configuré et que vous l’utilisez pour créer une machine virtuelle, les noms peuvent être différents. Nous vous recommandons de faire en sorte que le nom d’ordinateur dans le système d’exploitation et celui de la machine virtuelle soient identiques lorsque vous chargez votre propre fichier d’image.

### <a name="locations"></a>Emplacements
Toutes les ressources créées dans Azure sont réparties sur plusieurs [régions géographiques](https://azure.microsoft.com/regions/) dans le monde. En règle générale, la région est appelée **emplacement** lorsque vous créez une machine virtuelle. Pour une machine virtuelle, l’emplacement spécifie l’endroit où les disques durs virtuels sont stockés.

Ce tableau présente quelques moyens d’obtenir la liste des emplacements disponibles.

| Méthode | Description |
| --- | --- |
| Portail Azure |Sélectionnez un emplacement dans la liste lorsque vous créez une machine virtuelle. |
| Azure PowerShell |Utilisez la commande [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation). |
| de l’API REST |Utilisez l’opération [Lister les emplacements](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations). |
| Azure CLI |Utilisez l’opération [az account list-locations](https://docs.microsoft.com/en-us/cli/azure/account?view=azure-cli-latest#az_account_list_locations). |

### <a name="vm-size"></a>Taille de la machine virtuelle
La [taille](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de la machine virtuelle que vous utilisez est déterminée par la charge de travail que vous souhaitez exécuter. La taille que vous choisissez détermine ensuite des facteurs comme la puissance de traitement, la mémoire et la capacité de stockage. Azure propose différentes tailles vous permettant de prendre en charge de nombreux types d'utilisation.

Azure facture un [prix horaire](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en fonction de la taille et du système d’exploitation de la machine virtuelle. Pour les heures partielles, Azure facture uniquement les minutes utilisées. Le stockage est facturé séparément.

### <a name="vm-limits"></a>Limites des machines virtuelles
Votre abonnement comporte des [limites de quota](../../azure-subscription-service-limits.md) par défaut qui peuvent avoir un impact négatif sur le déploiement d’un grand nombre de machines virtuelles pour votre projet. La limite est de 20 machines virtuelles par région et par abonnement. Les limites peuvent être augmentées en soumettant un ticket de support demandant leur hausse.

### <a name="operating-system-disks-and-images"></a>Images et disques du système d’exploitation
Les machines virtuelles utilisent des [disques durs virtuels](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour stocker leurs données et leur système d’exploitation (SE). Les disques durs virtuels sont également utilisés pour les images à partir desquelles vous pouvez choisir d'installer un système d'exploitation. 

Azure fournit de nombreuses [images Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) à utiliser avec différentes versions et différents types de systèmes d’exploitation Windows Server. Les images Marketplace sont identifiées par l’éditeur d’images, l’offre, la référence (SKU) et la version (la version est généralement spécifiée en dernier). 

Ce tableau présente différents moyens de trouver les informations d’une image.

| Méthode | Description |
| --- | --- |
| Portail Azure |Les valeurs sont spécifiées automatiquement pour vous lorsque vous sélectionnez une image à utiliser. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "emplacement"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "emplacement" -Publisher "nomÉditeur"<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location "emplacement" -Publisher "nomÉditeur" -Offer "nomOffre" |
| API REST |[Lister les éditeurs d’images](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Lister les offres d’images](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Lister les références d’images](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/en-us/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list_publishers) --location "emplacement"<BR>[az vm image list-offers](https://docs.microsoft.com/en-us/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list_offers) --location "emplacement" --publisher "nomÉditeur"<BR>[az vm image list-skus](https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest#az_vm_list_skus) --location "emplacement" --publisher "nomÉditeur" --offer "nomOffre"|

Vous pouvez choisir de [charger et utiliser votre propre image](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) ; lorsque vous procédez ainsi, le nom de l’éditeur, l’offre et la référence ne sont pas utilisés.

### <a name="extensions"></a>Extensions
Les [extensions](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de machines virtuelles étendent les fonctionnalités de votre machine virtuelle par le biais de la configuration post-déploiement et de tâches automatisées.

Ces tâches courantes peuvent être accomplies à l’aide des extensions :

* **Exécuter des scripts personnalisés** : [l’extension de script personnalisé](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vous permet de configurer des charges de travail sur la machine virtuelle en exécutant votre script pendant l’approvisionnement de la machine virtuelle.
* **Déployer et gérer des configurations** : [l’extension de configuration d’état souhaité (DSC) PowerShell](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vous permet de configurer DSC sur une machine virtuelle pour gérer les environnements et les configurations.
* **Collecter les données de diagnostic** : [l’extension des diagnostics Azure](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vous permet de configurer la machine virtuelle de sorte qu’elle collecte des données de diagnostics utilisées pour surveiller l’intégrité de votre application.

### <a name="related-resources"></a>Ressources associées
Les ressources de cette table sont utilisées par la machine virtuelle et doivent exister ou être créées lors de sa création.

| Ressource | Obligatoire | Description |
| --- | --- | --- |
| [Groupe de ressources](../../azure-resource-manager/resource-group-overview.md) |OUI |La machine virtuelle doit être contenue dans un groupe de ressources. |
| [Compte de stockage](../../storage/common/storage-create-storage-account.md) |OUI |La machine virtuelle doit stocker ses disques durs virtuels dans le compte de stockage. |
| [Réseau virtuel](../../virtual-network/virtual-networks-overview.md) |OUI |La machine virtuelle doit faire partie d’un réseau virtuel. |
| [Adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Non  |La machine virtuelle peut avoir une adresse IP publique pour être accessible à distance. |
| [Interface réseau](../../virtual-network/virtual-network-network-interface.md) |OUI |La machine virtuelle a besoin de l’interface réseau pour communiquer sur le réseau. |
| [Disques de données](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Non  |La machine virtuelle peut comprendre des disques de données pour développer ses capacités de stockage. |

## <a name="how-do-i-create-my-first-vm"></a>Comment créer sa première machine virtuelle ?
Vous avez plusieurs possibilités pour la création de votre machine virtuelle. Votre choix dépend de votre environnement. 

Ce tableau fournit des informations pour vous aider à créer votre machine virtuelle.

| Méthode | Article |
| --- | --- |
| Portail Azure |[Créer une machine virtuelle exécutant Windows dans le portail](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Modèles |[Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Créer une machine virtuelle Windows à l’aide de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Kits de développement logiciel (SDK) client |[Déployer des ressources Azure en C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| API REST |[Créer ou mettre à jour une machine virtuelle](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Créer une machine virtuelle avec l’interface de ligne de commande Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

Même si l’on souhaite que cela ne se produise jamais, des problèmes peuvent survenir. Si vous rencontrez cette situation, consultez les informations dans [Résolution des problèmes de déploiement Resource Manager lors de la création d’une machine virtuelle Windows dans Azure](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Comment gérer la machine virtuelle créée ?
Les machines virtuelles peuvent être gérées à l’aide d’un portail sur navigateur, d’outils de ligne de commande avec prise en charge des scripts ou directement au moyen des API. Certaines tâches de gestion courantes que vous êtes susceptible d’effectuer récupèrent des informations sur une machine virtuelle, s’y connectent, gèrent la disponibilité et effectuent des sauvegardes.

### <a name="get-information-about-a-vm"></a>Obtenir des informations sur une machine virtuelle
Ce tableau montre différents moyens d’obtenir des informations sur une machine virtuelle.

| Méthode | Description |
| --- | --- |
| Portail Azure |Dans le menu Hub, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle dans la liste. Dans le panneau de la machine virtuelle, vous pouvez consulter les informations, fixer des valeurs et surveiller les métriques. |
| Azure PowerShell |Pour plus d’informations sur l’utilisation de PowerShell pour gérer des machines virtuelles, voir [Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| de l’API REST |Utilisez l’opération [Récupérer des informations sur la machine virtuelle](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) pour obtenir des informations sur une machine virtuelle. |
| Kits de développement logiciel (SDK) client |Pour plus d’informations sur l’utilisation de C# pour gérer des machines virtuelles, consultez [Gérer des Machines Virtuelles Azure à l’aide d’Azure Ressources Manager et de C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Azure CLI |Pour plus d’informations sur l’utilisation de Azure CLI pour gérer les machines virtuelles, consultez [Informations de référence sur Azure CLI](https://docs.microsoft.com/en-us/cli/azure/vm). |

### <a name="log-on-to-the-vm"></a>Se connecter à la machine virtuelle
Vous utilisez le bouton [Connecter](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) dans le Portail Azure pour démarrer une session Bureau à distance (RDP). Des problèmes peuvent survenir lorsque vous tentez d’utiliser une connexion à distance. Si vous en rencontrez, consultez les informations d’aide dans [Résoudre les problèmes de connexions bureau à distance à une machine virtuelle Azure sous Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="manage-availability"></a>Gérer la disponibilité
Il est important de comprendre comment [garantir la haute disponibilité](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) de votre application. Cette configuration implique la création de plusieurs machines virtuelles afin qu’il y en ait au moins une en cours d’exécution.

Afin que votre déploiement puisse bénéficier de notre contrat de niveau de service de 99,95 % pour les machines virtuelles, vous devez déployer au moins deux machines virtuelles exécutant votre charge de travail à l’intérieur d’un [groupe à haute disponibilité](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Grâce à cette configuration, vos machines virtuelles sont réparties sur plusieurs domaines d’erreur et déployées sur des hôtes ayant des fenêtres de maintenance distinctes. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explique la disponibilité garantie d’Azure dans son ensemble.

### <a name="back-up-the-vm"></a>Sauvegarder la machine virtuelle
Un [coffre Recovery Services](../../backup/backup-introduction-to-azure-backup.md) est utilisé pour protéger les données et les actifs dans la Sauvegarde Azure et Azure Site Recovery Services. Vous pouvez utiliser un coffre Recovery Services pour [déployer et gérer les sauvegardes des machines virtuelles déployées avec le modèle Resource Manager à l’aide de PowerShell](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Étapes suivantes
* Si votre objectif est d’utiliser des machines virtuelles Linux, consultez [Azure et Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour en savoir plus sur les instructions de configuration de votre infrastructure, consultez [Exploration d’un exemple d’infrastructure Azure](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
