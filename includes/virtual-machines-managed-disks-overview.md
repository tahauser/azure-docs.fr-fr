# <a name="azure-managed-disks-overview"></a>Vue d’ensemble d’Azure Managed Disks

Azure Managed Disks simplifie la gestion des disques des machines virtuelles Azure IaaS, en gérant les [comptes de stockage](../articles/storage/common/storage-introduction.md) associés aux disques de machines virtuelles. Vous spécifiez simplement le type ([Premium](../articles/virtual-machines/windows/premium-storage.md) ou [Standard](../articles/virtual-machines/windows/standard-storage.md)) et la taille du disque dont vous avez besoin, et Azure crée et gère le disque pour vous.

## <a name="benefits-of-managed-disks"></a>Avantages des disques managés

Examinons certains des avantages liés à l’utilisation de disques gérés, en commençant par cette vidéo de Channel 9, [Meilleure résilience de machine virtuelle Azure avec des disques gérés](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Déploiement de machines virtuelles simple et évolutif

Les disques managés gèrent le stockage pour vous en arrière-plan. Auparavant, vous deviez créer des comptes de stockage dédiés à la prise en charge des disques (fichiers de disques durs virtuels) de vos machines virtuelles Azure. Lors d’une montée en puissance, il était nécessaire de vous assurer d’avoir créé la quantité suffisante de comptes de stockage supplémentaires, ceci pour ne pas dépasser la limite d’E/S associée au stockage de vos disques. Maintenant que votre stockage est géré par Managed Disks, vous n’êtes plus restreint par les limites des comptes de stockage (comme 20 000 E/S par seconde et par compte). Vous n’avez plus à copier vos images personnalisées (fichiers de disques durs virtuels) sur plusieurs comptes de stockage. Vous pouvez les gérer à partir d’un emplacement centralisé (un compte de stockage par région Azure) et les utiliser pour créer des centaines de machines virtuelles dans un abonnement.

Managed Disks vous permet de créer jusqu’à 10 000 **disques** de machines virtuelles dans un abonnement. Ainsi, vous êtes en mesure de mettre en œuvre des milliers de **machines virtuelles** dans un seul abonnement. Par ailleurs, cette fonctionnalité optimise l’évolutivité des [groupes de machines virtuelles identiques](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) (VMSS, Virtual Machine Scale Sets), en vous donnant les moyens de créer jusqu’à 1 millier de machines virtuelles dans une instance VMSS à l’aide d’une image de Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Fiabilité supérieure des groupes à haute disponibilité

Les disques gérés accroît la fiabilité des groupes à haute disponibilité en garantissant que les disques des [machines virtuelles d’un groupe à haute disponibilité](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) sont suffisamment isolés les uns des autres, ceci pour éviter les points de défaillance uniques. Comment le service procède-t-il ? Il place automatiquement les disques dans différentes unités d’échelle de stockage (horodatages). Si un horodatage est mis en échec en raison d’une défaillance matérielle ou logicielle, seules les instances de machine virtuelle possédant des disques sur ces horodatages sont mises en échec. Par exemple, supposons qu’une de vos applications est exécutée sur 5 machines virtuelles, qui sont hébergées dans un groupe à haute disponibilité. Les disques de ces machines virtuelles ne seront pas stockés dans le même horodatage. Par conséquent, si un horodatage est mis en échec, les autres instances de l’application continuent de s’exécuter.

### <a name="highly-durable-and-available"></a>Disponibilité et durabilité élevées

Les disques Azure sont conçus pour offrir une disponibilité de 99,999 %. Vous pouvez travailler en toute quiétude en sachant que vous disposez de trois réplicas de vos données, ce qui offre plus de durabilité. Si un ou même deux de vos réplicas rencontrent des problèmes, les autres réplicas peuvent assurer la persistance de vos données et offrir une grande tolérance face aux pannes. Cette architecture a permis à Azure de fournir de façon cohérente une durabilité de classe Entreprise pour les disques IaaS, avec un taux de défaillance annuel inégalé dans le secteur de zéro %. 

### <a name="granular-access-control"></a>Contrôle d’accès granulaire

Utilisez le [contrôle d’accès en fonction du rôle Azure](../articles/active-directory/role-based-access-control-what-is.md) afin d’affecter à un ou plusieurs utilisateurs des autorisations spécifiques d’accès à un disque managé. Managed Disks expose différentes opérations, notamment la lecture, l’écriture (création/mise à jour), la suppression et la récupération d’un [URI de signature d’accès partagé](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) pour le disque. N’accordez l’accès qu’aux opérations dont une personne a besoin pour exécuter son travail. Par exemple, si vous voulez empêcher un utilisateur de copier un disque managé sur un compte de stockage, vous pouvez décider de lui interdire l’accès à l’action d’exportation sur ce disque managé. De la même manière, si vous voulez empêcher un utilisateur d’employer un URI de signature d’accès partagé pour copier un disque managé, vous pouvez décider de ne pas lui octroyer l’autorisation d’accès à ce disque managé.

### <a name="azure-backup-service-support"></a>Prise en charge du service Azure Backup
Utilisez le service Azure Backup avec Managed Disks pour créer une tâche de sauvegarde avec des sauvegardes périodiques, une restauration facile des machines virtuelles et des stratégies de rétention de sauvegarde. Les disques gérés prennent uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication. Ainsi, trois copies des données sont conservées dans une même région. Pour la récupération après sinistre régionale, vous devez sauvegarder vos disques de machines virtuelles dans une autre région à l’aide du [service Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) et d’un compte de stockage GRS comme archivage de sauvegarde. Actuellement, Azure Backup prend en charge des tailles de disque de données jusqu'à 1 To pour la sauvegarde. Pour en savoir plus sur ce point, consultez [Utilisation du service Sauvegarde Azure pour les machines virtuelles avec Managed Disks](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Tarification et facturation

Lorsque vous utilisez Managed Disks, les considérations de facturation suivantes s’appliquent :
* Type de stockage

* Taille du disque

* Nombre de transactions

* Transferts de données sortantes

* Captures instantanées des disques managés (copie intégrale du disque)

Examinons ces éléments de plus près.

**Type de stockage :** Managed Disks propose 2 niveaux de performance : [Premium](../articles/virtual-machines/windows/premium-storage.md) (disques SSD) et [Standard](../articles/virtual-machines/windows/standard-storage.md) (disque dur). La facturation d’un disque managé dépend du type de stockage sélectionné.


**Taille de disque** : la facturation des disques managés dépend de leur taille configurée. Azure mappe la taille configurée des disques (arrondie à la valeur supérieure) sur l’option Managed Disks la plus proche, tel qu’indiqué dans les tableaux ci-dessous. Chaque disque managé mappe sur l’une des tailles configurées prises en charge et est facturé en conséquence. Par exemple, si vous créez un disque managé standard et définissez une taille configurée de 200 Go, vous êtes facturé selon la tarification du disque S20.

Voici les tailles de disque disponible pour un disque managé Premium :

| **Disques managés<br> Premium** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Taille du disque        | 32 Gio   | 64 Gio   | 128 Go  | 256 Gio  | 512 Go  | 1 024 Gio (1 Tio) | 2 048 Gio (2 Tio) | 4 095 Gio (4 Tio) | 


Voici les tailles de disque disponible pour un disque managé Standard :

| **Disques managés<br> Standard** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Taille du disque        | 32 Gio   | 64 Gio   | 128 Go | 512 Go | 1 024 Gio (1 Tio) | 2 048 Gio (2 Tio) | 4 095 Gio (4 Tio) | 


**Nombre de transactions** : vous êtes facturé pour le nombre de transactions effectuées sur un disque managé standard. Les transactions associées aux disques managés Premium ne sont pas facturées.

**Transferts de données sortantes**: les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

Pour plus d’informations sur la tarification d’Azure Managed Disks, consultez la page [Managed Disks Tarification](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Captures instantanées de disque managé

Une capture instantanée est une copie en lecture seule d’un disque géré qui est stockée comme un disque géré standard par défaut. Avec des captures instantanées, vous pouvez sauvegarder vos disques managés à tout moment dans le temps. Ces captures instantanées existent indépendamment du disque source et peuvent être utilisées pour créer des disques managés par la suite. Elles sont facturées en fonction de la taille utilisée. Par exemple, si vous créez une capture instantanée d’un disque managé avec une capacité approvisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio, la capture instantanée sera facturée uniquement pour la taille des données utilisées de 10 Gio.  

Pour le moment, les [captures instantanées incrémentielles](../articles/virtual-machines/windows/incremental-snapshots.md) ne sont pas prises en charge pour les disques managés, mais elles le seront dans le futur.

Pour en savoir plus sur la création de captures instantanées avec Managed Disks, consultez ces ressources :

* [Créer une copie d’un disque dur virtuel stocké en tant que disque managé à l’aide de la fonction Instantanés dans Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Créer une copie d’un disque dur virtuel stocké en tant que disque managé à l’aide de la fonction Instantanés dans Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Images

Managed Disks prend également en charge la création d’une image personnalisée gérée. Vous pouvez créer une image à partir de votre disque dur virtuel (VHD) personnalisé dans un compte de stockage ou directement à partir d’une machine virtuelle généralisée (préparée à l’aide de Sysprep). L’ensemble des disques managés associés à une machine virtuelle sont capturés dans une seule image. Il s’agit notamment des disques de données et des disques de système d’exploitation. Dès lors, vous pouvez créer des centaines de machines virtuelles à l’aide de votre image personnalisée, sans qu’il ne soit nécessaire de copier ou de gérer aucun compte de stockage.

Pour plus d’informations sur la création des images, consultez les articles suivants :
* [Procédure de capture d’une image managée d’une machine virtuelle généralisée dans Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Guide pratique de généralisation et de capture d’une machine virtuelle Linux avec Azure CLI 2.0](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Images et captures instantanées

Souvent, le mot « image » est associé aux machines virtuelles. C’est désormais aussi le cas du terme « captures instantanées ». Il est important de bien saisir la différence entre les deux. Managed Disks vous permet de saisir une image d’une machine virtuelle généralisée qui a été libérée. Cette image comprend l’ensemble des disques attachés à la machine virtuelle. Vous pouvez l’utiliser pour créer une autre machine virtuelle, qui le cas échéant intégrera l’ensemble des disques.

Une capture instantanée est une copie d’un disque à un instant t. Elle s’applique uniquement à un disque. Si vous possédez une machine virtuelle qui présente uniquement un disque (le système d’exploitation), vous pouvez en saisir une capture instantanée ou une image, à partir desquelles vous créez une machine virtuelle.

Que se passe-t-il une machine virtuelle comprend cinq disques agrégés par bande ? Vous pouvez saisir une capture instantanée de chacun des disques, mais l’état des disques n’est pas connu au sein de la machine virtuelle. Les captures ne sont relatives qu’à leur disque respectif. Dans ce scénario, il faudrait coordonner les captures instantanées ; cette fonctionnalité n’est pas actuellement prise en charge.

## <a name="managed-disks-and-encryption"></a>Disques gérés et chiffrement

Il y a deux types de chiffrement à aborder dans le cas des disques gérés. Le premier est le chiffrement SSE (Storage Service Encryption), qui est effectué par le service de stockage. Le second est un chiffrement Azure Disk Encryption, que vous pouvez activer sur les disques de système d’exploitation et de données pour vos machines virtuelles.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Le [Chiffrement du service de stockage Azure](../articles/storage/common/storage-service-encryption.md) assure le chiffrement au repos et la protection de vos données pour assurer le respect des engagements de votre organisation en matière de sécurité et de conformité. Le Chiffrement du service de stockage est activé par défaut pour l’ensemble des disques gérés, captures instantanées et images dans toutes les régions où des disques gérés sont disponibles. Depuis le 10 juin 2017, l’ensemble des nouveaux disques gérés, captures instantanées et images, ainsi que les nouvelles données écrites sur des disques gérés existants, sont automatiquement chiffrés au repos à l’aide de clés gérées par Microsoft.  Pour plus d’informations, voir la [page du FAQ sur les disques gérés](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption).


### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption vous permet de chiffrer les disques de données et de système d’exploitation utilisés par une machine virtuelle IaaS. Cela inclut les disques gérés. Sur Windows, les disques sont chiffrés à l’aide de la technologie de chiffrement BitLocker standard. Sur Linux, les disques sont chiffrés à l’aide de la technologie DM-Crypt. La fonctionnalité est intégrée à Azure Key Vault pour vous permettre de contrôler et gérer les clés de chiffrement de disque. Pour plus d’informations, voir [Azure Disk Encryption pour machines virtuelles Windows et Linux IaaS](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Managed Disks, consultez les articles suivants.

### <a name="get-started-with-managed-disks"></a>Bien démarrer avec Managed Disks

* [Créer une machine virtuelle à l’aide de Resource Manager et de PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Création d’une machine virtuelle Linux avec Azure CLI 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Attacher un disque de données géré à une machine virtuelle Windows à l’aide de PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Ajouter un disque géré à une machine virtuelle Linux](../articles/virtual-machines/linux/add-disk.md)

* [Disques managés - Exemples de scripts PowerShell](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Utiliser des disques gérés dans les modèles Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Comparer les options de stockage Managed Disks

* [Stockage Premium et disques](../articles/virtual-machines/windows/premium-storage.md)

* [Stockage Standard et disques](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Instructions d’utilisation

* [Migrer de AWS et d’autres plates-formes vers Managed Disks dans Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Migrer des machines virtuelles Azure vers Managed Disks dans Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
