---
title: "Préparer l’infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et d’un disque partagé pour SAP ASCS/SCS | Microsoft Docs"
description: "Découvrez comment préparer l’infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et d’un disque partagé pour une instance SAP ASCS/SCS."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2547d40ac39bc8188511f6682911fa302cf3e8a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Préparer l’infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et d’un disque partagé pour SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Cet article décrit les étapes à suivre pour préparer l’infrastructure Azure à l’installation et à la configuration d’un système SAP à haute disponibilité sur un cluster de basculement Windows en utilisant un *disque partagé de cluster* en tant qu’option de clustering d’une instance ASCS SAP.

## <a name="prerequisites"></a>Composants requis

Avant de commencer l’installation, consultez cet article :

* [Guide d’architecture : Clustering d’une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Préparer l’infrastructure du modèle d’architecture n°1
Les modèles Azure Resource Manager pour SAP simplifient le déploiement des ressources requises.

Les modèles à trois niveaux dans Azure Resource Manager prennent également en charge les scénarios de haute disponibilité. Par exemple, Modèle d’architecture n°1 a deux clusters. Chaque cluster constitue un point de défaillance pour l’instance SAP ASCS/SCS et l’instance SGBD.

Voici où vous pouvez obtenir des modèles Azure Resource Manager pour l’exemple de scénario décrit dans cet article :

* [Image de Place de marché Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Image de Place de marché Azure à l’aide d’Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Image personnalisée à l’aide de Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Pour préparer l’infrastructure du modèle d’architecture n°1 :

- Dans le portail Azure, volet **Paramètres**, boîte de dialogue **SYSTEMAVAILABILITY** (disponibilité du système), sélectionnez **HA** (Haute disponibilité).

  ![Figure 1 : Définir les paramètres Azure Resource Manager de haute disponibilité SAP][sap-ha-guide-figure-3000]

_**Figure 1 :** Définir les paramètres Azure Resource Manager de haute disponibilité SAP_


  Les modèles créent :

  * **Des machines virtuelles** :
    * Des machines virtuelles de serveur d’applications SAP :\<SIDSystèmeSAP\>-di-\<Numéro\>
    * Des machines virtuelles de cluster ASCS/SCS : \<SIDSystèmeSAP\>-ascs-\<Numéro\>
    * Un cluster SGBD : \<SIDSystèmeSAP\>-db-\<Number\>

  * **Des cartes réseau pour toutes les machines virtuelles, avec une adresse IP associée** :
    * \<SIDSystèmeSAP\>-nic-di-\<Numéro\>
    * \<SIDSystèmeSAP\>-nic-ascs-\<Numéro\>
    * \<SIDSystèmeSAP\>-nic-db-\<Numéro\>

  * **Des comptes de stockage Azure (disques non gérés uniquement)** :

  * **Des groupes de disponibilité** pour :
    * Les machines virtuelles de serveur d’applications SAP : \<SIDSystèmeSAP\>-avset-di
    * Les machines virtuelles de cluster SAP ASCS/SCS : \<SIDSystèmeSAP\>-avset-ascs
    * Les machines virtuelles de cluster SGBD : \<SIDSystèmeSAP\>-avset-db

  * **Un équilibrage de charge interne Azure** :
    * Avec tous les ports pour l’instance ASCS/SCS et l’adresse IP \<SIDSystèmeSAP\>-lb-ascs
    * Avec tous les ports pour l’instance SGBD et l’adresse IP \<SIDSystèmeSAP\>-lb-db

  * **Un groupe de sécurité réseau** : \<SIDSystèmeSAP\>-nsg-ascs-0  
    * Avec un port RDP (Remote Desktop Protocol) ouvert vers la machine virtuelle \<SIDSystèmeSAP\>-ascs-0

> [!NOTE]
> Toutes les adresses IP des cartes réseau et des équilibrages de charge internes Azure sont dynamiques par défaut. Définissez-les en tant qu’adresses IP statiques. La marche à suivre est décrite plus loin dans cet article.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Déployer des machines virtuelles avec connectivité réseau d’entreprise (intersite) pour une utilisation en production
Pour les systèmes SAP de production, déployez les machines virtuelles Azure avec une [connectivité de réseau d’entreprise (intersite)][planning-guide-2.2] à l’aide d’une passerelle VPN Azure ou d’Azure ExpressRoute.

> [!NOTE]
> Vous pouvez utiliser votre instance de réseau virtuel Azure. Le réseau virtuel et le sous-réseau ont déjà été créés et préparés.
>
>

1.  Dans le portail Azure, volet **Paramètres**, boîte de dialogue **NEWOREXISTINGSUBNET** (sous-réseau nouveau ou existant), sélectionnez **existing** (existant).
2.  Dans la boîte de dialogue **SUBNETID** (ID de sous-réseau), vous devez ajouter la chaîne complète de l’ID du sous-réseau Azure préparé sur lequel vous envisagez de déployer vos machines virtuelles Azure.
3.  Pour obtenir la liste de tous les sous-réseaux du réseau Azure, exécutez cette commande PowerShell :

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Le champ **ID** indique la valeur de l’ID de sous-réseau.
4. Pour obtenir la liste de toutes les valeurs d’ID de sous-réseau, exécutez cette commande PowerShell :

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  L’ID de sous-réseau se présente ainsi :

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Déployer des instances SAP dans le cloud uniquement à des fins de test et de démonstration
Vous pouvez déployer votre système SAP à haute disponibilité dans un modèle de déploiement cloud uniquement. Ce type de déploiement est surtout utile pour les démonstrations et les tests. Il ne convient pas à une utilisation en production.

- Dans le portail Azure, volet **Paramètres**, boîte de dialogue **NEWOREXISTINGSUBNET** (sous-réseau nouveau ou existant), sélectionnez **new** (nouveau). Laissez le champ **SUBNETID** vide.

  Le modèle Azure Resource Manager SAP crée automatiquement le réseau virtuel et le sous-réseau Azure.

> [!NOTE]
> Vous devez également déployer au moins une machine virtuelle dédiée pour Active Directory et le service DNS dans la même instance de réseau virtuel Azure. Le modèle ne crée pas ces machines virtuelles.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Préparer l’infrastructure du modèle d’architecture n°2

Vous pouvez utiliser ce modèle Azure Resource Manager pour SAP afin de simplifier le déploiement des ressources d’infrastructure requises pour le modèle d’architecture SAP n°2.

Voici où vous pouvez obtenir des modèles Azure Resource Manager pour ce scénario de déploiement :

* [Image de Place de marché Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Image de Place de marché Azure à l’aide de Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Image personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Image personnalisée à l’aide de Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Préparer l’infrastructure du modèle d’architecture n°3

Vous pouvez préparer l’infrastructure et configurer SAP pour multi-SID. Par exemple, vous pouvez ajouter une instance SAP ASCS/SCS supplémentaire à une configuration de cluster *existante*. Pour plus d’informations, consultez [Configurer une instance SAP ASCS/SCS supplémentaire pour une configuration de cluster existante afin de créer une configuration SAP multi-SID dans Azure Resource Manager][sap-ha-multi-sid-guide].

Pour créer un cluster multi-SID, vous pouvez utiliser les [modèles de démarrage rapide multi-SID sur GitHub](https://github.com/Azure/azure-quickstart-templates).

Pour créer un nouveau cluster multi-SID, vous devez déployer les trois modèles suivants :

* [Modèle ASCS/SCS](#ASCS-SCS-template)
* [Modèle de base de données](#database-template)
* [Modèle de serveurs d’applications](#application-servers-template)

Les sections suivantes fournissent plus de détails sur les modèles et les paramètres que vous devez fournir dans les modèles.

### <a name="ASCS-SCS-template"></a> Modèle ASCS/SCS

Le modèle ASCS/SCS déploie deux machines virtuelles que vous pouvez utiliser pour créer un cluster de basculement Windows hébergeant plusieurs instances ASCS/SCS.

Pour définir le modèle multi-SID ASCS/SCS, dans le [modèle multi-SID ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] ou [le modèle multi-SID ASCS/SCS à l’aide des disques gérés][sap-templates-3-tier-multisid-xscs-marketplace-image-md], entrez des valeurs pour les paramètres suivants :

  - **Préfixe de ressource** : Définissez le préfixe de ressource servant à attribuer un préfixe à toutes les ressources créées lors du déploiement. Comme les ressources n’appartiennent pas à un seul système SAP, le préfixe de la ressource n’est pas le SID d’un système SAP.  Le préfixe doit contenir entre trois et six caractères.
  - **Type de pile** : Sélectionnez le type de pile du système SAP. Selon le type de pile, Azure Load Balancer a une adresse IP privée (ABAP ou Java uniquement) ou deux adresses IP privées (ABAP + Java) par système SAP.
  -  **Type de système d’exploitation** : Sélectionnez le système d’exploitation des machines virtuelles.
  -  **Nombre de systèmes SAP** : Sélectionnez le nombre de systèmes SAP que vous souhaitez installer dans ce cluster.
  -  **Disponibilité du système** : Sélectionnez **HA** (haute disponibilité).
  -  **Nom d’utilisateur et mot de passe d’administrateur** : Créez un utilisateur pouvant être utilisé pour ouvrir une session sur la machine.
  -  **Sous-réseau nouveau ou existant** : Déterminez si un réseau virtuel et un sous-réseau doivent être créés ou si un sous-réseau existant doit être utilisé. Si vous disposez déjà d’un réseau virtuel connecté à votre réseau local, sélectionnez **existant**.
  -  **ID du sous-réseau** : Définissez l’ID du sous-réseau auquel les machines virtuelles doivent être connectées. Sélectionnez le sous-réseau de votre VPN ou réseau virtuel ExpressRoute à utiliser pour connecter la machine virtuelle à votre réseau local. L'ID ressemble généralement à ceci :

   /subscriptions/\<ID_abonnement\>/resourceGroups/\<Nom_groupe_ressources\>/providers/Microsoft.Network/virtualNetworks/\<nom_réseau_virtuel\>/subnets/\<nom_sous_réseau\>

Le modèle déploie une instance d’équilibrage de charge Azure qui prend en charge plusieurs systèmes SAP :

- Les instances ASCS sont configurées pour les numéros 00, 10, 20...
- Les instances SCS sont configurées pour les numéros 01, 11, 21...
- Les instances ASCS Enqueue Replication Server (ERS) (Linux uniquement) sont configurées pour les numéros 02, 12, 22...
- Les instances SCS ERS (Linux uniquement) sont configurées pour les numéros 03, 13, 23...

L’équilibrage de charge contient 1 adresse IP virtuelle (2 pour Linux), 1 adresse IP virtuelle ASCS/SCS et 1 adresse IP virtuelle pour ERS (Linux uniquement).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Ports SAP ASCS/SCS
La liste suivante contient toutes les règles d’équilibrage de charge (où x représente le numéro du système SAP, par exemple, 1, 2, 3...) :
- Ports spécifiques de Windows pour chaque système SAP 445, 5985
- Ports ASCS (numéro d’instance x0) : 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Ports SCS (numéro d’instance x1) : 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Ports ASCS ERS sous Linux (numéro d’instance x2) : 33x2, 5x213, 5x214, 5x216
- Ports SCS ERS sous Linux (numéro d’instance x3) : 33x3, 5x313, 5x314, 5x316

L’équilibrage de charge est configuré pour utiliser les ports de sondage suivants (x étant le numéro du système SAP, par exemple 1, 2, 3...) :
- Port de sondage d’équilibrage de charge interne ASCS/SCS : 620x0
- Port de sondage d’équilibrage de charge interne ERS (Linux uniquement) : 621x2

### <a name="database-template"></a> Modèle de base de données

Le modèle de base de données déploie une ou deux machines virtuelles que vous pouvez utiliser pour installer le système de gestion de base de données relationnelle (SGBDR) pour un système SAP. Par exemple, si vous déployez un modèle ASCS/SCS pour cinq systèmes SAP, vous devez déployer ce modèle cinq fois.

Pour définir le modèle multi-SID de bases de données, dans le [modèle multi-SID de bases de données][sap-templates-3-tier-multisid-db-marketplace-image] ou [le modèle multi-SID de bases de données à l’aide de Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md], entrez des valeurs pour les paramètres suivants :

  -  **ID du système SAP** : Entrez l’ID du système SAP que vous souhaitez installer. Cet ID est utilisé comme préfixe pour les ressources déployées.
  -  **Type de système d’exploitation** : Sélectionnez le système d’exploitation des machines virtuelles.
  -  **Dbtype** : Sélectionnez le type de base de données que vous souhaitez installer sur le cluster. Sélectionnez **SQL** si vous souhaitez installer Microsoft SQL Server. Sélectionnez **HANA** si vous prévoyez d’installer SAP HANA sur les machines virtuelles. Assurez-vous de sélectionner le type de système d’exploitation correct. Sélectionnez **Windows** pour SQL, sélectionnez une distribution Linux pour HANA. Azure Load Balancer connecté aux machines virtuelles est configuré pour prendre en charge le type de base de données sélectionné :
    * **SQL** : L’équilibreur de charge utilise le port 1433. Veillez à utiliser ce port pour la configuration de SQL Server AlwaysOn.
    * **HANA** : L’équilibreur de charge utilise les ports 35015 et 35017. Veillez à installer SAP HANA avec l’instance numéro **50**.
    L’équilibreur de charge utilise le port de sondage 62550.
  -  **Taille du système SAP** : Définissez le nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont requis par le système, demandez à votre partenaire technologique SAP ou un intégrateur système.
  -  **Disponibilité du système** : Sélectionnez **HA** (haute disponibilité).
  -  **Nom d’utilisateur et mot de passe d’administrateur** : Créez un utilisateur pouvant être utilisé pour ouvrir une session sur la machine.
  -  **ID du sous-réseau** : Entrez l’ID du sous-réseau que vous avez utilisé lors du déploiement du modèle ASCS/SCS ou l’ID du sous-réseau créé dans le cadre du déploiement du modèle ASCS/SCS.

### <a name="application-servers-template"></a> Modèle de serveurs d’applications

Le modèle de serveurs d’applications déploie plusieurs machines virtuelles utilisables comme des instances de serveurs d’applications SAP pour un système SAP. Par exemple, si vous déployez un modèle ASCS/SCS pour cinq systèmes SAP, vous devez déployer ce modèle cinq fois.

Pour définir le modèle multi-SID des serveurs d’application, dans le [modèle multi-SID des serveurs d’application][sap-templates-3-tier-multisid-apps-marketplace-image] ou [le modèle multi-SID des serveurs d’application à l’aide de Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], entrez les valeurs des paramètres suivants :

  -  **ID du système SAP** : Entrez l’ID du système SAP que vous souhaitez installer. Cet ID est utilisé comme préfixe pour les ressources déployées.
  -  **Type de système d’exploitation** : Sélectionnez le système d’exploitation des machines virtuelles.
  -  **Taille du système SAP** : Nombre de SAP fournis par le nouveau système. Si vous ne savez pas combien de SAP sont requis par le système, demandez à votre partenaire technologique SAP ou un intégrateur système.
  -  **Disponibilité du système** : Sélectionnez **HA** (haute disponibilité).
  -  **Nom d’utilisateur et mot de passe d’administrateur** : Créez un utilisateur pouvant être utilisé pour ouvrir une session sur la machine.
  -  **ID du sous-réseau** : Entrez l’ID du sous-réseau que vous avez utilisé lors du déploiement du modèle ASCS/SCS ou l’ID du sous-réseau créé dans le cadre du déploiement du modèle ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Réseau virtuel Azure
Dans notre exemple, l’espace d’adressage de l’instance du réseau virtuel Azure est 10.0.0.0/16. Il existe un sous-réseau appelé Subnet (Sous-réseau), avec la plage d’adresses 10.0.0.0/24. L’ensemble des machines virtuelles et des équilibrages de charge internes sont déployés au sein de ce réseau virtuel.

> [!IMPORTANT]
> N’apportez aucune modification aux paramètres réseau dans le système d’exploitation invité (adresses IP, serveurs DNS, sous-réseau, etc.). Configurez tous vos paramètres réseau dans Azure. Ils sont propagés par le service DHCP.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>Adresses IP DNS

Pour définir les adresses IP DNS requises, effectuez les étapes suivantes :

1.  Dans le volet **Serveurs DNS** du portail Azure, assurez-vous que l’option **Serveurs DNS** de votre réseau virtuel est définie sur **DNS personnalisé**.
2.  Sélectionnez les paramètres en fonction de votre type de réseau. Pour plus d’informations, consultez les ressources suivantes :
    * [Connectivité de réseau d’entreprise (intersite)][planning-guide-2.2] : ajoutez les adresses IP des serveurs DNS locaux.  
    Vous pouvez étendre les serveurs DNS locaux aux machines virtuelles exécutées dans Azure. Dans ce scénario, vous pouvez ajouter l’adresse IP des machines virtuelles Azure sur lesquelles vous exécutez le service DNS.
    * [Déploiement dans le cloud uniquement][planning-guide-2.1] : déployez une machine virtuelle supplémentaire dans l’instance de réseau virtuel jouant le rôle de serveur DNS. Ajoutez l’adresse IP des machines virtuelles Azure que vous avez configurées pour exécuter le service DNS.

    ![Figure 2 : Configurer les serveurs DNS pour le réseau virtuel Azure][sap-ha-guide-figure-3001]

    _**Figure 2 :** Configurer les serveurs DNS pour le réseau virtuel Azure_

  > [!NOTE]
  > Si vous modifiez l’adresse IP des serveurs DNS, vous devez redémarrer les machines virtuelles Azure afin d’appliquer la modification et de propager les nouveaux serveurs DNS.
  >
  >

Dans notre exemple, le service DNS est installé et configuré sur les machines virtuelles Windows suivantes :

| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique |
| --- | --- | --- | --- |
| Premier serveur DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Deuxième serveur DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Noms d’hôtes et adresses IP statiques de l’instance en cluster SAP ASCS/SCS et l’instance en cluster SGBD

Pour un déploiement local, vous avez besoin des noms d’hôtes et adresses IP réservés suivants :

| Rôle du nom d’hôte virtuel | Nom d’hôte virtuel | Adresse IP statique virtuelle |
| --- | --- | --- |
| Nom d’hôte virtuel du premier cluster SAP ASCS/SCS (pour la gestion du cluster) |pr1-ascs-vir |10.0.0.42 |
| Nom d’hôte virtuel de l’instance SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nom d’hôte virtuel du deuxième cluster SAP SGBD (gestion du cluster) |pr1-dbms-vir |10.0.0.32 |

Lorsque vous créez le cluster, créez les noms d’hôtes virtuels pr1-ASC-vir et pr1-SGBD-vir, ainsi que les adresses IP associées qui gèrent le cluster lui-même. Pour plus d’informations sur la procédure à suivre, consultez [Collecter les nœuds de cluster dans une configuration de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Vous pouvez créer manuellement les deux autres noms d’hôtes virtuels, pr1 ASC sap et pr1 SGBD sap, ainsi que les adresses IP associées, sur le serveur DNS. L’instance SAP ASCS/SCS en cluster et l’instance SGBD en cluster utilisent ces ressources. Pour plus d’informations sur la procédure à suivre, consultez [Créer un nom d’hôte virtuel pour une instance SAP ASCS/SCS en cluster][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Définir des adresses IP statiques pour les machines virtuelles SAP
Après avoir déployé les machines virtuelles à utiliser dans votre cluster, vous devez définir des adresses IP statiques pour l’ensemble des machines virtuelles. Effectuez cette opération dans la configuration du réseau virtuel Azure et non dans le système d’exploitation invité.

1.  Dans le portail Azure, sélectionnez **Groupe de ressources** > **Carte réseau** > **Paramètres** > **Adresse IP**.
2.  Dans le volet **Adresses IP**, sous **Affectation**, sélectionnez **Statique**. Dans la zone **Adresse IP**, entrez l’adresse IP à utiliser.

  > [!NOTE]
  > Si vous modifiez l’adresse IP de la carte réseau, vous devez redémarrer les machines virtuelles Azure pour que la modification prenne effet.  
  >
  >

  ![Figure 3 : Définir des adresses IP statiques pour la carte réseau de chaque machine virtuelle][sap-ha-guide-figure-3002]

  _**Figure 3 :** Définir des adresses IP statiques pour la carte réseau de chaque machine virtuelle_

  Répétez cette étape pour toutes les interfaces réseau, c’est-à-dire pour toutes les machines virtuelles, y compris celles que vous souhaitez utiliser pour votre service Active Directory ou DNS.

Dans notre exemple, nous avons les machines virtuelles et les adresses IP statiques suivantes :

| Rôle de la machine virtuelle | Nom d’hôte de la machine virtuelle | Nom de la carte réseau | Adresse IP statique |
| --- | --- | --- | --- |
| Première instance du serveur d’applications SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Deuxième instance du serveur d’applications SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Dernière instance du serveur d’applications SAP serveur d’applications SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Premier nœud de cluster pour l’instance ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Deuxième nœud de cluster pour l’instance ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Premier nœud de cluster pour l’instance SGBD |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Deuxième nœud de cluster pour l’instance SGBD |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Définir une adresse IP statique pour l’équilibrage de charge interne Azure

Le modèle Azure Resource Manager SAP crée un équilibrage de charge interne Azure utilisé pour le cluster de l’instance SAP ASCS/SCS et le cluster SGBD.

> [!IMPORTANT]
> L’adresse IP du nom d’hôte virtuel de l’instance SAP ASCS/SCS est identique à celle de l’équilibrage de charge interne SAP ASCS/SCS : pr1-lb-ascs.
> L’adresse IP du nom virtuel du SGBD est identique à celle de l’équilibrage de charge interne du SGBD SAP : pr1-lb-dbms.
>
>

Pour définir une adresse IP statique pour l’équilibrage de charge interne Azure :

1.  Le déploiement initial définit l’adresse IP de l’équilibrage de charge interne sur **Dynamique**. Dans le portail Azure, dans le volet **Adresses IP**, sous **Affectation**, sélectionnez **Statique**.
2.  Définissez l’adresse IP de l’équilibrage de charge interne **pr1-lb-ascs** comme l’adresse IP du nom d’hôte virtuel de l’instance SAP ASCS/SCS.
3.  Définissez l’adresse IP de l’équilibrage de charge interne **pr1-lb-dbms** sur l’adresse IP du nom d’hôte virtuel de l’instance SGBD.

  ![Figure 4 : Définir une adresse IP statique pour l’équilibreur de charge interne de l’instance SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Figure 4 :** Définir une adresse IP statique pour l’équilibreur de charge interne de l’instance SAP ASCS/SCS_

Dans notre exemple, nous avons deux équilibrages de charge internes Azure qui présentent les adresses IP statiques suivantes :

| Rôle de l’équilibrage de charge interne Azure | Nom de l’équilibrage de charge interne Azure | Adresse IP statique |
| --- | --- | --- |
| Équilibrage de charge interne de l’instance SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Équilibrage de charge interne du SGBD SAP |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure

Le modèle Azure Resource Manager SAP crée les ports dont vous avez besoin :
* Une instance ABAP ASCS, avec le numéro d’instance par défaut 00
* Une instance Java SCS, avec le numéro d’instance par défaut 01

Lorsque vous installez votre instance SAP ASCS/SCS, vous devez utiliser le numéro d’instance par défaut 00 pour votre instance ABAP ASCS et le numéro d’instance par défaut 01 pour votre instance Java SCS.

Ensuite, créez les points de terminaison d’équilibrage de charge internes requis pour les ports SAP NetWeaver.

Pour créer les points de terminaison d’équilibrage de charge interne requis, créez tout d’abord ces points pour les ports SAP NetWeaver ABAP ASC :

| Service/nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance ASCS avec numéro d’instance 00) (ERS avec 10) |
| --- | --- | --- |
| Serveur de file d’attente / *lbrule3200* |32\<Numéro d’instance\> |3200 |
| Serveur de messages ABAP / *lbrule3600* |36\<Numéro d’instance\> |3600 |
| Message ABAP interne / *lbrule3900* |39\<Numéro d’instance\> |3900 |
| Serveur de messages HTTP / *Lbrule8100* |81\<Numéro d’instance\> |8100 |
| Service de démarrage SAP ASCS HTTP / *Lbrule50013* |5\<Numéro d’instance\>13 |50013 |
| Service de démarrage SAP ASCS HTTPS / *Lbrule50014* |5\<Numéro d’instance\>14 |50014 |
| Réplication de la file d’attente / *Lbrule50016* |5\<Numéro d’instance\>16 |50016 |
| Service de démarrage SAP ERS HTTP / *Lbrule51013* |5\<Numéro d’instance\>13 |51013 |
| Service de démarrage SAP ERS HTTP / *Lbrule51014* |5\<Numéro d’instance\>14 |51014 |
| Windows Remote Management (WinRM) *Lbrule5985* | |5985 |
| Partage de fichiers *Lbrule445* | |445 |

**Tableau 1 :** Numéro de port des instances SAP NetWeaver ABAP ASCS

Ensuite, créez ces points de terminaison d’équilibrage de charge pour les ports SAP NetWeaver Java SCS :

| Service/nom de la règle d’équilibrage de charge | Numéros de ports par défaut | Ports concrets pour (instance SCS avec numéro d’instance 01) (ERS avec 11) |
| --- | --- | --- |
| Serveur de file d’attente / *lbrule3201* |32\<Numéro d’instance\> |3201 |
| Serveur de passerelle / *lbrule3301* |33\<Numéro d’instance\> |3301 |
| Serveur de messages Java / *lbrule3900* |39\<Numéro d’instance\> |3901 |
| Serveur de messages HTTP / *Lbrule8101* |81\<Numéro d’instance\> |8101 |
| Service de démarrage SAP SCS HTTP / *Lbrule50113* |5\<Numéro d’instance\>13 |50113 |
| Service de démarrage SAP SCS HTTPS / *Lbrule50114* |5\<Numéro d’instance\>14 |50114 |
| Réplication de la file d’attente / *Lbrule50116* |5\<Numéro d’instance\>16 |50116 |
| Service de démarrage SAP ERS HTTP / *Lbrule51113* |5\<Numéro d’instance\>13 |51113 |
| Service de démarrage SAP ERS HTTP / *Lbrule51114* |5\<Numéro d’instance\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Partage de fichiers *Lbrule445* | |445 |

**Tableau 2 :** Numéro de port des instances SAP NetWeaver Java SCS

![Figure 5 : Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibreur de charge interne Azure][sap-ha-guide-figure-3004]

_**Figure 5 :** Règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibreur de charge interne Azure_

Définissez l’adresse IP de l’équilibrage de charge pr1-lb-dbms sur l’adresse IP du nom d’hôte virtuel de l’instance SGBD.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure

Si vous souhaitez utiliser d’autres numéros pour les instances SAP ASCS ou SCS, vous devez modifier les noms et valeurs par défaut de ces ports.

1.  Dans le portail Azure, sélectionnez **\<SID\>-lb-ascs load balancer** > **Règles d’équilibrage de charge**.
2.  Pour toutes les règles d’équilibrage de charge qui appartiennent à l’instance SAP ASCS ou SCS, modifiez les valeurs suivantes :

  * Nom
  * Port
  * Port principal

  Par exemple, si vous souhaitez remplacer le numéro d’instance ASC par défaut 00 par 31, vous devez modifier tous les ports répertoriés dans le tableau 1.

  Voici un exemple de mise à jour pour le port *lbrule3200*.

  ![Figure 6 : Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibreur de charge interne Azure][sap-ha-guide-figure-3005]

  _**Figure 6 :** Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibreur de charge interne Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Ajouter des machines virtuelles Windows au domaine

Après avoir affecté une adresse IP statique aux machines virtuelles, ajoutez les machines virtuelles au domaine.

![Figure 7 : Ajouter une machine virtuelle à un domaine][sap-ha-guide-figure-3006]

_**Figure 7 :** Ajouter une machine virtuelle à un domaine_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Ajouter des entrées de Registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS

Azure Load Balancer offre un équilibrage de charge interne qui ferme les connexions lorsqu’elles sont inactives pendant un laps de temps défini (délai d’inactivité). Les processus de travail SAP dans les instances de dialogue ouvrent des connexions au processus de mise en file d’attente SAP dès que la première requête de mise en file d’attente/suppression de la file d’attente doit être envoyée. Ces connexions restent généralement établies jusqu’à ce que le processus de travail ou de fil d’attente redémarre. Toutefois, si la connexion est inactive pendant un laps de temps donné, l’équilibrage de charge interne Azure ferme les connexions. Ce n’est pas un problème, car le processus de travail SAP rétablit la connexion au processus de file d’attente si elle n’existe plus. Ces activités sont documentées dans les traces de développement des processus SAP, mais elles créent une quantité importante de contenu supplémentaire dans les traces. Il est conseillé de modifier les paramètres TCP/IP `KeepAliveTime` et `KeepAliveInterval` sur les deux nœuds de cluster. Associez ces modifications des paramètres TCP/IP et les paramètres de profil SAP décrits plus loin dans cet article.

Pour ajouter des entrées de registre aux deux nœuds du cluster de l’instance SAP ASCS/SCS, ajoutez tout d’abord ces entrées de registre Windows aux deux nœuds de cluster Windows pour SAP ASCS/SCS :

| Chemin | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nom de la variable |`KeepAliveTime` |
| Type de variable |REG_DWORD (décimal) |
| Valeur |120 000 |
| Lien vers la documentation |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tableau 3 :** Modification du premier paramètre TCP/IP

Puis, ajoutez cette entrée de registre Windows aux deux nœuds de cluster Windows pour l’instance SAP ASCS/SCS :

| Chemin | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nom de la variable |`KeepAliveInterval` |
| Type de variable |REG_DWORD (décimal) |
| Valeur |120 000 |
| Lien vers la documentation |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

**Tableau 4 :** Modification du deuxième paramètre TCP/IP

Pour appliquer les modifications, redémarrez les deux nœuds de cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurer un cluster de basculement Windows Server pour une instance SAP ASCS/SCS

La configuration d’un cluster de basculement Windows Server pour une instance SAP ASCS/SCS implique les tâches suivantes :

- Collecter les nœuds de cluster dans une configuration de cluster.
- Configurer un témoin de partage de fichiers de cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Collecter les nœuds de cluster dans une configuration de cluster

1.  Dans l’Assistant Ajout de rôles et de fonctionnalités, ajoutez un clustering de basculement aux deux nœuds de cluster.
2.  Configurez le cluster de basculement à l’aide du Gestionnaire du cluster de basculement. Dans le Gestionnaire du cluster de basculement, cliquez sur **Créer un cluster**, puis ajoutez uniquement le nom du premier nœud de cluster (A). N’ajoutez pas le second nœud maintenant, vous le ferez lors d’une étape ultérieure.

  ![Figure 8 : Ajouter le nom de serveur ou de machine virtuelle du premier nœud de cluster][sap-ha-guide-figure-3007]

  _**Figure 8 :** Ajouter le nom de serveur ou de machine virtuelle du premier nœud de cluster_

3.  Entrez le nom de réseau (nom d’hôte virtuel) du cluster.

  ![Figure 9 : Entrer le nom du cluster][sap-ha-guide-figure-3008]

  _**Figure 9 :** Entrer le nom du cluster_

4.  Une fois le cluster créé, exécutez un test de validation du cluster.

  ![Figure 10 : Exécuter le test de validation de cluster][sap-ha-guide-figure-3009]

  _**Figure 10 :** Exécuter le test de validation de cluster_

  Vous pouvez ignorer les avertissements concernant des disques à ce stade de la procédure. Vous ajouterez un témoin de partage de fichiers et les disques partagés SIOS ultérieurement. À ce stade, le fait de ne pas disposer d’un disque quorum n’est pas un problème.

  ![Figure 11 : Aucun disque quorum n’a été trouvé][sap-ha-guide-figure-3010]

  _**Figure 11 :** Aucun disque quorum n’a été trouvé_

  ![Figure 12 : Une nouvelle adresse IP est requise pour la ressource principale du cluster][sap-ha-guide-figure-3011]

  _**Figure 12 :** Une nouvelle adresse IP est requise pour la ressource principale du cluster_

5.  Modifiez l’adresse IP du service principal du cluster. Le cluster ne peut pas démarrer tant que vous n’avez pas modifié l’adresse IP du service principal du cluster car l’adresse IP du serveur pointe vers un des nœuds de machine virtuelle. Effectuez cette opération dans la page **Propriétés** de la ressource IP du service principal du cluster.

  Par exemple, vous devez affecter une adresse IP (dans notre exemple, 10.0.0.42) au nom d’hôte virtuel du cluster pr1-ascs-vir.

  ![Figure 13 : Dans la boîte de dialogue Propriétés, modifier l’adresse IP][sap-ha-guide-figure-3012]

  _**Figure 13 :** Dans la boîte de dialogue **Propriétés**, modifier l’adresse IP_

  ![Figure 14 : Affecter l’adresse IP réservée pour le cluster][sap-ha-guide-figure-3013]

  _**Figure 14 :** Affecter l’adresse IP réservée pour le cluster_

6.  Mettez en ligne le nom de l’hôte virtuel du cluster.

  ![Figure 15 : Le service principal du cluster est opérationnel et son adresse IP correcte][sap-ha-guide-figure-3014]

  _**Figure 15 :** Le service principal du cluster est opérationnel et son adresse IP correcte_

7.  Ajoutez le deuxième nœud de cluster.

  Maintenant que le service principal du cluster est opérationnel, vous pouvez ajouter le deuxième nœud du cluster.

  ![Figure 16 : Ajouter le deuxième nœud de cluster][sap-ha-guide-figure-3015]

  _**Figure 16 :** Ajouter le deuxième nœud de cluster_

8.  Entrez un nom pour le second hôte du nœud de cluster.

  ![Figure 17 : Entrer le nom du second hôte du nœud de cluster][sap-ha-guide-figure-3016]

  _**Figure 17 :** Entrer le nom du second hôte du nœud de cluster_

  > [!IMPORTANT]
  > Assurez-vous que la case à cocher **Ajouter la totalité du stockage disponible au cluster** n’est *pas* sélectionnée.  
  >
  >

  ![Figure 18 : Ne pas activer la case][sap-ha-guide-figure-3017]

  _**Figure 18 :** Ne *pas* activer la case_

  Vous pouvez ignorer les avertissements concernant le quorum et les disques. Vous définirez le quorum et partagerez le disque ultérieurement, comme indiqué dans [Installer SIOS DataKeeper Cluster Edition pour un disque de partage en cluster SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![Figure 19 : Ignorer les avertissements concernant le disque quorum][sap-ha-guide-figure-3018]

  _**Figure 19 :** Ignorer les avertissements concernant le disque quorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurer un témoin de partage de fichiers de cluster

La configuration d’un témoin de partage de fichiers de cluster implique les tâches suivantes :

- Créer un partage de fichiers.
- Définir le quorum de témoin de partage de fichiers dans le Gestionnaire du cluster de basculement.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Créer un partage de fichiers

1.  Sélectionnez un témoin de partage de fichiers plutôt qu’un disque quorum. SIOS DataKeeper prend en charge cette option.

  Dans les exemples de cet article, le témoin de partage de fichiers se trouve sur le serveur Active Directory ou DNS exécuté dans Azure. Le témoin de partage de fichiers porte le nom domcontr-0. Comme vous avez configuré une connexion VPN à Azure (via une passerelle VPN ou Azure ExpressRoute), votre service Active Directory ou DNS est local et n’est pas adapté à l’exécution d’un témoin de partage de fichiers.

  > [!NOTE]
  > Si votre service Active Directory ou DNS s’exécute uniquement en local, ne configurez pas votre témoin de partage de fichiers sur le système d’exploitation Windows du service Active Directory ou DNS exécuté localement. La latence du réseau entre les nœuds de cluster exécutés dans Azure et le service Active Directory ou DNS local peut être trop importante et entraîner des problèmes de connectivité. Veillez à configurer le témoin de partage de fichiers sur une machine virtuelle Azure proche du nœud de cluster.  
  >
  >

  Au moins 1 024 Mo d’espace libre sont nécessaires pour le disque quorum. Le disque quorum a besoin d’au moins 2 048 Mo d’espace libre.

2.  Ajoutez l’objet nom de cluster.

  ![Figure 20 : Affecter les autorisations sur le partage pour l’objet nom de cluster][sap-ha-guide-figure-3019]

  _**Figure 20 :** Affecter les autorisations sur le partage pour l’objet nom de cluster_

  Assurez-vous que les autorisations incluent la possibilité de modifier des données du partage pour l’objet nom de cluster (dans notre exemple, pr1-ascs-vir$).

3.  Pour ajouter l’objet nom de cluster à la liste, sélectionnez **Ajouter**. Modifiez le filtre de manière à rechercher les objets ordinateurs, en plus de ceux indiqués dans la figure 22.

  ![Figure 21 : Modifier les types d’objet pour inclure les ordinateurs][sap-ha-guide-figure-3020]

  _**Figure 21 :** Modifier les **types d’objet** pour inclure les ordinateurs_

  ![Figure 22 : Cocher la case Ordinateurs][sap-ha-guide-figure-3021]

  _**Figure 22 :** Cocher la case **Ordinateurs**_

4.  Entrez l’objet nom de cluster, comme indiqué dans la figure 21. L’enregistrement étant déjà créé, vous pouvez modifier les autorisations, comme indiqué dans la figure 20.

5.  Sélectionnez l’onglet **Sécurité** du partage, puis définissez des autorisations plus détaillées pour l’objet nom de cluster.

  ![Figure 23 : Définir les attributs de sécurité de l’objet nom de cluster sur le quorum de partage de fichiers][sap-ha-guide-figure-3022]

  _**Figure 23 :** Définir les attributs de sécurité de l’objet nom de cluster sur le quorum de partage de fichiers_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Définir le quorum de témoin de partage de fichiers dans le Gestionnaire du cluster de basculement

1.  Ouvrez l’Assistant Configuration de quorum du cluster.

  ![Figure 24 : Démarrer l’Assistant Configuration de quorum du cluster][sap-ha-guide-figure-3023]

  _**Figure 24 :** Démarrer l’Assistant Configuration de quorum du cluster_

2.  Dans la page **Sélectionner l’option de configuration du quorum**, sélectionnez **Sélectionner le témoin de quorum**.

  ![Figure 25 : Configurations de quorum sélectionnables][sap-ha-guide-figure-3024]

  _**Figure 25 :** Configurations de quorum sélectionnables_

3.  Sur la page **Sélectionner le témoin de quorum**, sélectionnez **Configurer un témoin de partage de fichiers**.

  ![Figure 26 : Sélectionner le témoin de partage de fichiers][sap-ha-guide-figure-3025]

  _**Figure 26 :** Sélectionner le témoin de partage de fichiers_

4.  Entrez le chemin d’accès UNC du partage de fichiers (dans notre exemple, \\domcontr-0\FSW). Pour afficher une liste des modifications possibles, sélectionnez **Suivant**.

  ![Figure 27 : Définir l’emplacement du partage de fichiers du témoin][sap-ha-guide-figure-3026]

  _**Figure 27 :** Définir l’emplacement du partage de fichiers du témoin_

5.  Apportez les modifications souhaitées, puis sélectionnez **Suivant**. Vous devez reconfigurer correctement le cluster, comme indiqué dans la figure 28 :  

  ![Figure 28 : Confirmation de la reconfiguration du cluster][sap-ha-guide-figure-3027]

  _**Figure 28 :** Confirmation de la reconfiguration du cluster_

Après avoir installé le cluster de basculement Windows avec succès, vous devez apporter des modifications à certains seuils pour adapter la détection de basculement aux conditions dans Azure. Les paramètres à changer sont documentés dans : [Tuning Failover Cluster Network Thresholds (Réglage des seuils réseau de cluster de basculement)][tuning-failover-cluster-network-thresholds]. En supposant que les deux machines virtuelles qui constituent la configuration du cluster Windows pour ASCS/SCS se trouvent dans le même sous-réseau, modifiez les paramètres suivants avec ces valeurs :

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ces paramètres ont été testés auprès de clients et offrent un bon compromis. Ils sont suffisamment résilients, mais assurent également un basculement assez rapide dans des conditions d’erreur réelles sur un logiciel SAP ou en cas de défaillance d’un nœud ou d’une machine virtuelle.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installer SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS

Vous disposez maintenant d’une configuration fonctionnelle de clustering de basculement Windows Server dans Azure. Pour installer une instance SAP ASCS/SCS, vous avez besoin d’une ressource de disque partagé. Il n’est pas possible de créer les ressources de disque partagé requises dans Azure. SIOS DataKeeper Cluster Edition est une solution tierce que vous pouvez utiliser pour créer des ressources de disque partagé.

L’installation de SIOS DataKeeper Cluster Edition pour le disque de partage en cluster SAP ASCS/SCS implique les tâches suivantes :

- Ajouter Microsoft .NET Framework 3.5.
- Installer SIOS DataKeeper.
- Configurer SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Ajouter .NET Framework 3.5
.NET Framework 3.5 n’est pas automatiquement activé ou installé dans Windows Server 2012 R2. Comme SIOS DataKeeper requiert .NET sur tous les nœuds où vous installez DataKeeper, vous devez installer .NET Framework 3.5 sur le système d’exploitation invité de toutes les machines virtuelles du cluster.

Il existe deux façons d’ajouter .NET Framework 3.5 :

- Utilisez l’Assistant Ajout de rôles et de fonctionnalités, comme indiqué dans la figure 29 :

  ![Figure 29 : Installer .NET Framework 3.5 à l’aide de l’Assistant Ajout de rôles et de fonctionnalités][sap-ha-guide-figure-3028]

  _**Figure 29 :** Installer .NET Framework 3.5 à l’aide de l’Assistant Ajout de rôles et de fonctionnalités_

  ![Figure 30 : Barre de progression de l’installation de .NET Framework 3.5 affichée dans l’Assistant Ajout de rôles et de fonctionnalités][sap-ha-guide-figure-3029]

  _**Figure 30 :** Barre de progression de l’installation de .NET Framework 3.5 affichée dans l’Assistant Ajout de rôles et de fonctionnalités_

- Utilisez l’outil en ligne de commande dism.exe. Pour ce type d’installation, vous devez accéder au répertoire SxS du média d’installation Windows. Dans une invite de commandes avec élévation de privilèges, entrez cette commande :

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installer SIOS DataKeeper

Installez SIOS DataKeeper Cluster Edition sur chaque nœud du cluster. Pour créer un stockage partagé virtuel avec SIOS DataKeeper, créez un miroir synchronisé, puis simulez le stockage partagé en cluster.

Avant d’installer le logiciel SIOS, créez l’utilisateur de domaine DataKeeperSvc.

> [!NOTE]
> Ajoutez l’utilisateur de domaine DataKeeperSvc au groupe Administrateur local sur les deux nœuds de cluster.
>
>

Pour installer SIOS DataKeeper :

1.  Installez le logiciel SIOS sur les deux nœuds de cluster.

  ![Programme d’installation de SIOS][sap-ha-guide-figure-3030]

  ![Figure 31 : Première page de l’installation de SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Figure 31 :** Première page de l’installation de SIOS DataKeeper_

2.  Dans la boîte de dialogue, sélectionnez **Oui**.

  ![Figure 32 : DataKeeper vous indique qu’un service va être désactivé][sap-ha-guide-figure-3032]

  _**Figure 32 :** DataKeeper vous indique qu’un service va être désactivé_

3.  Dans la boîte de dialogue, nous vous recommandons de sélectionner **Compte de domaine ou de serveur**.

  ![Figure 33 : Sélection de l’utilisateur de SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Figure 33 :** Sélection de l’utilisateur de SIOS DataKeeper_

4.  Entrez le nom et le mot de passe de l’utilisateur de compte de domaine que vous avez créés pour SIOS DataKeeper.

  ![Figure 34 : Entrer le nom et le mot de passe de l’utilisateur du domaine pour l’installation de SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Figure 34 :** Entrer le nom et le mot de passe de l’utilisateur du domaine pour l’installation de SIOS DataKeeper_

5.  Installez la clé de licence de votre instance SIOS DataKeeper, comme indiqué dans la figure 35.

  ![Figure 35 : Entrer votre clé de licence SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Figure 35 :** Entrer votre clé de licence SIOS DataKeeper_

6.  À l’invite, redémarrez la machine virtuelle.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurer SIOS DataKeeper

Après avoir installé SIOS DataKeeper sur les deux nœuds, commencez la configuration. L’objectif de la configuration est de mettre en place une réplication synchrone des données entre les disques supplémentaires attachés à chacune des machines virtuelles.

1.  Démarrez l’outil de configuration et de gestion de DataKeeper, puis sélectionnez **Connect Server** (Connexion au serveur)

  ![Figure 36 : Outil de configuration et de gestion de SIOS DataKeeper][sap-ha-guide-figure-3036]

  _**Figure 36 :** Outil de configuration et de gestion de SIOS DataKeeper_

2.  Insérez le nom ou l’adresse TCP/IP du premier nœud auquel l’outil de gestion et de configuration doit se connecter, puis du second nœud.

  ![Figure 37 : Insérer le nom ou l’adresse TCP/IP du premier nœud auquel l’outil de gestion et de configuration doit se connecter, puis du second nœud][sap-ha-guide-figure-3037]

  _**Figure 37 :** Insérer le nom ou l’adresse TCP/IP du premier nœud auquel l’outil de gestion et de configuration doit se connecter, puis du second nœud_

3.  Créez le travail de réplication entre les deux nœuds.

  ![Figure 38 : Créer un travail de réplication][sap-ha-guide-figure-3038]

  _**Figure 38 :** Créer un travail de réplication_

  Un assistant vous guide à travers le processus de création d’un travail de réplication.

4.  Définissez le nom du travail de réplication.

  ![Figure 39 : Définir le nom du travail de réplication][sap-ha-guide-figure-3039]

  _**Figure 39 :** Définir le nom du travail de réplication_

  ![Figure 40 : Définir les données de base du nœud qui doit être le nœud source actuel][sap-ha-guide-figure-3040]

  _**Figure 40 :** Définir les données de base du nœud qui doit être le nœud source actuel_

5.  Définissez le nom, l’adresse TCP/IP et le volume de disque du nœud cible.

  ![Figure 41 : Définir le nom, l’adresse TCP/IP et le volume de disque du nœud cible actuel][sap-ha-guide-figure-3041]

  _**Figure 41 :** Définir le nom, l’adresse TCP/IP et le volume de disque du nœud cible actuel_

6.  Définissez les algorithmes de compression. Dans notre exemple, nous vous recommandons de compresser le flux de réplication. Dans les situations de resynchronisation notamment, la compression du flux de réplication accélère considérablement la resynchronisation. La compression utilise les ressources de processeur et de RAM d’une machine virtuelle. Plus le taux de compression augmente, plus le volume de ressources de processeur utilisées est important. Vous pouvez ajuster ce paramètre ultérieurement.

7.  Vous devez également vérifier si la réplication se fait de façon asynchrone ou synchrone. Lorsque vous protégez des configurations SAP ASCS/SCS, vous devez utiliser la réplication synchrone.  

  ![Figure 42 : Définir les détails de la réplication][sap-ha-guide-figure-3042]

  _**Figure 42 :** Définir les détails de la réplication_

8.  Définissez si le volume répliqué par le travail de réplication doit être représenté auprès d’une configuration de cluster de basculement Windows Server en tant que disque partagé. Pour la configuration SAP ASCS/SCS, sélectionnez **Oui** pour que le cluster Windows voie le volume répliqué en tant que disque partagé utilisable comme volume de cluster.

  ![Figure 43 : Sélectionner Oui pour activer le volume répliqué en tant que volume de cluster][sap-ha-guide-figure-3043]

  _**Figure 43 :** Sélectionner **Oui** pour activer le volume répliqué en tant que volume de cluster_

  Une fois le volume créé, l’outil de configuration et de gestion de DataKeeper indique que le travail de réplication est actif.

  ![Figure 44 : La mise en miroir synchrone de DataKeeper pour le disque de partage SAP ASCS/SCS est active][sap-ha-guide-figure-3044]

  _**Figure 44 :** La mise en miroir synchrone de DataKeeper pour le disque de partage SAP ASCS/SCS est active_

  Le Gestionnaire du cluster de basculement affiche maintenant le disque en tant que disque DataKeeper, comme indiqué dans la figure 45 :

  ![Figure 45 : Le Gestionnaire du cluster de basculement affiche le disque répliqué par DataKeeper][sap-ha-guide-figure-3045]

  _**Figure 45 :** Le Gestionnaire du cluster de basculement affiche le disque répliqué par DataKeeper_

## <a name="next-steps"></a>Étapes suivantes

* [Installer la haute disponibilité SAP NetWeaver à l’aide d’un cluster de basculement Windows et d’un disque partagé pour une instance SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
