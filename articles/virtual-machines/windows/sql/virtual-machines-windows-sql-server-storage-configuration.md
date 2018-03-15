---
title: Configuration du stockage pour les machines virtuelles SQL Server | Microsoft Docs
description: "Cette rubrique décrit comment Azure configure le stockage pour les machines virtuelles SQL Server lors de la configuration (modèle de déploiement Resource Manager). Elle explique également comment vous pouvez configurer le stockage pour vos machines virtuelles SQL Server existantes."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 21c8b955d48da03559097db93b2cb66029a203ec
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuration du stockage pour les machines virtuelles SQL Server
Lorsque vous configurez une image de machine virtuelle SQL Server dans Azure, le portail permet d’automatiser la configuration de votre stockage. Cela inclut l’attachement du stockage à la machine virtuelle, ce qui permet à SQL Server d’y accéder et le configure de façon à l’optimiser en fonction de vos besoins spécifiques.

Cette rubrique explique comment Azure configure le stockage pour vos machines virtuelles SQL Server à la fois lors de la configuration et pour les machines virtuelles existantes. Cette configuration est basée sur les [meilleures pratiques pour les performances](virtual-machines-windows-sql-performance.md) pour les machines virtuelles Azure exécutant SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>configuration requise
Pour utiliser les paramètres de configuration automatisée du stockage, votre machine virtuelle doit répondre aux conditions suivantes :

* Configurée avec une [image de galerie SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo);
* Utilise le [modèle de déploiement Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md);
* Utilise [Premium Storage](../premium-storage.md).

## <a name="new-vms"></a>Nouvelles machines virtuelles
Les sections suivantes décrivent comment configurer le stockage pour les nouvelles machines virtuelles SQL Server.

### <a name="azure-portal"></a>Portail Azure
Lorsque vous configurez une machine virtuelle Azure à l’aide d’une image de galerie SQL Server, vous pouvez choisir de configurer automatiquement le stockage pour votre nouvelle machine virtuelle. Vous indiquez la taille du stockage, ses limites de performances et le type de charge de travail. La capture d’écran suivante montre le panneau de configuration du stockage utilisé lors de la configuration de la machine virtuelle SQL.

