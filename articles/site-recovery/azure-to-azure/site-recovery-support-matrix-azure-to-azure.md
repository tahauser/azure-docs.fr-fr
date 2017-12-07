---
title: "Matrice de support Azure Site Recovery pour la réplication Azure vers Azure | Microsoft Docs"
description: "Fournit un récapitulatif des systèmes d’exploitation et des configurations pris en charge pour la réplication de machines virtuelles Azure Site Recovery d’une région à une autre à des fins de récupération d’urgence."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 46ca545cc73d7b8118aae4662c31965c7db87df5
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matrice de support Azure Site Recovery pour la réplication Azure vers Azure


>[!NOTE]
>
> La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article récapitule les composants et les configurations pris en charge pour Azure Site Recovery lors de la réplication et de la récupération de machines virtuelles d’une région vers une autre.

## <a name="user-interface-options"></a>Options d’interface utilisateur

**Interface utilisateur** |  **Prise en charge / Non prise en charge**
--- | ---
**Portail Azure** | Pris en charge
**Portail Classic** | Non pris en charge
**PowerShell** | Non pris en charge pour le moment
**API REST** | Non prise en charge pour le moment
**INTERFACE DE LIGNE DE COMMANDE** | Non prise en charge pour le moment


## <a name="resource-move-support"></a>Prise en charge du déplacement de ressources

**Type de déplacement de ressources** | **Pris en charge / Non pris en charge** | **Notes**  
--- | --- | ---
**Déplacer le coffre entre plusieurs groupes de ressources** | Non pris en charge |Vous ne pouvez pas déplacer le coffre Recovery services d’un groupe de ressources à un autre.
**Déplacer le calcul, le stockage et le réseau entre plusieurs groupes de ressources** | Non pris en charge |Si vous déplacez une machine virtuelle (ou ses composants associés, tels que le réseau et le stockage) après avoir activé la réplication, vous devez désactiver la réplication puis la réactiver pour la machine virtuelle.



## <a name="support-for-deployment-models"></a>Prise en charge des modèles de déploiement

**Modèle de déploiement** | **Pris en charge / Non pris en charge** | **Notes**  
--- | --- | ---
**Classique** | Pris en charge | Vous pouvez uniquement répliquer une machine virtuelle classique et la récupérer en tant que machine virtuelle classique. Vous ne pouvez pas la récupérer en tant que machine virtuelle Resource Manager. Si vous déployez une machine virtuelle classique sans réseau virtuel directement vers une région Azure, elle n’est pas prise en charge.
**Resource Manager** | Pris en charge |

>[!NOTE]
>
> - La réplication de machines virtuelles Azure d’un abonnement sur l’autre pour les scénarios de récupération d’urgence n’est pas prise en charge.
> - La migration de machines virtuelles Azure entre abonnements n’est pas prise en charge.
> - La migration de machines virtuelles Azure dans la même région n’est pas prise en charge.
> - La migration de machines virtuelles Azure du modèle de déploiement Classic au modèle de déploiement Resource Manager n’est pas prise en charge.

## <a name="support-for-replicated-machine-os-versions"></a>Prise en charge des versions de système d’exploitation de machine répliquée

