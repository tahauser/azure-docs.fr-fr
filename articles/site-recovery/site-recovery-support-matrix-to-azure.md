---
title: "Matrice de support Azure Site Recovery pour la réplication vers Azure | Microsoft Docs"
description: "Résume les systèmes d’exploitation et composants pris en charge pour Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2018
ms.author: rajanaki
ms.openlocfilehash: a17d0918ea5938daf81c469fd6402a7dc9764831
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matrice de support Azure Site Recovery pour la réplication de machines virtuelles locales vers Azure


Cet article résume les composants et les configurations pris en charge pour Azure Site Recovery lors de la réplication et de la récupération vers Azure. Pour plus d’informations sur les conditions requises pour Azure Site Recovery, consultez la [configuration requise](site-recovery-prereq.md).

> [!NOTE]
> Veillez à effectuer la mise à jour vers la dernière version de l’agent et du fournisseur Site Recovery pour assurer la compatibilité avec les mises à jour dans la matrice de prise en charge.


## <a name="support-for-deployment-options"></a>Prise en charge des options de déploiement

**Déploiement** | **Serveur VMware/physique** | **Hyper-V (avec / sans  Virtual Machine Manager)** |
--- | --- | ---
**Portail Azure** | Machines virtuelles VMware locales vers stockage Azure, avec Azure Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques. | Machines virtuelles Hyper-V locales vers stockage Azure, avec Resource Manager ou le stockage et les réseaux classiques.<br/><br/> Basculez vers des machines virtuelles Resource Manager ou classiques.
**Portail Classic** | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Mode Maintenance uniquement.
**PowerShell** | Prise en charge | Prise en charge


## <a name="support-for-datacenter-management-servers"></a>Prise en charge des serveurs de gestion du centre de données

### <a name="virtualization-management-entities"></a>Entités de gestion de la virtualisation

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vCenter 6.5, 6.0 ou 5.5
**Hyper-V (avec VMM)** | System Center Virtual Machine Manager 2016 et System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Un cloud System Center Virtual Machine Manager 2016 qui combine des hôtes Windows Server 2016 et 2012 R2 n’est pas actuellement pris en charge.
  > Les configurations qui incluent la mise à niveau d’un SCVMM 2012 R2 existant vers la version 2016 ne sont pas prises en charge pour le moment.
### <a name="host-servers"></a>Serveurs hôtes

