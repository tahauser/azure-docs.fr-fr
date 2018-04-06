---
title: Bonnes pratiques relatives aux performances de SQL Server dans Azure | Microsoft Docs
description: Présente les meilleures pratiques pour optimiser les performances de SQL Server dans Microsoft Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/20/2018
ms.author: jroth
ms.openlocfilehash: 2aa066caf6239f29038228c3c91607d913e70682
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure

## <a name="overview"></a>Vue d'ensemble

Cet article présente les meilleures pratiques pour optimiser les performances de SQL Server dans la machine virtuelle Microsoft Azure. Lorsque vous exécutez SQL Server dans Microsoft Azure Virtual Machines, nous vous recommandons de continuer à utiliser les mêmes options de réglage des performances de base de données qui s’appliquent à SQL Server dans un environnement serveur local. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, comme la taille de la machine virtuelle et la configuration des disques de données.

Lors de la création d’images SQL Server, [pensez à approvisionner vos machines virtuelles dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Les machines virtuelles SQL Server configurées dans le portail avec Resource Manager respectent les meilleures pratiques.

Cet article est axé sur l’obtention des *meilleures* performances pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations suivantes. Tenez compte de vos besoins de performances et de vos modèles de charges de travail lors de l’évaluation de ces recommandations.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server sur Azure Virtual Machines :

| Domaine | Optimisations |
| --- | --- |
| [Taille de la machine virtuelle](#vm-size-guidance) |[Édition SQL Enterprise DS3](../sizes-memory.md) ou supérieure.<br/><br/>[DS2](../sizes-memory.md) ou supérieure pour SQL Server Standard Edition ou SQL Server Web Edition. |
| [Stockage](#storage-guidance) |Utiliser [Premium Storage](../premium-storage.md). Le stockage standard n’est recommandé que pour le développement et le test.<br/><br/>Conservez le [compte de stockage](../../../storage/common/storage-create-storage-account.md) et la machine virtuelle SQL Server dans la même région.<br/><br/>Désactivez le [stockage géo-redondant](../../../storage/common/storage-redundancy.md) (géo-réplication) d’Azure sur le compte de stockage. |
| [Disques](#disks-guidance) |Utilisez au moins 2 [disques P30](../premium-storage.md#scalability-and-performance-targets) (1 pour les fichiers journaux ; 1 pour les fichiers de données et TempDB).<br/><br/>Éviter d’utiliser des disques de système d’exploitation ou temporaires pour le stockage ou la journalisation des bases de données.<br/><br/>Activer la mise en cache de lecture sur le ou les disques hébergeant les fichiers de données et TempDB.<br/><br/>Ne pas activer la mise en cache sur le ou les disques hébergeant le fichier journal.<br/><br/>Important : arrêtez le service SQL Server lorsque vous modifiez le paramètre de cache d’un disque de machine virtuelle Azure.<br/><br/>Entrelacer plusieurs disques de données Azure pour obtenir un débit d’E/S plus élevé.<br/><br/>Formatez avec des tailles d’allocation documentées. |
| [E/S](#io-guidance) |Activez la compression des pages de base de données.<br/><br/>Activer l’initialisation de fichiers instantanée pour les fichiers de données.<br/><br/>Limiter la croissance automatique sur la base de données.<br/><br/>Désactiver la réduction automatique sur la base de données.<br/><br/>Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système.<br/><br/>Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.<br/><br/>Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données.<br/><br/>Activer les pages verrouillées.<br/><br/>Appliquez les correctifs de performances de SQL Server. |
| [Spécifique aux fonctionnalités](#feature-specific-guidance) |Sauvegardez directement dans le stockage d’objets blob. |

Pour plus d’informations sur *comment* et *pourquoi* effectuer ces optimisations, passez en revue les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils liés à la taille des machines virtuelles

Pour les applications sensibles aux performances, il est recommandé d’utiliser les [tailles de machines virtuelles](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)suivantes :

* **SQL Server Enterprise Edition**: DS3 ou supérieure
* **SQL Server Standard Edition ou SQL Server Web Edition**: DS2 ou supérieure

## <a name="storage-guidance"></a>Conseils liés au stockage

Les machines virtuelles de série DS (ainsi que des séries DSv2 et GS) prennent en charge le [stockage Premium](../premium-storage.md). L’option Premium Storage est recommandée pour toutes les charges de travail de production.

> [!WARNING]
> L’option Standard Storage possède différents temps de latence et une bande passante variable. Elle est recommandée uniquement pour les charges de travail de développement/de test. Les charges de production doivent utiliser Premium Storage.

Par ailleurs, nous vous recommandons de créer votre compte de stockage Azure dans le même centre de données que vos machines virtuelles SQL Server afin de réduire les délais de transfert. Lors de la création d’un compte de stockage, désactivez la géo-réplication, étant donné que la cohérence de l’ordre d’écriture sur différents disques n’est pas garantie. Envisagez plutôt de configurer une technologie de récupération d’urgence de SQL Server entre deux centres de données Azure. Pour plus d’informations, consultez [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Conseils liés aux disques

Il existe trois types de disques principaux sur une machine virtuelle Azure :

* **Disque de système d’exploitation** : quand vous créez une machine virtuelle Azure, la plateforme attache au moins un disque (désigné par la lettre **C**) à la machine virtuelle en tant que disque de système d’exploitation. Ce disque est un disque dur virtuel (VHD) stocké en tant qu’objet blob de pages dans le stockage.
* **Disque temporaire** : les machines virtuelles Azure contiennent un autre disque, appelé disque temporaire (portant le nom de lecteur **D**:). C’est un disque du nœud qui peut être utilisé comme un espace de travail temporaire.
* **Disques de données**: vous pouvez également attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données ; ces disques sont également stockés en tant qu’objets blob de pages.

Les sections suivantes décrivent des recommandations pour l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) que vous pouvez amorcer et monter comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur **C**.

La stratégie de mise en cache par défaut sur le disque du système d’exploitation est **Lecture/Écriture**. Pour les applications sensibles aux performances, nous vous recommandons d’utiliser le disque de données au lieu du disque du système d’exploitation. Reportez-vous à la section sur les disques de données ci-dessous.

### <a name="temporary-disk"></a>Disque temporaire

Le disque de stockage temporaire, désigné par la lettre de **D**:, n’est pas persistant dans Azure Blob Storage. Ne stockez pas vos fichiers de base de données utilisateur ou vos fichiers journaux des transactions utilisateur sur le lecteur **D**:.

Sur les machines virtuelles de série D, Dv2 et G, le lecteur temporaire réside sur un disque SSD. Si votre charge de travail exploite intensivement TempDB (par exemple, pour les objets temporaires ou des jointures complexes), le stockage de TempDB sur le lecteur **D** peut entraîner un débit plus élevé et réduire la latence de TempDB.

Pour les machines virtuelles qui prennent en charge le stockage Premium (de série DS, DSv2 et GS), nous vous recommandons de stocker TempDB sur un disque qui prend en charge le stockage Premium avec la mise en cache en lecture activée. Il existe une exception à cette recommandation : si votre utilisation de TempDB est intensive en écriture, vous pouvez obtenir des performances supérieures en stockant TempDB sur le lecteur **D** local, qui est également un disque SSD sur ces tailles de machines.

### <a name="data-disks"></a>Disques de données

* **Utilisation de disques de données pour les données et les fichiers journaux** : si vous n’utilisez pas l’entrelacement de disques, utilisez au moins 2 [disques P30](../premium-storage.md#scalability-and-performance-targets) de stockage Premium, l’un pour contenir le ou les fichiers journaux et l’autre pour contenir les données, ainsi que le ou les fichiers TempDB. Chaque disque de stockage Premium fournit un nombre d’opérations d’E/S par seconde et une bande passante (Mo/s) en fonction de sa taille, comme décrit dans l’article suivant : [Utilisation du stockage Premium pour les disques](../premium-storage.md). Si vous utilisez une technique d’entrelacement de disques, comme des espaces de stockage, vous devez placer tous les fichiers sur le même disque.

   > [!NOTE]
   > Lorsque vous configurez une machine virtuelle SQL Server dans le portail, vous avez la possibilité de modifier votre configuration de stockage. Selon votre configuration, Azure configure un ou plusieurs disques. Plusieurs disques sont combinés en un pool de stockage unique par entrelacement. Les fichiers journaux et de données se trouvent dans cette configuration. Pour plus d’informations, voir [Configuration du stockage pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Entrelacement de disques**: pour augmenter le débit, vous pouvez ajouter des disques de données supplémentaires et utiliser l’entrelacement de disques. Pour déterminer le nombre de disques de données, vous devez analyser le nombre d’opérations d’E/S par seconde et de bande passante disponibles pour vos fichiers journaux, vos données et vos fichiers TempDB. Notez que les différentes tailles de machine virtuelle sont associées à des limites spécifiques concernant la bande passante et le nombre d’opérations d’E/S par seconde pris en charge. Pour en savoir plus, consultez les tableaux relatifs aux nombre d’opérations d’E/S par seconde en fonction des [tailles de machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Respectez les recommandations suivantes :

  * Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx) en respectant les consignes suivantes :

      1. Définissez l’intervalle (taille de bande) sur 64 Ko (65 536 octets) pour les charges de travail OLTP et sur 256 Ko (262 144 octets) pour les charges de travail d’entrepôt de données, afin d’éviter qu’un alignement incorrect de la partition n’affecte les performances. Ces paramètres doivent être définis avec PowerShell.
      1. Nombre de colonnes définies = nombre de disques physiques. Utilisez PowerShell lorsque vous configurez plus de 8 disques (et non l’interface utilisateur du gestionnaire de serveur). 

    Par exemple, le PowerShell suivant crée un nouveau pool de stockage avec un entrelacement de 64 Ko et 2 colonnes :

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes entrelacés de système d’exploitation). La taille de l’entrelacement restera 64 Ko. Remarque : cette option est déconseillée à partir des versions Windows 8/Windows Server 2012. Pour plus d’informations, consultez les instructions du support dans [Transition du service de disque virtuel vers l’API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Si vous utilisez les [espaces de stockage direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) avec un scénario comme [Instances de cluster de basculement SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), vous devez configurer un pool unique. Notez que même si vous créez différents volumes sur ce pool unique, tous partageront les mêmes caractéristiques, par exemple la même stratégie de mise en cache. 

  * Déterminez le nombre de disques associés à votre pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, consultez [Tailles des machines virtuelles](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Si vous n’utilisez pas le stockage Premium (scénarios de développement/de test), nous vous recommandons d’ajouter le nombre maximal de disques de données pris en charge par la [taille de votre machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et d’utiliser l’entrelacement de disques.

* **Stratégie de mise en cache** : pour les disques de données de stockage Premium, activez la mise en cache de lecture uniquement sur les disques de données hébergeant vos fichiers de données et les fichiers de données TempDB. Si vous n’utilisez pas Premium Storage, n’activez aucune mise en cache sur les disques de données. Pour obtenir des instructions sur la configuration de la mise en cache des disques, consultez les articles suivants. Pour le modèle de déploiement classique (ASM), consultez : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Pour le modèle de déploiement Azure Resource Manager, consultez : [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) et [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1).

  > [!WARNING]
  > Arrêtez le service SQL Server lorsque vous modifiez le paramètre de cache de disques de machines virtuelles Azure pour éviter toute altération de la base de données.

* **Taille d’unité d’allocation NTFS**: lors du formatage du disque de données, il est recommandé d’utiliser une taille d’unité d’allocation de 64 Ko pour les fichiers de données et les fichiers journaux, ainsi que pour TempDB.

* **Meilleures pratiques de gestion des disques**: lors de la suppression d’un disque de données ou de la modification de son type de cache, arrêtez le service SQL Server. Lorsque les paramètres de mise en cache sont modifiés sur le disque du système d’exploitation, Azure arrête la machine virtuelle, modifie le type de cache et redémarre la machine virtuelle. Lorsque les paramètres du cache d’un disque de données sont modifiés, la machine virtuelle n’est pas arrêtée, mais le disque de données est détaché de la machine virtuelle lors de la modification, puis rattaché.

  > [!WARNING]
  > Si le service SQL Server n’est pas arrêté au cours de ces opérations, cela peut provoquer une altération de la base de données.

## <a name="io-guidance"></a>Recommandations liées aux E/S

* Pour obtenir les meilleurs résultats avec Premium Storage, vous devez paralléliser vos applications et vos demandes. Premium Storage est conçu pour les scénarios où la profondeur de file d’attente est supérieure à 1. Les améliorations seront donc minimes ou inexistantes pour les demandes de série à thread unique (même si elles sont gourmandes en stockage). Par exemple, cela peut affecter les résultats des tests à thread unique des outils d’analyses de performances, par exemple SQLIO.

* Envisagez d’utiliser un [compression des pages de base de données](https://msdn.microsoft.com/library/cc280449.aspx) , car elle peut améliorer les performances de charges de travail gourmandes en E/S. Toutefois, la compression de données peut augmenter la consommation d’UC sur le serveur de base de données.

* Envisagez d’activer l’initialisation instantanée des fichiers pour réduire le temps requis pour l’allocation initiale des fichiers. Pour tirer parti de l’initialisation instantanée des fichiers, vous devez accorder l’autorisation SE_MANAGE_VOLUME_NAME au compte de service SQL Server (MSSQLSERVER) et l’ajouter à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. Si vous utilisez une image de plateforme SQL Server pour Azure, le compte de service par défaut (NT Service\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. En d’autres termes, l’initialisation instantanée des fichiers n’est pas activée dans une image de plateforme SQL Server pour Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **Effectuer les tâches de maintenance de volume** , redémarrez le service SQL Server. Des considérations de sécurité liées à l’utilisation de cette fonctionnalité peuvent exister. Pour plus d’informations, consultez [Initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** ) est considérée comme un simple plan d’urgence en cas de croissance plus rapide que prévu. Ne gérez pas la croissance de vos données et journaux quotidiennement avec la croissance automatique. En cas d’utilisation de la croissance automatique, augmentez préalablement le fichier à l’aide du commutateur Taille.

* Vérifiez que la fonctionnalité de réduction automatique ( **autoshrink** ) est désactivée afin d’éviter une surcharge inutile susceptible d’affecter négativement les performances.

* Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système. Pour plus d’informations, consultez [Déplacer des bases de données système](https://msdn.microsoft.com/library/ms345408.aspx).

* Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données. Cette opération peut être effectuée dans le Gestionnaire de configuration SQL Server en double-cliquant sur l’instance de SQL Server et en sélectionnant des propriétés. Il est possible de modifier les paramètres du fichier journal et du fichier de trace sous l’onglet **Paramètres de démarrage** . Le répertoire de vidage est spécifié sous l’onglet **Avancé** . La capture d’écran suivante indique où rechercher le paramètre de démarrage du journal des erreurs.

    ![Capture d’écran du journal des erreurs SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données. Utilisez les recommandations de cet article, puis apportez les modifications souhaitées dans la fenêtre Propriétés du serveur. Pour obtenir des instructions, consultez [Afficher ou modifier les emplacements par défaut des fichiers de données et des fichiers journaux (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La capture d’écran suivante montre comment effectuer ces modifications.

    ![Journal des données et fichiers de sauvegarde SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Activez les pages verrouillées pour réduire les activités d’E/S et de pagination. Pour plus d’informations, consultez [Activer l’option Lock Pages in Memory (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Si vous exécutez SQL Server 2012, installez la mise à jour cumulative 10 Service Pack 1. Elle contient le correctif qui permet de résoudre les problèmes de performances d’E/S lorsque vous exécutez une sélection dans l’instruction de table temporaire dans SQL Server 2012. Pour plus d’informations, consultez cet [article de la Base de connaissances](http://support.microsoft.com/kb/2958012).

* Envisagez de compresser tous les fichiers de données lors des transferts vers et depuis Azure.

## <a name="feature-specific-guidance"></a>Conseils spécifiques aux fonctionnalités

Certains déploiements peuvent bénéficier de plus grands avantages en termes de performances à l’aide de techniques de configuration avancées. La liste suivante présente certaines fonctionnalités SQL Server qui peuvent vous aider à améliorer les performances :

* **Sauvegarde vers le stockage Azure**: lors de la création de sauvegardes pour un serveur SQL Server s’exécutant sur des machines virtuelles Azure, vous pouvez vous référer à [Sauvegarde de SQL Server vers une URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2, et recommandée pour la sauvegarde vers les disques de données attachés. Lors de la sauvegarde ou la restauration vers/depuis Azure Storage, suivez les recommandations indiquées dans [Meilleures pratiques et résolution des problèmes pour la sauvegarde de SQL Server vers une URL et Restauration à partir de sauvegardes stockées dans Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes en utilisant la [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).

    Pour les versions antérieures à SQL Server 2012, vous pouvez utiliser l’ [outil de sauvegarde SQL Server vers Azure](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut augmenter le débit de sauvegarde à l’aide de plusieurs cibles d’entrelacement de sauvegarde.

* **Fichiers de données SQL Server dans Azure**: cette nouvelle fonctionnalité, nommée [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. L’exécution de SQL Server avec des fichiers de données dans Azure offre des caractéristiques de performances comparables à l’utilisation de disques de données Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour les meilleures pratiques de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-security.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](virtual-machines-windows-sql-server-iaas-faq.md).