La prise en charge ci-dessous est applicable à toutes les charges de travail en cours d’exécution sur le système d’exploitation mentionné.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Server avec Expérience utilisateur)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 avec au moins SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server n’est pas pris en charge.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- Serveur LTS Ubuntu 14.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Serveur LTS Ubuntu 16.04 [ (versions du noyau prises en charge)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(La mise à niveau des machines de réplication de SLES 11 SP3 vers SLES 11 SP4 n’est pas prise en charge. Si une machine répliquée a été mise à niveau, de SLES 11SP3 vers SLES 11 SP4, vous devez désactiver la réplication et protéger à nouveau la machine après la mise à niveau.)

>[!NOTE]
>
> Sur les serveurs Ubuntu utilisant l’authentification et la connexion basées sur un mot de passe, et utilisant le package cloud-init pour configurer des machines virtuelles cloud, la connexion basée sur un mot de passe peut être désactivée lors du basculement (en fonction de la configuration de cloudinit.) La connexion basée sur un mot de passe peut être réactivée sur la machine virtuelle en réinitialisant le mot de passe dans le menu des paramètres (dans la section SUPPORT + TROUBLESHOOTING) de la machine virtuelle basculée sur le portail Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau Ubuntu prises en charge pour les machines virtuelles Azure

**Version** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic à 3.13.0-117-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic à 3.13.0-121-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic à 3.13.0-125-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-83-generic |
LTS 16.04 | 9.10 | 4.4.0-21-generic à 4.4.0-81-generic,<br/>4.8.0-34-generic à 4.8.0-56-generic,<br/>4.10.0-14-generic à 4.10.0-24-generic |
LTS 16.04 | 9.11 | 4.4.0-21-generic à 4.4.0-83-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Systèmes de fichiers et configurations de stockage invité pris en charge sur les machines virtuelles Azure exécutant le système d’exploitation Linux

* Systèmes de fichiers : ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS
* Gestionnaire de volume : LVM2
* Logiciel multichemin : Device Mapper

## <a name="region-support"></a>Prise en charge de la région

Vous pouvez répliquer et restaurer des machines virtuelles entre deux régions appartenant au même cluster géographique.

**Cluster géographique** | **Régions Azure**
-- | --
Amérique | Canada de l’Est, Canada du Centre, Sud du Centre des États-Unis, Ouest du Centre des États-Unis, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis de l’Ouest, États-Unis de l’Ouest 2, États-Unis du Centre, Nord du Centre des États-Unis
Europe | Ouest du Royaume-Uni, Sud du Royaume-Uni, Europe du Nord, Europe de l’Ouest
Asie | Inde du Sud, Centre de l’Inde, Asie du Sud-Est, Asie de l’Est, Japon de l’Est, Japon de l’Ouest, Centre de la Corée, Corée du Sud
Australie   | Est de l’Australie, Sud-Est de l’Australie

>[!NOTE]
>
> Pour la région Sud du Brésil, vous pouvez uniquement répliquer et basculer vers les régions Amérique du Sud, États-Unis de l’Ouest, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis de l’Ouest, États-Unis de l’Ouest 2 et Amérique du Nord, puis effectuer une restauration automatique.


## <a name="support-for-compute-configuration"></a>Prise en charge de la configuration de calcul

**Configuration** | **Prise en charge/Non prise en charge** | **Notes**
--- | --- | ---
Taille | N’importe quelle taille de machine virtuelle Azure avec au moins deux cœurs d’UC et 1 Go de RAM | Voir [Tailles de machine virtuelle Azure](../../virtual-machines/windows/sizes.md).
Groupes à haute disponibilité | Pris en charge | Si vous utilisez l’option par défaut pendant l’étape « Activation de la réplication » dans le portail, le groupe à haute disponibilité est créé automatiquement en fonction de la configuration de la région source. Vous pouvez changer le groupe à haute disponibilité cible à tout moment dans « Élément répliqué > Paramètres > Calcul et réseau > Groupe à haute disponibilité ».
Machines virtuelles HUB (Hybrid Use Benefit) | Prises en charge | Si la machine virtuelle source a une licence HUB activée, la machine virtuelle de basculement ou de test de basculement utilise également la licence HUB.
Groupes identiques de machines virtuelles  | Non pris en charge |
Images de la galerie Azure publiées par Microsoft | Prises en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Images de la galerie Azure publiées par un tiers | Prises en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Images personnalisées publiées par un tiers | Prises en charge | Prises en charge tant que la machine virtuelle s’exécute sur un système d’exploitation pris en charge par Site Recovery.
Machines virtuelles migrées à l’aide de Site Recovery | Prises en charge | S’il s’agit d’un ordinateur physique/VMware migré vers Azure à l’aide de Site Recovery, vous devez désinstaller l’ancienne version du service Mobilité et redémarrer l’ordinateur avant de le répliquer vers une autre région Azure.

## <a name="support-for-storage-configuration"></a>Prise en charge de la configuration de stockage

**Configuration** | **Prise en charge/Non prise en charge** | **Notes**
--- | --- | ---
Taille maximale du disque du système d’exploitation | 2048 GB | Voir [Disques utilisés par les machines virtuelles](../../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Taille maximale de disque de données | 4095 Go | Voir [Disques utilisés par les machines virtuelles](../../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Nombre de disques de données | Jusqu’à 64, tel que pris en charge par une taille spécifique de machine virtuelle Azure | Voir [Tailles de machine virtuelle Azure](../../virtual-machines/windows/sizes.md).
Disque temporaire | Toujours exclus de la réplication | Le disque temporaire est exclu de la réplication. Dans les recommandations Azure, il est stipulé que vous ne devez pas placer de données persistantes sur un disque temporaire. Pour plus d’informations, consultez [Disque temporaire sur des machines virtuelles Azure](../../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Taux de modification des données sur le disque | Au plus 6 Mbits/s par disque | Si le taux moyen de modification des données sur le disque est en permanence supérieur à 6 Mbits/s, la réplication ne pourra pas suivre. Toutefois, s’il s’agit d’une rafale de données occasionnelle, que le taux de modification des données est supérieur à 6 Mbits/s pendant un certain laps de temps, et qu’il redescend par la suite, la réplication pourra rattraper le retard. Dans ce cas, certains points de récupération pourront être légèrement différés.
Disques sur des comptes de stockage Standard | Pris en charge |
Disques sur des comptes de stockage Premium | Pris en charge | Si une machine virtuelle a des disques répartis sur des comptes de stockage Standard et Premium, vous pouvez sélectionner un compte de stockage cible différent pour chaque disque afin d’être sûr d’avoir la même configuration de stockage dans la région cible.
Disques managés Standard | Non pris en charge |  
Disques managés Premium | Non pris en charge |
Espaces de stockage | Pris en charge |         
Chiffrement au repos (SSE) | Pris en charge | Pour les comptes de stockage du cache et cible, vous pouvez sélectionner un compte de stockage compatible SSE.     
Azure Disk Encryption (ADE) | Non pris en charge |
Ajout/suppression de disque à chaud | Non pris en charge | Si vous ajoutez ou supprimez un disque de données sur la machine virtuelle, vous devez désactiver la réplication puis la réactiver pour la machine virtuelle.
Exclure le disque | Non pris en charge|   Le disque temporaire est exclu par défaut.
LRS | Pris en charge |
GRS | Pris en charge |
RA-GRS | Pris en charge |
ZRS | Non pris en charge |  
Stockage à froid et à chaud | Non pris en charge | Les disques de machine virtuelle ne sont pas pris en charge sur le stockage à froid et à chaud.
Points de terminaison de service de réseau virtuel (Pare-feu et réseaux virtuels dans Stockage Azure )  | Non | L’autorisation d’accès à des réseaux virtuels Azure spécifiques sur des comptes de stockage en cache utilisés pour stocker des données répliquées n’est pas prise en charge. 

>[!IMPORTANT]
> Vérifiez que vous respectez les valeurs d'évolutivité et de performances des disques VM pour les machines virtuelles [Linux](../../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../../virtual-machines/windows/disk-scalability-targets.md) afin d'éviter tout problème de performances. Si vous suivez les paramètres par défaut, Site Recovery créera les disques et les comptes de stockage nécessaires en fonction de la configuration source. Si vous personnalisez et sélectionnez vos propres paramètres, veillez à suivre les cibles de scalabilité et de performances de disque de vos machines virtuelles sources.

## <a name="support-for-network-configuration"></a>Prise en charge de la configuration réseau
**Configuration** | **Prise en charge/Non prise en charge** | **Notes**
--- | --- | ---
Interfaces réseau | Jusqu’à la quantité maximale de cartes réseau prises en charge par une taille spécifique de machine virtuelle Azure | Les cartes réseau sont créées quand la machine virtuelle est créée dans le cadre de l’opération Test de basculement ou Basculement. Le nombre de cartes réseau sur la machine virtuelle de basculement dépend du nombre de cartes réseau que possède la machine virtuelle source au moment de l’activation de la réplication. Si vous ajoutez/supprimez des cartes réseau après l’activation de la réplication, cela n’affecte pas le nombre de cartes réseau sur la machine virtuelle de basculement.
Équilibreur de charge Internet | Pris en charge | Vous devez associer l’équilibreur de charge préconfiguré de manière à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Équilibreur de charge interne | Pris en charge | Vous devez associer l’équilibreur de charge préconfiguré de manière à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Adresse IP publique| Pris en charge | Vous devez associer une adresse IP publique existante à la carte réseau ou en créer une et l’associer à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Groupe de sécurité réseau sur la carte réseau (Resource Manager)| Pris en charge | Vous devez associer le groupe de sécurité réseau à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.  
Groupe de sécurité réseau sur le sous-réseau (Resource Manager et Classique)| Pris en charge | Vous devez associer le groupe de sécurité réseau à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Groupe de sécurité réseau sur la machine virtuelle (Classique)| Pris en charge | Vous devez associer le groupe de sécurité réseau à la carte réseau à l’aide d’un script d’automatisation Azure dans un plan de récupération.
Adresse IP réservée (adresse IP statique) / Conserver l’adresse IP source | Prise en charge | Si la carte réseau sur la machine virtuelle source a une configuration IP statique et que le sous-réseau cible a la même adresse IP disponible, elle est affectée à la machine virtuelle de basculement. Si le sous-réseau cible n’a pas la même adresse IP disponible, l’une des adresses IP disponibles sur le sous-réseau est réservée à cette machine virtuelle. Vous pouvez spécifier l’adresse IP fixe de votre choix dans « Élément répliqué > Paramètres > Calcul et réseau > Interfaces réseau ». Vous pouvez sélectionner la carte réseau et spécifier le sous-réseau et l’adresse IP de votre choix.
Adresse IP dynamique| Prise en charge | Si la carte réseau sur la machine virtuelle source a une configuration IP dynamique, la carte réseau sur la machine virtuelle de basculement est également dynamique par défaut. Vous pouvez spécifier l’adresse IP fixe de votre choix dans « Élément répliqué > Paramètres > Calcul et réseau > Interfaces réseau ». Vous pouvez sélectionner la carte réseau et spécifier le sous-réseau et l’adresse IP de votre choix.
Intégration de Traffic Manager | Prise en charge | Vous pouvez préconfigurer Traffic Manager pour que le trafic soit acheminé vers le point de terminaison dans la région source de manière régulière et vers le point de terminaison dans la région cible en cas de basculement.
DNS managé Azure | Pris en charge |
Système DNS personnalisé  | Pris en charge |    
Proxy non authentifié | Pris en charge | Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).    
Proxy authentifié | Non pris en charge | Si la machine virtuelle utilise un proxy authentifié pour la connectivité sortante, elle ne peut pas être répliquée à l’aide d’Azure Site Recovery.    
VPN de site à site avec infrastructure locale (avec ou sans ExpressRoute)| Pris en charge | Vérifiez que les itinéraires définis par l’utilisateur et les groupes de sécurité réseau sont configurés de telle sorte que le trafic Site Recovery ne soit pas acheminé vers l’infrastructure locale. Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).  
Connexion de réseau virtuel à réseau virtuel | Pris en charge | Voir le [document d’aide à la mise en réseau](site-recovery-azure-to-azure-networking-guidance.md).  


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Commencez à protéger vos charges de travail en [répliquant des machines virtuelles Azure](azure-to-azure-quickstart.md).