**Déploiement** | **Support**
--- | ---
**Machine virtuelle VMware/serveur physique** | vSphere 6.5, 6.0 ou 5.5
**Hyper-V (avec / sans Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour.<br></br>Si SCVMM est utilisé, les hôtes Windows Server 2016 doivent être gérés par SCVMM 2016.


  >[!Note]
  >Les sites Hyper-V qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge. La récupération vers un autre emplacement pour les machines virtuelles sur un hôte Windows Server 2016 n’est pas prise en charge pour le moment.

## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

Les machines virtuelles qui sont protégées doivent répondre aux [conditions requises pour Azure](#failed-over-azure-vm-requirements) lors de la réplication vers Azure.
Le tableau ci-dessous récapitule la prise en charge des systèmes d’exploitation répliqués dans différents scénarios de déploiement lors de l’utilisation d’Azure Site Recovery. Cette prise en charge est applicable pour toutes les charges de travail en cours d’exécution sur le système d’exploitation mentionné.

 **Serveur VMware/physique** | **Hyper-V (avec ou sans VMM)** |
--- | --- |
Windows Server 2016 64 bits (Server Core, Server avec Expérience utilisateur)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 avec au moins SP1<br/><br/> Red Hat Enterprise Linux : 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.4<br/><br/>CentOS : 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.4 <br/><br/>Serveur LTS Ubuntu 14.04[ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Serveur LTS Ubuntu 16.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(La mise à niveau des machines de réplication de SLES 11 SP3 vers SLES 11 SP4 n’est pas prise en charge. Si une machine répliquée a été mise à niveau, de SLES 11SP3 vers SLES 11 SP4, vous devez désactiver la réplication et protéger à nouveau la machine après la mise à niveau.) | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \* Windows Server 2016 Nano Server n’est pas pris en charge.
>
> Dans les distributions Linux, seuls les noyaux de stockage qui font partie de la version/mise à jour mineure de la distribution sont pris en charge.
>
> Les mises à niveau sur des versions majeures d’une distribution Linux sur une machine virtuelle VMware ou un serveur physique protégé par Azure Site Recovery ne sont pas prises en charge. Lors de la mise à niveau du système d’exploitation sur des versions majeures (par exemple, CentOS 6.\* vers CentOS 7.\*), désactivez la réplication pour la machine, mettez à niveau le système d’exploitation sur la machine, puis activez à nouveau la réplication.
>


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versions du noyau Ubuntu prises en charge pour les serveurs VMware/physiques

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic à 3.13.0-121-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic à 3.13.0-128-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-91-generic, |
14.04 LTS | 9.12 | 3.13.0-24-generic à 3.13.0-132-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic à 3.13.0-137-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-104-generic |
LTS 16.04 | 9.10 | 4.4.0-21-generic à 4.4.0-81-generic,<br/>4.8.0-34-generic à 4.8.0-56-generic,<br/>4.10.0-14-generic à 4.10.0-24-generic |
LTS 16.04 | 9.11 | 4.4.0-21-generic à 4.4.0-91-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-32-generic |
LTS 16.04 | 9.12 | 4.4.0-21-generic à 4.4.0-96-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-35-generic |
LTS 16.04 | 9.13 | 4.4.0-21-generic à 4.4.0-104-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Systèmes de fichiers pris en charge et configurations de stockage invité sous Linux (serveurs physiques / VMware)

Les systèmes de fichiers et le logiciel de configuration de stockage suivants sont pris en charge sur les serveurs Linux exécutés sur des serveurs VMware ou physiques :
* Systèmes de fichiers : ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS
* Gestionnaire de volume : LVM2
* Logiciel multichemin : Device Mapper

Les périphériques de stockage Paravirtualized (exportés par les pilotes paravirtualized) ne sont pas pris en charge.<br/>
Les unités de bloc d’entrée et de sortie en file d’attente ne sont pas prises en charge.<br/>
Les serveurs physiques avec le contrôleur de stockage HP CCISS ne sont pas pris en charge.<br/>

>[!Note]
> Sur les serveurs Linux, les répertoires suivants (s’ils sont configurés en tant que partitions/systèmes de fichiers séparés) doivent tous se trouver sur le même disque (le disque du système d’exploitation) sur le serveur source : / (racine), /boot, /usr, /usr/local, /var, /etc. ; /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.<br/><br/>
>


## <a name="support-for-network-configuration"></a>Prise en charge de la configuration réseau
Les tableaux suivants récapitulent la prise en charge de la configuration réseau dans différents scénarios de déploiement avec utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-network-configuration"></a>Configuration du réseau hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
Association de cartes réseau | OUI<br/><br/>Non pris en charge lorsque les machines physiques sont répliquées| OUI
VLAN | OUI | OUI
IPv4 | OUI | OUI
IPv6 | Non  | Non 

### <a name="guest-vm-network-configuration"></a>Configuration du réseau de machines virtuelles invitées

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
Association de cartes réseau | Non  | Non 
IPv4 | OUI | OUI
IPv6 | Non  | Non 
Adresse IP statique (Windows) | OUI | OUI
Adresse IP statique (Linux) | OUI <br/><br/>Les machines virtuelles sont configurées pour utiliser le protocole DHCP lors de la restauration automatique  | Non 
Plusieurs cartes réseau | OUI | OUI

### <a name="failed-over-azure-vm-network-configuration"></a>Configuration de réseau des machines virtuelles Azure basculées

**Mise en réseau Azure** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | OUI | OUI
ILB | OUI | OUI
ELB | OUI | OUI
Traffic Manager | OUI | OUI
Plusieurs cartes réseau | OUI | OUI
Adresse IP réservée | OUI | OUI
IPv4 | OUI | OUI
Conserver l’adresse IP source | OUI | OUI
Points de terminaison de service de réseau virtuel (Pare-feu et réseaux virtuels dans Stockage Azure ) | Non  | Non 


## <a name="support-for-storage"></a>Prise en charge du stockage
Les tableaux suivants récapitulent la prise en charge de la configuration du stockage dans différents scénarios de déploiement avec utilisation d’Azure Site Recovery pour répliquer vers Azure.

### <a name="host-storage-configuration"></a>Configuration du stockage hôte

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Oui pour VMware<br/><br/> Non pour les serveurs physiques | N/A
SMB 3.0 | N/A | OUI
SAN (ISCSI) | OUI | OUI
Chemins d’accès multiples (MPIO)<br></br>Testé avec : Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pour CLARiiON | OUI | OUI

### <a name="guest-or-physical-server-storage-configuration"></a>Configuration du stockage sur serveur physique ou invité

**Configuration** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
VMDK | OUI | N/A
VHD/VHDX | N/A | OUI
Machine virtuelle de 2e génération | N/A | OUI
EFI/UEFI| Migration vers Azure pour Windows Server 2012 et les dernières versions des machines virtuelles VMware uniquement. </br></br> ** Voir la remarque à la fin du tableau.  | OUI
Disque de cluster partagé | Non  | Non 
Disque chiffré | Non  | Non 
NFS | Non  | N/A
SMB 3.0 | Non  | Non 
RDM | OUI<br/><br/> N/A pour les serveurs physiques | N/A
Disque > 1 To | OUI<br/><br/>Jusqu’à 4095 Go | OUI<br/><br/>Jusqu’à 4095 Go
Disque avec une taille de secteur logique de 4 K et physique de 4 K | OUI | Non prise en charge pour les machines virtuelles de génération 1<br/><br/>Les machines virtuelles de génération 2 ne sont pas prises en charge.
Disque avec une taille de secteur logique de 4 K et physique de 512 octets | OUI |  OUI
Volume avec disque à bandes > 1 To<br/><br/> Gestion des volumes logiques | OUI | OUI
Espaces de stockage | Non  | OUI
Ajout/suppression de disque à chaud | Non  | Non 
Exclure le disque | OUI | OUI
Chemins d’accès multiples (MPIO) | N/A | OUI

> [!NOTE]
> ** Les machines virtuelles VMware à démarrage UEFI exécutant Windows Server 2012 ou une version ultérieure peuvent être migrées vers Azure. Les restrictions suivantes s’appliquent.
> - Migration vers Azure uniquement. Restauration automatique vers le site VMware local non prise en charge.
> - 4 partitions maximum sont prises en charge sur le disque du système d’exploitation du serveur.
> - Requiert la version du service Azure Site Recovery mobilité 9.13 ou version ultérieure.
> - Pas de prise en charge pour les serveurs physiques.

**Azure Storage** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
LRS | OUI | OUI
GRS | OUI | OUI
RA-GRS | OUI | OUI
Stockage froid | Non  | Non 
Stockage chaud| Non  | Non 
Objets blob de blocs | Non  | Non 
Chiffrement au repos (SSE)| OUI | OUI
Stockage Premium | OUI | OUI
Service Import/Export | Non  | Non 
Points de terminaison de service de réseau virtuel (Pare-feu et réseaux virtuels dans Stockage Azure ) configurés sur le compte de stockage cible ou le compte de stockage de cache utilisé pour stocker les données de réplication | Non  | Non 
Comptes de stockage V2 à usage général (niveaux chaud et froid) | Non  | Non 


## <a name="support-for-azure-compute-configuration"></a>Prise en charge de la configuration de calcul Azure

**Fonctionnalité de calcul** | **Serveur VMware/physique** | **Hyper-V (avec / sans Virtual Machine Manager)**
--- | --- | ---
Groupes à haute disponibilité | OUI | OUI
HUB | OUI | OUI  
Disques gérés | OUI | OUI<br/><br/>La restauration automatique locale depuis une machine virtuelle Azure avec disques gérés n’est actuellement pas prise en charge.

## <a name="failed-over-azure-vm-requirements"></a>Configuration requise des machines virtuelles Azure basculées

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Les machines virtuelles locales à répliquer doivent répondent à la configuration requise d’Azure ci-dessous lors de la réplication vers Azure.

**Entité** | **Configuration requise** | **Détails**
--- | --- | ---
**Système d’exploitation invité** | Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md) | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu'à 2048 Go si vous répliquez des **machines virtuelles VMware ou des serveurs physiques vers Azure**.<br/><br/>Jusqu’à 2048 Go pour les machines virtuelles **Hyper-V Génération 1**.<br/><br/>Jusqu’à 300 Go pour les **machines virtuelles Hyper-V Génération 2**.  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | 64 ou moins si vous répliquez des **machines virtuelles VMware sur Azure** ; 16 ou moins si vous répliquez des **machines virtuelles Hyper-V sur Azure** | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 4095 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Disque dur virtuel partagé** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
**BitLocker** | Non pris en charge | Bitlocker doit être désactivé préalablement à la protection d’une machine virtuelle.
**Nom de la machine virtuelle** | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Le nom de la machine virtuelle doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery.
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2 -- Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et un espace disque inférieur à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Prise en charge des actions de coffre Recovery Services

**Action** | **Serveur VMware/physique** | **Hyper-V (sans VMM)** | **Hyper-V (avec VMM)**
--- | --- | --- | ---
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non  | Non  | Non 
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non  | Non  | Non 


## <a name="support-for-provider-and-agent"></a>Prise en charge du fournisseur et de l’agent

**Name** | **Description** | **Version la plus récente** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Installé sur les serveurs VMM locaux ou sur des serveurs Hyper-V s’il n’existe aucun serveur VMM | 5.1.2700.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)
**Installation unifiée d’Azure Site Recovery (VMware vers Azure)** | Coordonne les communications entre les serveurs VMware locaux et Azure  <br/><br/> Installé sur des serveurs VMware locaux | 9.12.4653.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)
**Service de mobilité** | Coordonne la réplication entre les serveurs VMware/serveurs physiques et Azure/site secondaire<br/><br/> Installé sur une machine virtuelle ou des serveurs physiques VMware que vous souhaitez répliquer  | 9.12.4653.1 (disponible sur le portail) | [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)
**Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans serveur VMM) | Dernier agent (disponible sur le portail) |






## <a name="next-steps"></a>étapes suivantes
[Vérifiez les composants requis](site-recovery-prereq.md)
