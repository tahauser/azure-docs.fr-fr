---
title: Opérations SAP HANA sur Azure | Microsoft Docs
description: Guide des opérations pour les systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb715960a516c6b2ca16376c12cb6f796e0b395
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guide des opérations des SAP HANA sur Azure
Ce document fournit des instructions pour le fonctionnement des systèmes SAP HANA qui sont déployés sur des machines virtuelles Azure natives. Ce document n’a pas pour but de remplacer la documentation SAP standard, qui propose le contenu suivant :

- [SAP Administration Guide (Guide d’administration de SAP)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP Installation Guide (Guide d’installation de SAP)](https://service.sap.com/instguides)
- [Notes SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide, vous devez disposer des connaissances de base quant aux différents composants Azure suivants :

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Mise en réseau et réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Stockage Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Pour en savoir plus sur SAP NetWeaver et d’autres composants SAP sur Azure, consultez la section [SAP sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) de la [documentation Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considérations de base relatives à la configuration
Les sections suivantes décrivent les considérations de base relatives à la configuration pour le déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="connect-into-azure-virtual-machines"></a>Se connecter à des machines virtuelles Azure
Comme décrit dans le [guide de planification des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), il existe deux méthodes de base pour se connecter à des machines virtuelles Azure :

- Connexion par le biais de points de terminaison publics et Internet sur une machine virtuelle Jump ou la machine virtuelle qui exécute SAP HANA.
- Connexion par le biais d’un [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou d’Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Pour les scénarios de production, vous devez avoir une connectivité de site à site par le biais d’un VPN ou d’ExpressRoute. Ce type de connexion est également nécessaire pour les scénarios hors production qui alimentent des scénarios de production où des logiciels SAP sont utilisés. L’image suivante représente un exemple de connectivité intersite :

![Connectivité intersite](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Choisir des types de machines virtuelles Azure
Les types de machines virtuelles Azure qui peuvent être utilisés pour les scénarios de production sont répertoriés dans la [documentation SAP pour IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Pour les scénarios hors production, une plus large gamme de types de machines virtuelles Azure natives est disponible.

>[!NOTE]
> Pour les scénarios hors production, utilisez les types de machines virtuelles qui sont répertoriés dans la [note SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Pour les scénarios de production, utilisez les machines virtuelles Azure certifiées SAP HANA qui sont répertoriées dans la [liste des plateformes IaaS certifiées](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) publiée par SAP.

Déployez les machines virtuelles sur Azure en utilisant :

- le portail Azure ;
- les applets de commande Azure PowerShell ;
- l’interface de ligne de commande Azure.

Vous pouvez également déployer une plateforme SAP HANA installée et complète sur les services de machine virtuelle Azure par le biais de la [plateforme cloud SAP](https://cal.sap.com/). Le processus d’installation est décrit dans [Déploiement de SAP S/4HANA ou BW/4HANA sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou avec le processus d’automatisation publié [ici](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Choisir le type de stockage Azure
Azure fournit deux types de stockage adaptés aux machines virtuelles Azure exécutant SAP HANA :

- [Stockage Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Stockage Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre deux méthodes de déploiement avec les disques durs virtuels sur le stockage Azure Standard et Premium. Si le scénario global le permet, tirez parti des déploiements [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Pour obtenir une liste des types de stockage et les contrats SLA associés pour les débits d’IOPS et de stockage, passez en revue la [documentation Azure sur les disques managés](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configuration du stockage des machines virtuelles Azure

Normalement, quand vous achetez des appliances SAP HANA pour une utilisation locale, vous n’avez pas besoin de vous préoccuper des sous-systèmes d’E/S et de leurs capacités, car c’est le fournisseur des appliances qui doit s’assurer que les appliances remplissent les exigences de stockage minimales requises pour SAP HANA. Quand vous concevez votre propre infrastructure Azure, prenez aussi connaissance de certaines de ces exigences. Cela vous permettra de mieux comprendre les différentes configurations requises recommandées dans les sections suivantes. Cela vaut également si vous configurez des machines virtuelles pour y exécuter SAP HANA. Certaines des exigences requises le sont pour :

- Permettre les lectures/écritures sur le volume /hana/log à un débit de 250 Mo/s au minimum pour des tailles d’E/S d’1 Mo/s
- Permettre les lectures sur le volume /hana/data à un débit de 400 Mo/s au minimum pour des tailles d’E/S de 16 Mo et 64 Mo
- Permettre les écritures sur le volume /hana/data à un débit de 250 Mo/s au minimum pour des tailles d’E/S de 16 Mo et 64 Mo

Étant donné qu’une latence de stockage faible est critique pour les systèmes SGBD, même pour des systèmes comme SAP HANA, conservez les données en mémoire. Le point critique dans le stockage concerne généralement les écritures du journal de transaction sur les systèmes SGBD. D’autres opérations peuvent également être critiques, par exemple, l’écriture des points de sauvegarde ou le chargement des données en mémoire après une récupération sur incident. C’est pourquoi vous devez obligatoirement utiliser des disques Azure Premium pour les volumes /hana/data et /hana/log. Pour respecter les exigences de débit minimal requises par SAP pour les volumes /hana/log et /hana/data, créez un volume RAID 0 avec MDADM ou LVM sur plusieurs disques de stockage Azure Premium et utilisez les volumes RAID comme volumes /hana/data et /hana/log. Utilisez les tailles de bande recommandées suivantes pour le disque RAID 0 :

- 64 Ko ou 128 Ko pour /hana/data
- 32 Ko pour /hana/log

> [!NOTE]
> Vous n’avez pas besoin de configurer un niveau de redondance avec des volumes RAID, car les stockages Azure Premium et Standard conservent trois images d’un disque dur virtuel. L’utilisation d’un volume RAID a pour but principal de configurer des volumes qui fournissent un débit d’E/S suffisant.

L’augmentation du nombre de disques durs virtuels Azure sous un volume RAID a pour effet d’augmenter les débits d’IOPS et de stockage. Ainsi, si vous placez un volume RAID 0 sur trois disques P30 de stockage Azure Premium, vous obtenez en principe trois fois plus de débit d’IOPS et trois fois plus de débit de stockage qu’avec un seul disque P30 de stockage Azure Premium.

Ne configurez pas de mise en cache du stockage Premium sur les disques utilisés pour /hana/data et /hana/log. Sur tous les disques constituant ces volumes, la mise en cache doit être définie sur Aucune.

Gardez également à l’esprit le débit d’E/S de machine virtuelle global lors du dimensionnement ou du choix d’une machine virtuelle. Le débit de stockage de machine virtuelle global est décrit dans l’article [Tailles de machine virtuelle à mémoire optimisée](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Configuration du stockage Azure avec prise en compte des coûts
Le tableau suivant illustre une configuration de types de machines virtuelles que les clients utilisent couramment pour héberger SAP HANA sur des machines virtuelles Azure. Il est possible que certains types de machines virtuelles ne remplissent pas tous les critères minimum pour SAP HANA. Toutefois, ces machines virtuelles n’ont pas montré, jusqu’à présent, de problèmes de fonctionnement dans les scénarios hors production. 

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Throughput | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 Go | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 Gio | 1 200 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume /hana/backup a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.   
Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour /hana/data et /hana/log, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. 

> [!NOTE]
> Les configurations ci-dessus ne bénéficient PAS du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), car elles utilisent à la fois le stockage Azure Premium et le stockage Azure Standard. Toutefois, ce choix a été fait dans le but d’optimiser les coûts.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Configuration du stockage Azure pour bénéficier du contrat SLA de machine virtuelle unique
Pour bénéficier du [contrat SLA de machine virtuelle Azure à instance unique](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), vous devez utiliser exclusivement des disques durs virtuels de stockage Azure Premium.

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Throughput | /hana/data et /hana/log<br /> agrégés avec LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 Go | 768 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 Go | 384 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 Gio | 768 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 Gio | 1 200 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 Gio | 2 000 Mo/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1 000 Gio | 1 000 Mo/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


Les disques recommandés pour les types de machines virtuelles les plus petits avec 3 x P20 dépassent la taille des volumes en ce qui concerne les recommandations d’espace selon le [livre blanc de stockage TDI SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Toutefois, le choix affiché dans le tableau a été effectué pour fournir un débit de disque suffisant pour SAP HANA. La taille indiquée pour le volume /hana/backup a été fixée pour permettre la conservation de sauvegardes représentant deux fois le volume de mémoire, mais vous pouvez ajuster ce paramètre selon vos besoins.  
Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour /hana/data et /hana/log, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solution de stockage avec l’Accélérateur des écritures Azure pour les machines virtuelles Azure de la série M
L’Accélérateur des écritures Azure est une fonctionnalité qui est fournie uniquement pour les machines virtuelles de la série M. Comme son nom l’indique, cette fonctionnalité vise à améliorer la latence d’E/S des opérations d’écriture sur le stockage Azure Premium. Pour SAP HANA, l’Accélérateur des écritures doit être utilisé exclusivement sur le volume /hana/log. Les configurations présentées plus haut doivent donc être modifiées en conséquence. Le principal changement concerne la répartition entre les volumes /hana/data et /hana/log pour utiliser l’Accélérateur des écritures Azure uniquement sur le volume /hana/log. 

> [!IMPORTANT]
> La certification SAP HANA des machines virtuelles Azure de la série M est valable exclusivement avec l’Accélérateur des écritures Azure sur le volume /hana/log. Par conséquent, dans les scénarios de production, les déploiements SAP HANA sur des machines virtuelles Azure de la série M doivent être configurés avec l’Accélérateur des écritures Azure sur le volume /hana/log.  

> [!NOTE]
> Pour les scénarios de production, vérifiez si un type de machine virtuelle spécifique est pris en charge pour SAP HANA dans la [documentation SAP pour IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Les configurations recommandées sont les suivantes :

| Référence de la machine virtuelle | RAM | Bande passante E/S DE MACHINE VIRTUELLE<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1 000 Gio | 1 000 Mo/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1 750 Gio | 1 000 Mo/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2 000 Gio | 2 000 Mo/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3 800 Gio | 2 000 Mo/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Vérifiez que le débit de stockage des différents volumes suggérés est suffisant pour la charge de travail à effectuer. Si la charge de travail nécessite de plus grands volumes pour /hana/data et /hana/log, augmentez le nombre de disques durs virtuels de stockage Azure Premium. Le dimensionnement d’un volume en ajoutant plus de disques durs virtuels que le nombre suggéré permet d’augmenter le débit d’IOPS et d’E/S dans les limites définies pour le type de machine virtuelle Azure.

L’Accélérateur des écritures Azure fonctionne uniquement en association avec [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Cela signifie que les disques de stockage Azure Premium constituant le volume /hana/log doivent être déployés en tant que disques managés.

L’Accélérateur des écritures Azure prend en charge un nombre limité de disques durs virtuels de stockage Azure Premium par machine virtuelle. Les limites actuelles sont :

- 16 disques durs virtuels pour une machine virtuelle M128xx
- 8 disques durs virtuels pour une machine virtuelle M64xx

Vous trouverez des instructions plus détaillées sur l’activation de l’Accélérateur des écritures Azure dans l’article [Accélérateur des écritures Azure pour les déploiements SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/how-to-enable-write-accelerator).

Vous y trouverez aussi les détails et les restrictions relatifs à l’utilisation de cet accélérateur.


### <a name="set-up-azure-virtual-networks"></a>Configurer les réseaux virtuels Azure
Quand vous disposez d’une connectivité de site à site dans Azure via un VPN ou ExpressRoute, vous avez au minimum un [réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) connecté par le biais d’une passerelle virtuelle au circuit ExpressRoute ou VPN. La passerelle virtuelle réside sur un sous-réseau du réseau virtuel Azure. Pour installer SAP HANA, vous créez deux sous-réseaux supplémentaires dans le réseau virtuel. Un sous-réseau héberge les machines virtuelles pour exécuter les instances de SAP HANA. L’autre sous-réseau exécute des machines virtuelles Jumpbox ou de gestion pour héberger SAP HANA Studio ou d’autres logiciels de gestion.

Quand vous installez les machines virtuelles pour exécuter SAP HANA, celles-ci nécessitent :

- deux cartes réseau virtuelles installées, une pour se connecter au sous-réseau de gestion et une autre qui sert à se connecter à l’instance de SAP HANA dans la machine virtuelle Azure à partir du réseau local ou d’autres réseaux ;
- des adresses IP statiques privées déployées pour les deux cartes réseau virtuelles.

Pour obtenir une vue d’ensemble des différentes méthodes d’attribution des adresses IP, consultez [Types d’adresses IP et méthodes d’allocation dans Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Pour les machines virtuelles qui exécutent SAP HANA, vous devez utiliser les adresses IP statiques attribuées. En effet, certains attributs de configuration pour HANA référencent des adresses IP.

Les [groupes de sécurité réseau Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) permettent de diriger le trafic qui est acheminé vers l’instance de SAP HANA ou la machine virtuelle Jumpbox. Les groupes de sécurité réseau sont associés au sous-réseau SAP HANA et au sous-réseau de gestion.

L’image suivante représente une vue d’ensemble d’un schéma de déploiement approximatif pour SAP HANA :

![Schéma de déploiement approximatif pour SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Pour déployer SAP HANA sur Azure sans une connexion de site à site, accédez à l’instance de SAP HANA avec une adresse IP publique. L’adresse IP doit être affectée à la machine virtuelle Azure exécutant votre machine virtuelle Jumpbox. Dans ce scénario de base, le déploiement s’appuie sur les services DNS intégrés à Azure pour résoudre les noms d’hôte. Dans un déploiement plus complexe où des adresses IP publiques sont utilisées, les services DNS intégrés à Azure sont particulièrement importants. Utilisez des groupes de sécurité réseau Azure pour limiter les ports ouverts ou les plages d’adresses IP autorisées à se connecter aux sous-réseaux Azure avec des ressources qui ont des adresses IP publiques. L’image suivante représente un schéma approximatif pour le déploiement de SAP HANA sans une connexion de site à site :
  
![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Opérations pour le déploiement de SAP HANA sur des machines virtuelles Azure
Les sections suivantes décrivent certaines des opérations liées au déploiement de systèmes SAP HANA sur des machines virtuelles Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Opérations de sauvegarde et restauration sur des machines virtuelles Azure
Les documents suivants décrivent comment sauvegarder et restaurer votre déploiement de SAP HANA :

- [Vue d’ensemble de la sauvegarde SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Sauvegarde SAP HANA au niveau des fichiers](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Sauvegarde SAP HANA à partir de captures instantanées de stockage](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Démarrer et redémarrer des machines virtuelles contenant SAP HANA
L’une des caractéristiques importantes du cloud public Azure est que vous êtes facturé uniquement pour les minutes de calcul que vous dépensez. Par exemple, quand vous arrêtez une machine virtuelle exécutant SAP HANA, seuls les coûts de stockage pendant cette durée vous sont facturés. Une autre fonctionnalité est disponible quand vous spécifiez des adresses IP statiques pour vos machines virtuelles dans votre déploiement initial. Quand vous redémarrez une machine virtuelle avec SAP HANA, la machine virtuelle redémarre avec ses adresses IP antérieures. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utiliser SAProuter pour la prise en charge à distance de SAP
Si vous avez une connexion de site à site entre vos emplacements locaux et Azure, et que vous exécutez des composants SAP, il est très probable que vous exécutez déjà SAProuter. Dans ce cas, effectuez les opérations suivantes pour la prise en charge à distance :

- Mettre à jour l’adresse IP privée et statique de la machine virtuelle qui héberge SAP HANA dans la configuration de SAProuter.
- Configurer le groupe de sécurité réseau du sous-réseau qui héberge la machine virtuelle HANA pour autoriser le trafic via le port TCP/IP 3299.

Si vous vous connectez à Azure par le biais d’Internet et que vous n’avez pas installé de routeur SAP pour la machine virtuelle avec SAP HANA, vous devez installer le composant. Installez SAProuter sur une machine virtuelle distincte dans le sous-réseau de gestion. L’image suivante représente un schéma approximatif pour le déploiement de SAP HANA sans une connexion de site à site et avec SAProuter :

![Schéma de déploiement approximatif pour SAP HANA sans une connexion de site à site et avec SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Veillez à installer SAProuter sur une machine virtuelle distincte, et non sur votre machine virtuelle Jumpbox. La machine virtuelle distincte doit avoir une adresse IP statique. Pour connecter votre SAProuter au SAProuter hébergé par SAP, contactez SAP pour obtenir une adresse IP. (Le SAProuter qui est hébergé par SAP est l’équivalent de l’instance de SAProuter que vous installez sur la machine virtuelle.) Utilisez l’adresse IP de SAP pour configurer votre instance de SAProuter. Dans les paramètres de configuration, le seul port nécessaire est le port TCP 3299.

Pour plus d’informations sur la façon de configurer et de gérer des connexions de prise en charge à distance par le biais de SAProuter, consultez la [documentation SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Haute disponibilité avec SAP HANA sur les machines virtuelles Azure natives
Si vous exécutez SUSE Linux 12 SP1 ou une version plus récente, vous pouvez établir un cluster Pacemaker avec des appareils STONITH. Vous pouvez utiliser les appareils afin de définir une configuration SAP HANA qui utilise la réplication synchrone avec la réplication de système HANA et le basculement automatique. Pour plus d’informations sur la procédure de configuration, consultez le [Guide de la haute disponibilité de SAP HANA sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