![Configuration du stockage des machines virtuelles SQL Server lors de la configuration](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

En fonction de votre choix, Azure exécute les tâches de configuration du stockage suivantes après avoir créé la machine virtuelle :

* Crée et attache les disques de données Premium Storage à la machine virtuelle.
* Configure les disques de données pour que SQL Server puisse y accéder.
* Configure les disques de données dans un pool de stockage basé sur les critères indiqués pour la taille et les performances (E/S par seconde et débit).
* Associe le pool de stockage à un nouveau lecteur sur la machine virtuelle.
* Optimise ce nouveau lecteur en fonction du type de charge de travail que vous avez spécifié (entrepôt de données, traitement transactionnel ou général).

Pour plus d’informations sur la manière dont Azure configure les paramètres de stockage, consultez la section [Configuration du stockage](#storage-configuration). Pour une description détaillée de la création d’une machine virtuelle SQL Server dans le portail Azure, consultez [le didacticiel de configuration](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Modèles Resource Manager
Si vous utilisez les modèles Resource Manager suivants, deux disques de données premium sont attachés par défaut, sans aucune configuration de pool de stockage. Toutefois, vous pouvez personnaliser ces modèles afin de modifier le nombre de disques de données premium attachés à la machine virtuelle.

* [Créer des machines virtuelles avec sauvegarde automatisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Créer des machines virtuelles avec mise à jour corrective automatisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Créer des machines virtuelles avec AKV Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles SQL Server existantes, vous pouvez modifier certains paramètres de stockage dans le portail Azure. Sélectionnez votre machine virtuelle, accédez à la zone Paramètres, puis sélectionnez Configuration de SQL Server. Le panneau de configuration de SQL Server indique l’utilisation actuelle du stockage de votre machine virtuelle. Tous les lecteurs qui existent sur votre machine virtuelle apparaissent dans ce graphique. Pour chaque lecteur, l’espace de stockage s’affiche dans quatre sections :

* Données SQL
* Journal SQL
* Autre (stockage non-SQL)
* Disponible

![Configurer le stockage pour les machines virtuelles SQL Server existantes](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Pour configurer le stockage afin d’ajouter un nouveau lecteur ou d’étendre un lecteur existant, cliquez sur le lien Modifier au-dessus du graphique.

Les options de configuration que vous voyez varient selon que vous avez déjà utilisé cette fonctionnalité ou non. Lorsque vous l’utilisez pour la première fois, vous pouvez spécifier vos besoins en stockage pour votre nouveau lecteur. Si vous avez déjà utilisé cette fonctionnalité pour créer un lecteur, vous pouvez choisir d’étendre le stockage correspondant.

### <a name="use-for-the-first-time"></a>Première utilisation
Si c’est la première fois que vous utilisez cette fonctionnalité, vous pouvez spécifier les limites de taille et de performances du stockage du nouveau lecteur. Le fonctionnement est le même que lors de la configuration. La principale différence est que vous n’êtes pas autorisé à indiquer le type de charge de travail. Cette restriction évite l’interruption des configurations SQL Server existantes sur la machine virtuelle.

![Configurer les curseurs de stockage SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crée un nouveau lecteur, en fonction de vos spécifications. Dans ce scénario, Azure exécute les tâches de configuration du stockage suivantes :

* Crée et attache les disques de données Premium Storage à la machine virtuelle.
* Configure les disques de données pour que SQL Server puisse y accéder.
* Configure les disques de données dans un pool de stockage basé sur les critères indiqués pour la taille et les performances (E/S par seconde et débit).
* Associe le pool de stockage à un nouveau lecteur sur la machine virtuelle.

Pour plus d’informations sur la manière dont Azure configure les paramètres de stockage, consultez la section [Configuration du stockage](#storage-configuration).

### <a name="add-a-new-drive"></a>Ajout d’un nouveau lecteur
Si vous avez déjà configuré le stockage sur votre machine virtuelle SQL Server, l’extension du stockage donne accès à deux nouvelles options. La première option consiste à ajouter un nouveau lecteur, ce qui peut augmenter le niveau de performances de votre machine virtuelle.

![Ajouter un nouveau lecteur à une machine virtuelle SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Toutefois, après avoir ajouté le lecteur, vous devez effectuer quelques configurations manuelles supplémentaires pour obtenir l’augmentation des performances.

### <a name="extend-the-drive"></a>Extension du lecteur
L’autre option pour augmenter le stockage est d’étendre le lecteur existant. Cette option augmente la capacité de stockage de votre lecteur, mais n’augmente pas les performances. Avec les pools de stockage, vous ne pouvez pas modifier le nombre de colonnes une fois le pool de stockage créé. Le nombre de colonnes détermine le nombre d’écritures parallèles, qui peuvent être réparties sur les disques de données. Par conséquent, les disques de données ajoutés ne peuvent pas augmenter les performances. Ils peuvent uniquement fournir davantage d’espace de stockage pour les données en cours d’écriture. Cette limitation signifie également que, lorsque vous étendez le lecteur, le nombre de colonnes détermine le nombre minimal de disques de données que vous pouvez ajouter. Par conséquent, si vous créez un pool de stockage comprenant quatre disques de données, le nombre de colonnes est également égal à quatre. Chaque fois que vous étendez le stockage, vous devez ajouter au moins quatre disques de données.

![Étendre un lecteur d’une machine virtuelle SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuration du stockage
Cette section répertorie les modifications de configuration de stockage qu’Azure exécute automatiquement au cours de la configuration de la machine virtuelle SQL sur le portail Azure.

* Si vous avez sélectionné moins de deux téraoctets de stockage pour votre machine virtuelle, Azure ne crée pas de pool de stockage.
* Si vous avez sélectionné au moins deux téraoctets de stockage pour votre machine virtuelle, Azure configure un pool de stockage. La prochaine section de cette rubrique fournit les détails de la configuration du pool de stockage.
* La configuration automatique du stockage utilise toujours des disques de données P30 [Premium Storage](../premium-storage.md) . Par conséquent, il existe un mappage 1:1 entre le nombre de téraoctets sélectionné et le nombre de disques de données attachés à votre machine virtuelle.

Pour plus d’informations sur les tarifs, consultez la page [Tarifs de Storage](https://azure.microsoft.com/pricing/details/storage) dans l’onglet **Stockage sur disque** .

### <a name="creation-of-the-storage-pool"></a>Création du pool de stockage
Azure utilise les paramètres suivants pour créer le pool de stockage sur les machines virtuelles SQL Server.

| Paramètre | Valeur |
| --- | --- |
| Taille de l’entrelacement |256 Ko (entrepôt de données) ; 64 Ko (transactionnel) |
| Tailles du disque |1 To chacun |
| Cache |Lire |
| Taille d'allocation |Taille d'unité d'allocation NTFS = 64 Ko |
| Initialisation instantanée des fichiers |activé |
| Verrouillage des pages en mémoire |activé |
| Récupérer |Récupération simple (sans résilience) |
| Nombre de colonnes |Nombre de disques de données<sup>1</sup> |
| Emplacement de TempDB |Stocké sur les disques de données<sup>2</sup> |

<sup>1</sup> Une fois le pool de stockage créé, vous ne pouvez pas modifier le nombre de colonnes qu’il contient.

<sup>2</sup> Ce paramètre s’applique uniquement au premier lecteur que vous créez à l’aide de la fonctionnalité de configuration du stockage.

## <a name="workload-optimization-settings"></a>Paramètres d’optimisation de la charge de travail
Le tableau suivant décrit les trois options de type de charge de travail disponibles, ainsi que leurs optimisations :

| Type de charge de travail | Description | Optimisations |
| --- | --- | --- |
| **Généralités** |Paramètre par défaut qui prend en charge la plupart des charges de travail |Aucun |
| **Traitement transactionnel** |Optimise le stockage pour les charges de travail OLTP de base de données traditionnelles |Indicateur de trace 1117<br/>Indicateur de trace 1118 |
| **Entrepôt de données** |Optimise le stockage pour les charges de travail d’analyse et de création de rapports |Indicateur de trace 610<br/>Indicateur de trace 1117 |

> [!NOTE]
> Vous pouvez uniquement spécifier le type de charge de travail si vous configurez une machine virtuelle SQL en la sélectionnant à l’étape de configuration du stockage.
>
>

## <a name="next-steps"></a>Étapes suivantes
Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
