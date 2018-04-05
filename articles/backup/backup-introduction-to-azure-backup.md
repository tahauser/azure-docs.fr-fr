---
title: Qu’est-ce qu’Azure Backup ? | Microsoft Docs
description: Utilisez la sauvegarde Azure pour sauvegarder et restaurer les données et les charges de travail depuis les serveurs Windows Server, les stations de travail Windows, les serveurs System Center DPM et les machines virtuelles Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: sauvegarde et restauration ; services de restauration ; solutions de sauvegarde
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 3/1/2018
ms.author: markgal;trinadhk;anuragm
ms.custom: mvc
ms.openlocfilehash: 00ed2a64c672e1d2ae9a0037905a544b6c4424b7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="overview-of-the-features-in-azure-backup"></a>Vue d’ensemble des fonctionnalités de sauvegarde Azure
Azure Backup est le service Azure qui vous permet de sauvegarder (ou de protéger) et de restaurer vos données dans le cloud Microsoft. Azure Backup remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Azure Backup propose plusieurs composants que vous pouvez télécharger et déployer sur l’ordinateur ou sur le serveur approprié, ou dans le cloud. Vous déployez un composant (ou un agent) en fonction de ce que vous souhaitez protéger. Vous pouvez utiliser tous les composants de Sauvegarde Azure (que vous protégiez des données en local ou dans le cloud) pour sauvegarder des données dans un coffre Recovery Services d’Azure. Pour plus d’informations sur le composant à utiliser pour protéger des données, des applications ou des charges de travail spécifiques, consultez le [tableau des composants Azure Backup](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (plus loin dans cet article).

[Regarder une vidéo de présentation d’Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>Pourquoi utiliser Azure Backup ?
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison ou une destination de stockage statique similaire aux disques ou bandes. Bien que simple, cette approche est limitée et ne tire pas pleinement parti d’une plateforme de cloud sous-jacente, ce qui se traduit par une solution coûteuse et inefficace. Certaines solutions sont coûteuses, car elles vous obligent à payer un type de stockage inapproprié ou une capacité de stockage inutile. D’autres encore sont inefficaces, car elles ne fournissent pas le type ou la quantité de stockage dont vous avez besoin, ou les tâches administratives prennent beaucoup trop de temps. Par opposition, Azure Backup offre les principaux avantages suivants :

**Gestion automatique du stockage** : les environnements hybrides impliquent souvent un stockage hétérogène (une partie en local et une autre dans le cloud). Avec Azure Backup, l’utilisation d’appareils de stockage locaux ne génère aucun coût. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. Avec le paiement à l’utilisation, vous payez uniquement le stockage que vous utilisez. Pour plus d’informations, consultez [l’article sur les tarifs Azure](https://azure.microsoft.com/pricing/details/backup).

**Mise à l’échelle illimitée** : Azure Backup utilise la puissance et l’échelle illimitée du cloud Azure pour garantir la haute disponibilité, sans supplément de maintenance ou de surveillance. Vous pouvez configurer des alertes pour fournir des informations sur les événements, mais vous n’avez pas à vous soucier de la haute disponibilité de vos données dans le cloud.

**Diverses options de stockage** : la réplication du stockage est l’un des facteurs de haute disponibilité. La solution Sauvegarde Azure propose deux types de réplications : le [stockage localement redondant](../storage/common/storage-redundancy-lrs.md) et le [stockage géoredondant](../storage/common/storage-redundancy-grs.md). Choisissez l’option de stockage de sauvegarde en fonction de vos besoins :

* Le stockage localement redondant (LRS) réplique vos données trois fois (il crée trois copies de vos données) dans une unité d’échelle de stockage dans un centre de données. Toutes les copies des données existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données contre les défaillances matérielles locales.

* Le stockage géo-redondant (GRS) est l’option de réplication recommandée par défaut. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité des données supérieure, même en cas de panne au niveau régional.

**Transfert de données illimité** : Azure Backup ne limite pas la quantité de données entrantes ou sortantes transférées. Par ailleurs, les données transférées ne sont pas facturées par Azure Backup. Toutefois, si vous utilisez le service Azure Import/Export pour importer de grandes quantités de données, les données entrantes ont un coût. Pour plus d’informations sur ce coût, consultez [Flux de travail de la sauvegarde hors connexion dans Azure Backup](backup-azure-backup-import-export.md). Les données sortantes sont les données transférées à partir d’un coffre Recovery Services pendant une opération de restauration.

**Chiffrement des données** : le chiffrement des données garantit une transmission et un stockage sécurisés de vos données dans le cloud public. La phrase secrète de chiffrement est stockée en local et n’est jamais transmise ou stockée dans Azure. Si vous avez besoin de restaurer des données, vous êtes le seul à disposer de la phrase secrète de chiffrement ou de la clé.

**Sauvegarde cohérente avec les applications** : une sauvegarde cohérente avec les applications signifie qu’un point de récupération dispose de toutes les données requises pour restaurer la copie de sauvegarde. Sauvegarde Microsoft Azure fournit des sauvegardes cohérentes avec les applications, qui garantissent qu’aucun correctif supplémentaire n’est requis pour restaurer les données. La restauration de données cohérentes avec les applications réduit le délai de restauration, ce qui permet de rétablir rapidement le fonctionnement normal.

**Rétention à long terme** : vous pouvez utiliser les coffres de Recovery Services pour la conservation de données à court terme et à long terme. Azure ne limite pas la durée de conservation des données dans un coffre Recovery Services. Vous pouvez conserver des données dans un coffre aussi longtemps que vous le souhaitez. Azure Backup est limité à 9 999 points de récupération par instance protégée. Consultez la section [Sauvegarde et rétention](backup-introduction-to-azure-backup.md#backup-and-retention) dans cet article pour savoir comment cette limite peut avoir un impact sur vos besoins de sauvegarde.

## <a name="which-azure-backup-components-should-i-use"></a>Quels composants Azure Backup dois-je utiliser ?
Consultez le tableau suivant qui explique ce que vous pouvez protéger avec chaque composant de Sauvegarde Microsoft Azure. 

| Composant | Avantages | limites | Qu’est-ce qui est protégé ? | Où sont stockées les sauvegardes ? |
| --- | --- | --- | --- | --- |
| Agent Azure Backup (MARS) |<li>Sauvegarde des fichiers et des dossiers sur un système d’exploitation Windows physique ou virtuel (les machines virtuelles peuvent être locales ou dans Azure)<li>Aucun serveur de sauvegarde distinct n’est requis. |<li>Sauvegarde 3 fois par jour <li>Ne tient pas compte des applications ; restauration au niveau du fichier, du dossier et du volume seulement, <li>  Linux non pris en charge. |<li>Fichiers, <li>Dossiers, <li>État du système |Coffre Recovery Services |
| System Center DPM |<li>Instantanés tenant compte des applications (VSS)<li>Flexibilité totale concernant le moment d’exécution de la sauvegarde<li>Granularité de récupération (tout)<li>Possibilité d’utiliser un coffre Recovery Services<li>Prise en charge de Linux sur machines virtuelles Hyper-V et VMware <li>Sauvegarder et restaurer des machines virtuelles VMware à l’aide de DPM 2012 R2 |Impossible de sauvegarder des charges de travail Oracle.|<li>Fichiers, <li>Dossiers,<li> Volumes, <li>Machines virtuelles,<li> Applications,<li> Charges de travail <li>État du système |<li>Coffre Recovery Services,<li> Disque connecté localement,<li>  Bande (locale uniquement) |
| Azure Backup Server |<li>Instantanés tenant compte des applications (VSS)<li>Flexibilité totale concernant le moment d’exécution de la sauvegarde<li>Granularité de récupération (tout)<li>Possibilité d’utiliser un coffre Recovery Services<li>Prise en charge de Linux sur machines virtuelles Hyper-V et VMware<li>Sauvegarder et restaurer des machines virtuelles VMWare <li>Ne nécessite pas de licence System Center |<li>Impossible de sauvegarder des charges de travail Oracle.<li>Requiert toujours un abonnement Azure en direct<li>Aucune prise en charge de la sauvegarde sur bande |<li>Fichiers, <li>Dossiers,<li> Volumes, <li>Machines virtuelles,<li> Applications,<li> Charges de travail, <li>État du système |<li>Coffre Recovery Services,<li> Disque connecté localement |
| Sauvegarde des machines virtuelles IaaS Azure |<li>Instantanés tenant compte des applications (VSS)<li>Sauvegardes natives pour Windows/Linux<li>Aucune installation spécifique d’agent n’est requise<li>Sauvegarde au niveau structure sans nécessiter d’infrastructure de sauvegarde |<li>Sauvegarde des machines virtuelles une fois par jour <li>Restauration des machines virtuelles uniquement au niveau du disque<li>Impossible d’effectuer une sauvegarde en local |<li>Machines virtuelles, <li>Tous les disques (à l’aide de PowerShell) |<p>Coffre Recovery Services</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>Quels sont les scénarios de déploiement de chaque composant ?
| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge |
| --- | --- | --- | --- |
| Agent Azure Backup (MARS) |<p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server exécutée dans Azure.</p> |<p>**Oui**</p> <p>L’agent Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server.</p> |<p>Coffre Recovery Services</p> |
| System Center DPM |<p>**Oui**</p><p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide de System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Oui**</p> <p>Apprenez-en davantage sur [la protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/system-center-docs/dpm/data-protection-manager).</p> |<p>Disque connecté localement,</p> <p>Coffre Recovery Services,</p> <p>Bande (locale uniquement)</p> |
| Azure Backup Server |<p>**Oui**</p><p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>**Oui**</p> <p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>Disque connecté localement,</p> <p>Coffre Recovery Services</p> |
| Sauvegarde des machines virtuelles IaaS Azure |<p>**Oui**</p><p>Partie de la structure Azure</p><p>Spécialisé dans la [sauvegarde des machines virtuelles Azure IaaS (infrastructure en tant que service)](backup-azure-vms-introduction.md).</p> |<p>**Non**</p> <p>Utilisez System Center DPM pour sauvegarder des machines virtuelles dans votre centre de données.</p> |<p>Coffre Recovery Services</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>Quelles applications et charges de travail est-il possible de sauvegarder ?
Le tableau suivant fournit une matrice des données et des charges de travail pouvant être protégées à l’aide d’Azure Backup. La colonne Solution Azure Backup contient des liens vers la documentation de déploiement de cette solution. 

| Données ou charge de travail | Environnement source | Solution Azure Backup |
| --- | --- | --- |
| Fichiers et dossiers |Windows Server |<p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Fichiers et dossiers |Ordinateur Windows |<p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle VMware |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machines virtuelles IaaS Azure (Windows) |exécution dans Azure |[Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |
| Machines virtuelles IaaS Azure (Linux) |exécution dans Azure |[Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Prise en charge de Linux
Le tableau suivant montre les composants Azure Backup prenant en charge Linux.  

| Composant | Prise en charge Linux (approuvée par Azure) |
| --- | --- |
| Agent Azure Backup (MARS) |Aucun (agent uniquement sur Windows) |
| System Center DPM |<li> Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/> <li> Restauration de machines virtuelles invitées Linux Hyper-V et VMware </br> </br>  *Sauvegarde cohérente des fichiers non disponible pour la machine virtuelle Azure* <br/> |
| Azure Backup Server |<li>Sauvegarde cohérente au niveau fichier de machines virtuelles invitées Linux sur Hyper-V et VMware<br/> <li> Restauration de machines virtuelles invitées Linux Hyper-V et VMware </br></br> *Sauvegarde cohérente des fichiers non disponible pour la machine virtuelle Azure*  |
| Sauvegarde des machines virtuelles IaaS Azure |Sauvegarde cohérente au niveau application à l’aide de [l’infrastructure pré et post-script](backup-azure-linux-app-consistent.md)<br/> [Récupération de fichiers granulaire](backup-azure-restore-files-from-vm.md)<br/> [Restaurer tous les disques de machines virtuelles](backup-azure-arm-restore-vms.md#restore-backed-up-disks)<br/> [Restauration de machines virtuelles](backup-azure-arm-restore-vms.md#create-a-new-vm-from-a-restore-point) |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Utilisation des machines virtuelles Premium Storage avec Azure Backup
Azure Backup protège les machines virtuelles Premium Storage. Stockage Premium Azure est un stockage SSD conçu pour supporter des charges de travail nécessitant de nombreuses E/S. Stockage Premium est intéressant pour les charges de travail des machines virtuelles. Pour plus d’informations sur le stockage Premium, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../virtual-machines/windows/premium-storage.md).

### <a name="back-up-premium-storage-vms"></a>Sauvegarder des machines virtuelles Premium Storage
Au moment de sauvegarder des machines virtuelles de stockage Premium, le service de sauvegarde crée un emplacement temporaire intermédiaire appelé « AzureBackup- » dans le compte Stockage Premium. La taille de l’emplacement intermédiaire est égale à la taille de la capture instantanée de point de récupération. Vérifiez que le compte de stockage Premium dispose d’un espace libre suffisant pour prendre en compte cet emplacement intermédiaire temporaire. Pour plus d’informations, consultez l’article [Limites du stockage Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets). Une fois la sauvegarde terminée, l'emplacement intermédiaire est supprimé. Le prix du stockage utilisé pour l’emplacement intermédiaire est conforme à l’ensemble de la [tarification de Premium Storage](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

> [!NOTE]
> Évitez de modifier l'emplacement intermédiaire.
>
>

### <a name="restore-premium-storage-vms"></a>Restaurer des machines virtuelles Premium Storage
Les machines virtuelles Stockage Premium peuvent être restaurées vers Stockage Premium ou Stockage Standard. La restauration d’un point de récupération de machines virtuelles Stockage Premium dans Stockage Premium est le processus classique. Toutefois, il peut être rentable de restaurer un point de récupération de machines virtuelles Stockage Premium vers Stockage Standard si vous avez besoin d’un sous-ensemble de fichiers de la machine virtuelle.

## <a name="using-managed-disk-vms-with-azure-backup"></a>Utilisation des machines virtuelles de disque géré avec Azure Backup
Azure Backup protège les machines virtuelles de disque géré. Les disques gérés vous libèrent de la gestion des comptes de stockage des machines virtuelles et simplifient considérablement l’approvisionnement des machines virtuelles.

### <a name="back-up-managed-disk-vms"></a>Sauvegarder des machines virtuelles de disque géré
La sauvegarde des machines virtuelles sur des disques gérés est identique à la sauvegarde des machines virtuelles de Resource Manager. Dans le portail Azure, vous pouvez configurer le travail de sauvegarde directement à partir de la vue de la machine virtuelle ou de la vue du coffre Recovery Services. Vous pouvez sauvegarder des machines virtuelles sur des disques gérés par le biais des collections RestorePoint basées sur des disques gérés. Azure Backup prend également en charge la sauvegarde des machines virtuelles de disque géré chiffrées avec Azure Disk Encryption (ADE).

### <a name="restore-managed-disk-vms"></a>Restaurer des machines virtuelles de disque géré
Sauvegarde Azure vous permet de restaurer une machine virtuelle complète avec des disques gérés ou de restaurer des disques gérés sur un compte de stockage. Azure gère les disques gérés pendant le processus de restauration. Vous (le client) gérez le compte de stockage créé dans le cadre du processus de restauration. Lorsque vous restaurez des machines virtuelles chiffrées gérées, les clés et secrets correspondants doivent figurer dans le coffre de clés avant de procéder à la restauration.

## <a name="what-are-the-features-of-each-backup-component"></a>Quelles sont les fonctionnalités de chaque composant Azure Backup ?
Les sections suivantes comportent des tableaux qui résument la disponibilité ou la prise en charge de diverses fonctionnalités dans chaque composant Azure Backup. Pour un support ou des détails supplémentaires, consultez les informations après chaque tableau.

### <a name="storage"></a>Stockage
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Coffre Recovery Services |![OUI][green] |![OUI][green] |![OUI][green] |![OUI][green] |
| Stockage sur disque | |![OUI][green] |![OUI][green] | |
| Stockage sur bande | |![OUI][green] | | |
| Compression <br/>(dans un coffre Recovery Services) |![OUI][green] |![OUI][green] |![OUI][green] | |
| Sauvegarde incrémentielle |![OUI][green] |![OUI][green] |![OUI][green] |![OUI][green] |
| Déduplication de disque | |![Partiellement][yellow] |![Partiellement][yellow] | | |

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

Le coffre Recovery Services est la cible de stockage par défaut sur tous les composants. System Center DPM et le serveur de sauvegarde Azure offrent également la possibilité de disposer d’une copie du disque local. Toutefois, seul System Center DPM permet d’écrire des données sur un périphérique de stockage sur bande.

#### <a name="compression"></a>Compression
En outre, les sauvegardes sont compressées afin de réduire la quantité d’espace de stockage requise. L’extension de machine virtuelle est le seul composant qui n’effectue aucune compression. L’extension de machine virtuelle copie toutes les données de sauvegarde de votre compte de stockage vers le coffre Recovery Services dans la même région. Aucune compression n’est effectuée lors du transfert des données. Le transfert des données sans compression augmente légèrement le stockage utilisé. Toutefois, le stockage des données sans compression réduit les délais de restauration au cas où vous auriez besoin d’utiliser ce point de récupération.


#### <a name="disk-deduplication"></a>Déduplication de disque
Vous pouvez tirer parti de la déduplication lorsque vous déployez System Center DPM ou le serveur de sauvegarde Azure [sur une machine virtuelle Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Windows Server effectue la déduplication (au niveau de l’hôte) sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle.

> [!NOTE]
> La déduplication n’est pas disponible dans Azure pour aucun des composants Azure Backup. Lorsque System Center DPM et Azure Backup Server sont déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peuvent pas être dédupliqués.
>
>

### <a name="incremental-backup-explained"></a>Explication de la sauvegarde incrémentielle
Chaque composant de Sauvegarde Azure prend en charge la sauvegarde incrémentielle quel que soit le stockage cible (disque, bande, coffre Recovery Services). La sauvegarde incrémentielle garantit un stockage efficace des sauvegardes, en transférant uniquement les modifications apportées depuis la dernière sauvegarde.

#### <a name="comparing-full-differential-and-incremental-backup"></a>Comparaison entre la sauvegarde complète, différentielle et incrémentielle

La consommation du stockage, l’objectif de délai de récupération (RTO) et la consommation réseau varient pour chaque type de méthode de sauvegarde. Afin de réduire le coût total de possession (TCO) de la sauvegarde, vous devez comprendre comment choisir la meilleure solution de sauvegarde. L’illustration suivante compare la sauvegarde complète, la sauvegarde différentielle et la sauvegarde incrémentielle. Dans l’image, la source de données A est composée de 10 blocs de stockage A1-A10, qui sont sauvegardés tous les mois. Les blocs A2, A3, A4 et A9 ont changé lors du premier mois et le bloc A5 a changé lors du mois suivant.

![illustration montrant la comparaison des méthodes de sauvegarde](./media/backup-introduction-to-azure-backup/backup-method-comparison.png)

Avec la **sauvegarde complète**, chaque copie de sauvegarde contient la source de données entière. La sauvegarde complète consomme une grande quantité de bande passante réseau et de stockage, à chaque fois qu’une copie de sauvegarde est transférée.

La **sauvegarde différentielle** stocke uniquement les blocs qui ont changé depuis la sauvegarde complète initiale, ce qui entraîne une quantité réduite de la consommation de stockage et de réseau. Les sauvegardes différentielles ne conservent pas de copies redondantes des données inchangées. Toutefois, étant donné que les blocs de données inchangés entre des sauvegardes successives sont transférés et stockés, les sauvegardes différentielles sont inefficaces. Lors du deuxième mois, les blocs A2, A3, A4 et A9 qui ont changé sont sauvegardés. Lors du troisième mois, ces mêmes blocs sont à nouveau sauvegardés, ainsi que le bloc A5 qui a changé. Les blocs modifiés continuent d’être sauvegardés jusqu’à la prochaine sauvegarde complète.

La **sauvegarde incrémentielle** permet d’obtenir une efficacité élevée en matière de stockage et de réseau en stockant uniquement les blocs de données modifiés depuis la dernière sauvegarde. Avec une sauvegarde incrémentielle, il est inutile d’effectuer des sauvegardes complètes régulières. Dans l’exemple, après la sauvegarde complète effectuée pour le premier mois, les blocs A2, A3, A4 et A9 sont marqués comme modifiés et transférés pour le deuxième mois. Lors du troisième mois, seul le bloc A5 qui a changé est marqué et transféré. Le fait de déplacer moins de données permet d’économiser des ressources de réseau et de stockage, ce qui réduit le coût total de possession.

### <a name="security"></a>Sécurité
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Sécurité du réseau<br/> (vers Azure) |![OUI][green] |![OUI][green] |![OUI][green] |![Partiellement][yellow] |
| Sécurité des données<br/> (dans Azure) |![OUI][green] |![OUI][green] |![OUI][green] |![Partiellement][yellow] |

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Sécurité du réseau
L’ensemble du trafic de sauvegarde entre vos serveurs et le coffre Recovery Services est chiffré à l’aide du protocole AES (Advanced Encryption Standard) 256. Les données de sauvegarde sont envoyées via une connexion HTTPS sécurisée. Les données de sauvegarde sont également stockées dans le coffre Recovery Services sous une forme chiffrée. Vous êtes le seul, en tant que client Azure, à connaître la phrase secrète pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

> [!WARNING]
> Une fois le coffre Recovery Services établi, vous êtes le seul à avoir accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de votre clé de chiffrement et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.
>
>

#### <a name="data-security"></a>Sécurité des données
La sauvegarde des machines virtuelles Azure exige la configuration du chiffrement *dans* la machine virtuelle. Utilisez BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux. Azure Backup ne chiffre pas automatiquement les données de sauvegarde en provenance de ce chemin d’accès.

### <a name="network"></a>Réseau
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Compression réseau <br/>(vers le **serveur de sauvegarde**) | |![OUI][green] |![OUI][green] | |
| Compression réseau <br/>(vers le **coffre Recovery Services**) |![OUI][green] |![OUI][green] |![OUI][green] | |
| Protocole réseau <br/>(vers le **serveur de sauvegarde**) | |TCP |TCP | |
| Protocole réseau <br/>(vers le **coffre Recovery Services**) |HTTPS |HTTPS |HTTPS |HTTPS |

![clé de table](./media/backup-introduction-to-azure-backup/table-key-2.png)

L’extension de machine virtuelle lit directement les données à partir du compte de stockage Azure via le réseau de stockage. Il n’est donc pas nécessaire de compresser ce trafic.

Si vous utilisez un serveur System Center DPM ou un serveur de sauvegarde Azure comme serveur de sauvegarde secondaire, compressez les données transférées du serveur principal vers le serveur de sauvegarde. Le fait de compresser les données avant de les sauvegarder dans DPM ou dans le serveur de sauvegarde Azure permet d’économiser de la bande passante.

#### <a name="network-throttling"></a>Limitation du réseau
L’agent Azure Backup assure une limitation du réseau qui permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cette limitation peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

## <a name="backup-and-retention"></a>Sauvegarde et rétention

La solution Sauvegarde Azure présente une limite de 9 999 points de récupération, également appelés copies ou instantanés de sauvegarde, par *instance protégée*. Une instance protégée est un ordinateur, un serveur (physique ou virtuel) ou une charge de travail configurés pour sauvegarder des données dans Azure. Pour plus d’informations, consultez la section [Qu’est-ce qu’une instance protégée ?](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Une instance est protégée une fois qu’une copie de sauvegarde des données a été enregistrée. La copie de sauvegarde des données constitue la protection. Si les données sources sont perdues ou endommagées, la copie de sauvegarde peut les restaurer. Le tableau ci-après indique la fréquence de sauvegarde maximale pour chaque composant. Votre configuration de votre stratégie de sauvegarde détermine la rapidité avec laquelle vous consommez les points de récupération. Par exemple, si vous créez un point de récupération chaque jour, vous pouvez conserver les points de récupération pendant 27 ans avant d’en manquer. Si vous optez pour un point de récupération par mois, vous pouvez conserver les points de récupération pendant 833 ans avant d’en manquer. Le service de sauvegarde ne définit pas de délai d’expiration pour un point de récupération.

|  | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Fréquence de sauvegarde<br/> (vers un coffre Recovery Services) |Trois sauvegardes par jour |Deux sauvegardes par jour |Deux sauvegardes par jour |Une sauvegarde par jour |
| Fréquence de sauvegarde<br/> (vers le disque) |Non applicable |<li>Toutes les 15 minutes pour SQL Server <li>Toutes les heures pour les autres charges de travail |<li>Toutes les 15 minutes pour SQL Server <li>Toutes les heures pour les autres charges de travail</p> |Non applicable |
| Options de rétention |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |
| Nombre maximal de points de récupération par instance protégée |9 999|9 999|9 999|9 999|
| Période de rétention maximale |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |
| Points de récupération sur le disque local |Non applicable |<li>64 pour les serveurs de fichiers,<li>448 pour les serveurs d’applications |<li>64 pour les serveurs de fichiers,<li>448 pour les serveurs d’applications |Non applicable |
| Points de récupération sur bande |Non applicable |Illimité |Non applicable |Non applicable |

## <a name="what-is-a-protected-instance"></a>Qu’est-ce qu’une instance protégée ?
Une instance protégée est une référence générique à un ordinateur Windows, à un serveur (physique ou virtuel) ou à une base de données SQL qui ont été configurés pour sauvegarder des données dans Azure. Une instance est protégée une fois que vous configurez une stratégie de sauvegarde pour l’ordinateur, le serveur ou la base de données et que vous créez une copie de sauvegarde des données. Les copies ultérieures des données de sauvegarde pour cette instance protégée (qui sont appelées points de récupération), augmentent la quantité de stockage consommée. Vous pouvez créer jusqu’à 9 999 points de récupération pour une instance protégée. Si vous supprimez un point de récupération du stockage, ce point n’entre pas dans le total des 9 999 points de récupération.
Parmi les exemples d’instances protégées, citons les machines virtuelles, les serveurs d’applications, les bases de données et les ordinateurs personnels exécutant le système d’exploitation Windows. Par exemple : 

* Machine virtuelle exécutant la structure d’hyperviseur Hyper-V ou Azure IaaS. Les systèmes d’exploitation invités de cette machine virtuelle peuvent être Windows Server ou Linux.
* Serveur d’applications : le serveur d’applications peut être une machine physique ou virtuelle exécutant Windows Server et des charges de travail impliquant des données à sauvegarder. Les charges de travail courantes sont Microsoft SQL Server, Microsoft Exchange Server, Microsoft SharePoint Server et le rôle Serveur de fichiers sur Windows Server. Pour sauvegarder ces charges de travail, vous avez besoin de System Center Data Protection Manager (DPM) ou du serveur de sauvegarde Azure.
* Un ordinateur personnel, une station de travail ou un ordinateur portable exécutant le système d’exploitation Windows.


## <a name="what-is-a-recovery-services-vault"></a>Présentation d’un coffre Recovery Services
Un coffre Recovery Services est une entité de stockage en ligne dans Azure qui permet de conserver les données telles que les copies de sauvegarde, les points de récupération et les stratégies de sauvegarde. Vous pouvez utiliser les coffres Recovery Services pour conserver les données de sauvegarde pour les services Azure et les stations de travail et serveurs locaux. Les coffres Recovery Services facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion. Dans chaque abonnement Azure, vous pouvez créer jusqu’à 25 coffres Recovery Services par région Azure. Concernant le choix du lieu où stocker vos données, les régions présentent des différences. Consultez [Stockage géoredondant](../storage/common/storage-redundancy-grs.md) pour plus d’informations sur les appariements de régions et d’autres considérations sur le stockage.

Les coffres de sauvegarde, qui étaient basés sur Azure Service Manager, constituaient la première version des coffres. Les coffres Recovery Services, qui ajoutent les fonctionnalités du modèle Azure Resource Manager, en constituent la seconde version. Pour une description complète des différences de fonctionnalités, consultez [l’article de présentation du coffre Recovery Services](backup-azure-recovery-services-vault-overview.md). Vous ne pouvez plus créer de coffres de sauvegarde, et tous les archivages de sauvegarde existants ont été mis à niveau vers les coffres Recovery Services. Vous pouvez utiliser le portail Azure pour gérer les coffres mis à niveau vers les coffres Recovery Services.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>Quelle est la différence entre Azure Backup et Azure Site Recovery ?
La sauvegarde Azure Backup et Azure Site Recovery sont liés dans la mesure où les deux services sauvegardent les données et peuvent les restaurer. Toutefois, ces services ont des objectifs différents en assurant la continuité d’activité et la récupération d’urgence dans votre entreprise. Utilisez Sauvegarde Azure pour protéger et restaurer les données à un niveau plus granulaire. Par exemple, si une présentation sur un ordinateur portable est endommagée, vous utilisez Sauvegarde Azure pour la restaurer. Si vous souhaitez répliquer la configuration et les données d’une machine virtuelle dans un autre centre de données, utilisez Azure Site Recovery.

Azure Backup protège les données en local et dans le cloud. Azure Site Recovery coordonne la réplication, le basculement et la restauration automatique des machines virtuelles et des serveurs physiques. Les deux services sont importants, car votre solution de récupération d’urgence doit protéger vos données et les rendre récupérables (Backup) *et* assurer la disponibilité de vos charges de travail (Site Recovery) en cas de panne.

Les concepts qui suivent vont vous aider à prendre des décisions importantes en matière de sauvegarde et de récupération.

| Concept | Détails | Sauvegarde | Récupération d’urgence |
| --- | --- | --- | --- |
| Objectif de point de récupération (RPO) |Quantité de perte de données acceptable si la récupération doit être exécutée. |Les solutions de sauvegarde offrent des RPO extrêmement variables. Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données. |Les solutions de récupération d’urgence ont un RPO faible. La copie de récupération d’urgence peut devoir être prête en seulement quelques secondes ou quelques minutes. |
| Objectif de délai de récupération (RTO) |Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. |Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe. |Les solutions de récupération d’urgence ont un RTO plus faible car elles sont davantage synchronisées avec la source et ont moins de modifications à traiter. |
| Rétention |Durée pendant laquelle les données doivent être stockées |Pour les scénarios qui exigent une reprise des opérations (altération des données, suppression accidentelle de fichiers, défaillances du système d’exploitation), les données de sauvegarde sont généralement conservées pendant 30 jours au maximum.<br>Du point de vue de la conformité, il se peut que vous deviez stocker les données pendant des mois, voire des années. Dans ce cas, les données de sauvegarde sont parfaitement adaptées aux besoins d’archivage. |Une récupération d’urgence porte uniquement sur les données de récupération opérationnelle, soit en général quelques heures, sans dépasser une journée. Puisque les solutions de récupération d’urgence sont conçues pour capturer les données à un niveau extrêmement précis, l’utilisation des données de récupération d’urgence n’est pas recommandée dans le cadre d’une rétention à long terme. |

## <a name="next-steps"></a>Étapes suivantes
Utilisez l’une des didacticiels suivants pour obtenir des instructions complètes détaillées pour protéger les données sur Windows Server, ou protéger une machine virtuelle (VM) dans Azure :

* [Sauvegarde des fichiers et dossiers](backup-try-azure-backup-in-10-mins.md)
* [Sauvegarde des machines virtuelles Azure](backup-azure-vms-first-look-arm.md)

Pour plus d’informations sur la protection des autres charges de travail, consultez l’un des articles suivants :

* [Sauvegarder Windows Server](backup-configure-vault.md)
* [Sauvegarder les charges de travail des applications](backup-azure-microsoft-azure-backup.md)
* [Sauvegarde des machines virtuelles IaaS Azure](backup-azure-arm-vms-prepare.md)

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
