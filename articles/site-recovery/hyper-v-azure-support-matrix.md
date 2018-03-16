---
title: "Matrice de prise en charge pour la réplication d’Hyper-V vers Azure | Microsoft Docs"
description: "Résume les composants pris en charge et les exigences pour la réplication de Hyper-V vers Azure avec Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 81983b9287a6b8073724f0cd973929f4b4677d4a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matrice de prise en charge pour la réplication d’Hyper-V vers Azure


Cet article résume les composants pris en charge ainsi que les paramètres concernant la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure, à l’aide du service [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scénarios pris en charge

**Scénario** | **Détails**
--- | --- 
**Hyper-V avec VMM** | Vous pouvez effectuer la récupération d’urgence vers Azure pour les machines virtuelles s’exécutant sur des hôtes Hyper-V gérés dans l’infrastructure System Center Virtual Machine Manager (VMM)<br/><br/> Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de PowerShell.<br/><br/> Lorsque les hôtes Hyper-V sont gérés par VMM, vous pouvez également effectuer la récupération d’urgence vers un site secondaire local. Pour plus d’informations sur ce scénario, consultez cet [article](tutorial-vmm-to-vmm.md).
**Hyper-V sans VMM** | Vous pouvez effectuer la récupération d’urgence vers Azure pour les machines virtuelles s’exécutant sur les hôtes Hyper-V qui ne sont pas gérés par VMM.<br/><br/> Vous pouvez déployer ce scénario dans le portail Azure ou à l’aide de PowerShell. 


## <a name="on-premises-servers"></a>Serveurs locaux

**Serveur** | **Configuration requise** | **Détails**
--- | --- | ---
**Hyper-V (s’exécutant sans VMM)** | Windows Server 2016, Windows Server 2012 R2 avec les dernières mises à jour. | Lorsque vous configurez un site Hyper-V dans Site Recovery, le mélange d’ordinateurs hôtes exécutant Windows Server 2016 et 2012 R2 n’est pas pris en charge.<br/><br/> Pour les machines virtuelles situées sur un hôte exécutant Windows Server 2016, la récupération vers un autre emplacement n’est pas prise en charge.
**Hyper-V (s’exécutant avec VMM)** | VMM 2016, VMM 2012 R2 | Si VMM est utilisé, les hôtes Windows Server 2016 doivent être gérés dans VMM 2016.<br/><br/> Les clouds VMM combinant des hôtes Hyper-V exécutant Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.<br/><br/> Les environnements qui incluent une mise à niveau d’un serveur VMM 2012 R2 existant vers 2016 ne sont pas pris en charge.


## <a name="replicated-vms"></a>Machines virtuelles répliquées


Le tableau suivant récapitule la prise en charge des machines virtuelles. Site Recovery prend en charge les charges de travail s’exécutant sur un système d’exploitation pris en charge. 

 **Composant** | **Détails**
--- | ---
Configuration des machines virtuelles | Les machines virtuelles qui répliquent vers Azure doivent répondre aux [conditions requises par Azure](#failed-over-azure-vm-requirements).
Système d’exploitation invité | N’importe quel système d’exploitation invité [pris en charge par Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server n’est pas pris en charge.




## <a name="hyper-v-network-configuration"></a>Configuration réseau Hyper-V

**Composant** | **Hyper-V avec VMM** | **Hyper-V sans VMM**
--- | --- | ---
Réseau hôte : association de cartes réseau | OUI
Hôte réseau : VLAN | OUI
Hôte réseau : IPv4 | OUI
Hôte réseau : IPv6 | Non 
Réseau de machines virtuelles invitées : association de cartes réseau | Non 
Réseau de machines virtuelles invitées : IPv4 | OUI
Réseau de machines virtuelles invitées : IPv6 | Non 
Réseau de machines virtuelles invitées : adresse IP statique (Windows) | OUI
Réseau de machines virtuelles invitées : adresse IP statique (Linux) | Non 
Réseau de machines virtuelles invitées : plusieurs cartes réseau | OUI



## <a name="azure-vm-network-configuration-after-failover"></a>Configuration de réseau des machines virtuelles Azure (après basculement)

**Composant** | **Hyper-V avec VMM** | **Hyper-V sans VMM**
--- | --- | ---
ExpressRoute | OUI | OUI
ILB | OUI | OUI
ELB | OUI | OUI
Traffic Manager | OUI | OUI
Plusieurs cartes réseau | OUI | OUI
Adresse IP réservée | OUI | OUI
IPv4 | OUI | OUI
Conserver l’adresse IP source | OUI | OUI
Points de terminaison de service de réseau virtuel<br/><br/> (Pare-feu et réseaux virtuels dans Stockage Azure) | Non  | Non 


## <a name="hyper-v-host-storage"></a>Stockage hôte Hyper-V

**Stockage** | **Hyper-V avec VMM** | Hyper-V sans VMM
--- | --- | --- | ---
NFS | N/D | N/D
SMB 3.0 | OUI | OUI
SAN (ISCSI) | OUI | OUI
Chemins d’accès multiples (MPIO). Testé avec :<br></br> Module DSM Microsoft, EMC PowerPath 5.7 SP4<br/><br/> Module DSM EMC PowerPath pour CLARiiON | OUI | OUI

## <a name="hyper-v-vm-guest-storage"></a>Stockage invité de machines virtuelles Hyper-V

**Stockage** | **Hyper-V avec VMM** | Hyper-V sans VMM
--- | --- | ---
VMDK | N/D | N/D
VHD/VHDX | OUI | OUI
Machine virtuelle de 2e génération | OUI | OUI
EFI/UEFI| OUI | OUI
Disque de cluster partagé | Non  | Non 
Disque chiffré | Non  | Non 
NFS | N/D | N/D
SMB 3.0 | Non  | Non 
RDM | N/D | N/D
Disque > 1 To | Oui, jusqu’à 4 095 Go | Oui, jusqu’à 4 095 Go
Disque : secteur logique et physique de 4 K | Non pris en charge : 1re génération, 2e génération | Non pris en charge : 1re génération, 2e génération
Disque : secteur logique de 4 K et physique de 512 octets | OUI |  OUI
Volume avec disque à bandes > 1 To<br/><br/> Gestion des volumes logiques | OUI | OUI
Espaces de stockage | OUI | OUI
Ajout/suppression de disque à chaud | Non  | Non 
Exclure le disque | OUI | OUI
Chemins d’accès multiples (MPIO) | OUI | OUI

## <a name="azure-storage"></a>Stockage Azure

**Composant** | **Hyper-V avec VMM** | **Hyper-V sans VMM**
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
Points de terminaison de service VNET (pare-feux de Stockage Azure et VNET), sur la cible pour le compte de stockage de cache utilisé pour les données de réplication | Non  | Non 


## <a name="azure-compute-features"></a>Fonctionnalités de Calcul Azure

**Fonctionnalité** | **Hyper-V avec VMM** | **Hyper-V sans VMM**
--- | --- | ---
Groupes à haute disponibilité | OUI | OUI
HUB | OUI | OUI  
Disques gérés | Oui, pour le basculement<br/><br/> La restauration automatique des disques managés n’est pas prise en charge | Oui, pour le basculement<br/><br/> La restauration automatique des disques managés n’est pas prise en charge

## <a name="azure-vm-requirements"></a>Exigences des machines virtuelles Azure

Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les exigences des machines virtuelles Azure décrites dans ce tableau.

**Composant** | **Configuration requise** | **Détails**
--- | --- | ---
**Système d’exploitation invité** | Site Recovery fonctionne sur tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu’à 2 048 Go pour les machines virtuelles de 1re génération.<br/><br/> Jusqu’à 300 Go pour les machines virtuelles de 2e génération.  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | 16 ou moins  | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 4095 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Disque dur virtuel partagé** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
**BitLocker** | Non pris en charge | BitLocker doit être désactivé avant d’activer la réplication pour une machine virtuelle.
**Nom de la machine virtuelle** | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Le nom de la machine virtuelle doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery.
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2 -- Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et un espace disque inférieur à 300 Go sont prises en charge.<br></br>Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Actions de coffre Recovery Services

**Action** |  **Hyper-V avec VMM** | **Hyper-V sans VMM**
--- | --- | --- 
Déplacer le coffre entre plusieurs groupes de ressources<br/><br/> Au sein et entre des abonnements | Non  | Non  
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources<br/><br/> Au sein et entre des abonnements | Non  | Non  


## <a name="provider-and-agent"></a>Fournisseur et agent

Pour vous assurer que votre déploiement est compatible avec les paramètres de cet article, vérifiez que vous utilisez le fournisseur et les versions d’agent les plus récents.

**Name** | **Description** | **Détails**
--- | --- | --- | --- | ---
**Fournisseur Azure Site Recovery** | Coordonne les communications entre les serveurs locaux et Azure <br/><br/> Hyper-V avec VMM : installé sur les serveurs VMM<br/><br/> Hyper-V sans VMM : installé sur les hôtes Hyper-V| Version la plus récente : 5.1.2700.1 (disponible sur le portail)<br/><br/> [Fonctionnalités et correctifs récents](https://aka.ms/latest_asr_updates)
**Agent Microsoft Azure Recovery Services (MARS)** | Coordonne la réplication entre les machines virtuelles Hyper-V et Azure<br/><br/> Installé sur des serveurs Hyper-V locaux (avec ou sans VMM) | Dernier agent disponible sur le portail






## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [préparer Azure](tutorial-prepare-azure.md) à la récupération d’urgence de machines virtuelles Hyper-V locales. 
