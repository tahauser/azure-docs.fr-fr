---
title: "Installer la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un disque partagé pour une instance SAP ASCS/SCS dans Azure | Microsoft Docs"
description: "Découvrez comment installer la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un disque partagé pour une instance SAP ASCS/SCS."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 419bbdd57a391dbbf01c2110a1609cb3d0ded003
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Installer la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un disque partagé pour une instance SAP ASCS/SCS dans Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

Cet article explique comment installer et configurer un système SAP à haute disponibilité dans Azure à l’aide d’un cluster de basculement Windows Server et d’un disque partagé de cluster pour le clustering d’une instance SAP ASCS/SCS.

## <a name="prerequisites"></a>Prérequis

Avant de commencer l’installation, consultez ces documents :

* [Guide d’architecture : Clustering d’une instance SAP ASCS/SCS sur un cluster de basculement Windows à l’aide d’un disque partagé de cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Préparer l’infrastructure Azure pour la haute disponibilité SAP à l’aide d’un cluster de basculement Windows et d’un disque partagé pour une instance SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Nous ne décrivons pas l’installation du système SGBD dans cet article, car celle-ci varie en fonction du système utilisé. Nous partons du principe que la haute disponibilité du SGBD est assurée par les fonctionnalités prises en charge par les différents fournisseurs de SGBD pour Azure, par exemple AlwaysOn ou la mise en miroir de base de données pour SQL Server, et Oracle Data Guard pour les bases de données Oracle. Dans le scénario utilisé dans cet article, nous n’ajoutons pas de protection supplémentaire au SGBD.

Il n’existe pas de considérations particulières lorsque différents services de SGBD interagissent avec une configuration SAP ASCS ou SCS en cluster dans Azure.

> [!NOTE]
> Les procédures d’installation de systèmes SAP NetWeaver ABAP, Java et ABAP+Java sont presque identiques. La différence la plus notable est qu’un système SAP ABAP comprend une instance ASCS, le système SAP Java une instance SCS, et le système SAP ABAP+Java une instance ASCS et une instance SCS exécutées dans le même groupe de cluster de basculement Microsoft. Toute différence d’installation pour chaque pile d’installation de SAP NetWeaver est mentionnée explicitement. Le reste de la procédure est identique.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installer SAP avec une instance ASCS/SCS à haute disponibilité

> [!IMPORTANT]
> Veillez à ne pas placer votre fichier d’échange sur des volumes SIOS DataKeeper mis en miroir. DataKeeper ne prend pas en charge les volumes mis en miroir. Vous pouvez laisser votre fichier d’échange sur le lecteur temporaire D d’une machine virtuelle (valeur par défaut). S’il ne s’y trouve pas déjà, déplacez le fichier d’échange Windows sur le lecteur D de votre machine virtuelle Azure.
>
>

L’installation de SAP avec une instance ASCS/SCS à haute disponibilité implique les tâches suivantes :

* Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster.
* Installer le premier nœud de cluster SAP.
* Modifier le profil SAP de l’instance ASCS/SCS.
* Ajouter un port de sondage.
* Ouvrir le port de sondage du Pare-feu Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Créer un nom d’hôte virtuel pour l’instance SAP ASCS/SCS en cluster

1.  Dans le Gestionnaire DNS Windows, créez une entrée DNS pour le nom d’hôte virtuel de l’instance ASC/SCS.

  > [!IMPORTANT]
  > L’adresse IP que vous affectez au nom d’hôte virtuel de l’instance ASC/SCS doit être identique à celle que vous avez affectée à l’équilibrage de charge Azure Load Balancer (\<SID\>-lb-ascs).  
  >
  >

  L’adresse IP du nom d’hôte SAP ASCS/SCS virtuel (pr1-ascs-sap) est identique à celle de l’équilibrage de charge Azure Load Balancer (pr1-lb-ascs).

  ![Figure 1 : Définir l’entrée DNS pour le nom virtuel et l’adresse TCP/IP du cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

  _**Figure 1 :** Définir l’entrée DNS pour le nom virtuel et l’adresse TCP/IP du cluster SAP ASCS/SCS_

2.  Pour définir l’adresse IP affectée au nom d’hôte virtuel, sélectionnez **Gestionnaire DNS** > **Domaine**.

  ![Figure 2 : Nouveau nom virtuel et nouvelle adresse TCP/IP de la configuration du cluster SAP ASCS/SCS][sap-ha-guide-figure-3047]

  _**Figure 2 :** Nouveau nom virtuel et nouvelle adresse TCP/IP de la configuration du cluster SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installer le premier nœud de cluster SAP

1.  Exécutez l’option du premier nœud de cluster sur le nœud A du cluster, par exemple sur pr1-ascs-0*host.
2.  Pour conserver les ports par défaut pour l’équilibrage de charge interne Azure, sélectionnez :

  * **Système ABAP** : numéro d’instance **ASCS****00**
  * **Système Java** : numéro d’instance **SCS****01**
  * **Système ABAP+Java** : numéro d’instance **ASCS****00** et numéro d’instance **SCS****01**

  Pour utiliser des numéros d’instance autres que 00 pour l’instance ABAP ASCS et que 01 pour l’instance Java SCS, commencez par modifier les règles d’équilibrage de charge par défaut de l’équilibreur de charge interne Azure. Pour plus d’informations, consultez [Modifier les règles d’équilibrage de charge ASCS/SCS par défaut pour l’équilibrage de charge interne Azure][sap-ha-guide-8.9].

Les quelques étapes suivantes ne sont pas décrites dans la documentation d’installation de SAP standard.

> [!NOTE]
> La documentation d’installation de SAP explique comment installer le premier nœud de cluster ASC/SCS.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modifier le profil SAP de l’instance ASCS/SCS

Tout d’abord, ajoutez un nouveau paramètre de profil. Ce paramètre de profil empêche les connexions entre les processus de travail SAP et le serveur de mise en file d’attente de se fermer lorsqu’elles sont inactives pendant trop longtemps. Nous mentionnons ce scénario problématique dans la section [Ajouter des entrées de registre aux deux nœuds de cluster de l’instance SAP ASCS/SCS][sap-ha-guide-8.11]. Dans cette section, nous apportons également deux modifications à certains paramètres de connexion TCP/IP de base. Dans un deuxième temps, vous devez configurer le serveur de mise en file d’attente pour qu’il envoie un signal `keep_alive` afin que les connexions n’atteignent pas le seuil d’inactivité de l’équilibrage de charge interne Azure.

Pour modifier le profil SAP de l’instance ASCS/SCS :

1.  Ajoutez ce paramètre de profil au profil de l’instance SAP ASCS/SCS :

  ```
  enque/encni/set_so_keepalive = true
  ```
  Dans notre exemple, le chemin est :

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Par exemple, au profil de l’instance SAP SCS et au chemin correspondant :

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Pour appliquer les modifications, redémarrez l’instance SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Ajouter un port de sondage

Utilisez la fonctionnalité de sondage de l’équilibrage de charge interne pour que la configuration de cluster entière fonctionne avec Azure Load Balancer. Généralement, l’équilibrage de charge interne Azure répartit équitablement la charge de travail entrante entre les machines virtuelles participantes.

 Toutefois, cela ne fonctionne pas dans certaines configurations de cluster, car une seule instance est active. L’autre instance est passive et ne peut accepter aucune partie de la charge de travail. La fonctionnalité de sondage est utile lorsque l’équilibrage de charge interne Azure affecte du travail uniquement à une instance active. Avec la fonctionnalité de sondage, l’équilibrage de charge interne peut détecter les instances actives, puis cibler uniquement ces instances avec la charge de travail.

Pour ajouter un port de sondage :

1.  Vérifiez la valeur **ProbePort** actuelle en exécutant la commande PowerShell suivante :

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   Exécutez la commande depuis l’une des machines virtuelles de la configuration de cluster.

2.  Définissez un port de sondage. Le numéro de port de sondage par défaut est 0. Dans notre exemple, nous utilisons le port de sondage 62000.

  ![Figure 3 : Le port de sondage de la configuration de cluster est défini sur 0 par défaut][sap-ha-guide-figure-3048]

  _**Figure 3 :** Le port de sondage de la configuration de cluster est 0 par défaut_

  Le numéro de port est défini dans les modèles Azure Resource Manager SAP. Vous pouvez affecter le numéro de port dans PowerShell.

  Pour définir une nouvelle valeur ProbePort pour la ressource de cluster SAP \<SID\> IP, exécutez le script PowerShell suivant pour mettre à jour les variables PowerShell en fonction de votre environnement :

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  Après avoir mis en ligne le rôle de cluster SAP \<SID\>, vérifiez que **ProbePort** est défini sur la nouvelle valeur.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  Après l’exécution du script, vous êtes invité à redémarrer le groupe de clusters SAP pour activer les modifications.

  ![Figure 4 : Sonder le port de cluster après avoir défini la nouvelle valeur][sap-ha-guide-figure-3049]

  _**Figure 4 :** Sonder le port de cluster après avoir défini la nouvelle valeur_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Ouvrir le port de sondage du pare-feu Windows

Ouvrez un port de sondage du pare-feu Windows sur les deux nœuds de cluster. Utilisez le script suivant pour ouvrir un port de sondage du pare-feu Windows. Mettez à jour les variables PowerShell de votre environnement.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** est défini sur **62000**. Vous pouvez maintenant accéder au partage de fichiers \\\ascsha-clsap\sapmnt depuis d’autres hôtes, par exemple ascsha-dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installer l’instance de base de données

Pour installer l’instance de base de données, suivez la procédure décrite dans la documentation d’installation de SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installer le deuxième nœud de cluster

Pour installer le deuxième cluster, suivez les étapes décrites dans le guide d’installation de SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Modifier le type de démarrage de l’instance de service Windows SAP ERS

Choisir le type de démarrage du service Windows SAP ERS **Automatique (début différé)** sur les deux nœuds du cluster.

![Figure 5 : Définir le type de service de l’instance SAP ERS sur Automatique (début différé)][sap-ha-guide-figure-3050]

_**Figure 5 :** Définir le type de service de l’instance SAP ERS sur Automatique (début différé)_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installer le serveur d’applications principal SAP

Installez l’instance de serveur d’application principal (PAS) \<SID\>-di-0 sur la machine virtuelle désignée pour héberger le PAS. Il n’y a aucune dépendance vis-à-vis d’Azure. Il n’y a aucun paramètre propre à DataKeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installer le serveur d’applications supplémentaire SAP

Installez un serveur d’applications supplémentaire SAP (AAS) sur toutes les machines virtuelles désignées pour héberger une instance de serveur d’applications SAP. Par exemple, sur \<SID\>-di-1 à \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Cette étape conclut l’installation d’un système SAP NetWeaver de haute disponibilité. Procédez maintenant à un test de basculement.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Tester le basculement et la réplication SIOS de l’instance SAP ASCS/SCS
Vous pouvez facilement tester et surveiller le basculement et la réplication de disque SIOS d’une instance SAP ASCS/SCS à l’aide du Gestionnaire du cluster de basculement et de l’outil de configuration et de gestion de SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> L’instance SAP ASCS/SCS s’exécute sur le nœud de cluster A

Le groupe de clusters SAP PR1 s’exécute sur le nœud de cluster A, par exemple sur pr1-ascs-0. Affectez le lecteur de disque partagé S, qui fait partie du groupe de clusters SAP PR1, au nœud de cluster A. L’instance ASCS/SCS utilise aussi le lecteur de disque S. 

![Figure 6 : Gestionnaire du cluster de basculement : le groupe de clusters SAP \<SID\> s’exécute sur le nœud de cluster A][sap-ha-guide-figure-5000]

_**Figure 6 :** Gestionnaire du cluster de basculement : le groupe de clusters SAP \<SID\> s’exécute sur le nœud de cluster A_

Dans l’outil de configuration et de gestion de SIOS DataKeeper, vous pouvez constater que les données du disque partagé sont répliquées de manière synchrone à partir du lecteur du volume source S sur le nœud de cluster A vers le lecteur du volume cible S sur le nœud de cluster B. Par exemple, il est répliqué de pr1-ascs-0 [10.0.0.40] vers pr1-ascs-1 [10.0.0.41].

![Figure 7 : Dans SIOS DataKeeper, répliquer le volume local du nœud de cluster A au nœud de cluster B][sap-ha-guide-figure-5001]

_**Figure 7 :** Dans SIOS DataKeeper, répliquer le volume local du nœud de cluster A au nœud de cluster B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Basculement du nœud A au nœud B

1.  Choisissez l’une des options suivantes pour lancer le basculement du groupe de clusters SAP \<SID\> du nœud de cluster A au nœud de cluster B :
  - Gestionnaire du cluster de basculement  
  - Applet de commande PowerShell de cluster de basculement

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Redémarrez le nœud de cluster A dans le système d’exploitation invité Windows. Cela lance le basculement automatique du groupe de clusters SAP \<SID\> du nœud A vers le nœud B.  
3.  Redémarrez le nœud de cluster A à partir du portail Azure. Cela lance le basculement automatique du groupe de clusters SAP \<SID\> du nœud A vers le nœud B.  
4.  Redémarrez le nœud de cluster A à l’aide d’Azure PowerShell. Cela lance le basculement automatique du groupe de clusters SAP \<SID\> du nœud A vers le nœud B.

  Après le basculement, le groupe de clusters SAP \<SID\> s’exécute sur le nœud de cluster B. Par exemple, il s’exécute sur pr1-ascs-1.

  ![Figure 8 : Dans le Gestionnaire du cluster de basculement, le groupe de clusters SAP \<SID\> s’exécute sur le nœud de cluster B][sap-ha-guide-figure-5002]

  _**Figure 8** : Dans le Gestionnaire du cluster de basculement, le groupe de clusters SAP \<SID\> s’exécute sur le nœud de cluster B_

  Le disque partagé est maintenant monté sur le nœud de cluster B. SIOS DataKeeper réplique les données du lecteur du volume source S sur le nœud de cluster B vers le lecteur du volume cible sur le nœud de cluster A. Par exemple, il est répliqué de pr1-ascs-1 [10.0.0.41] vers pr1-ascs-0 [10.0.0.40].

  ![Figure 9 : SIOS DataKeeper réplique le volume local du nœud de cluster B vers le nœud de cluster A][sap-ha-guide-figure-5003]

  _**Figure 9 :** SIOS DataKeeper réplique le volume local du nœud de cluster B vers le nœud de cluster A_
