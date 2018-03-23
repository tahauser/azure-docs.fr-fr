---
title: Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver | Microsoft Docs
description: Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd12808f7e3bbb8a4edfe0d8de1e5b0a007770a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure permet aux entreprises d’acquérir des ressources de calcul et de stockage rapidement, sans longs cycles d’acquisition. Le service Machines virtuelles Azure permet aux entreprises de déployer dans Azure des applications classiques, comme des applications basées sur SAP NetWeaver, et d’étendre leur fiabilité et leur disponibilité même en l’absence des ressources supplémentaires disponibles en local. Le service Machines virtuelles Azure prend également en charge la connectivité intersite, ce qui permet aux entreprises d’intégrer de manière active Machines virtuelles Azure dans leurs domaines locaux, leurs clouds privés et leur paysage SAP.
Ce livre blanc décrit les fondamentaux de Machines virtuelles Microsoft Azure et présente les considérations à prendre en compte en matière de planification et d’implémentation pour les installations SAP NetWeaver dans Azure. Il est donc primordial de lire ce document avant de commencer à effectuer des déploiements de SAP NetWeaver dans Azure.
Ce document vient compléter la documentation sur l’installation SAP et les notes SAP, qui représentent les ressources à consulter pour installer et déployer les logiciels SAP sur des plateformes données.

## <a name="summary"></a>Résumé
Cloud Computing est un terme largement utilisé parce qu’il devient de plus en plus important dans le secteur de l’informatique, que ce soit pour les petites entreprises ou les grandes multinationales.

Microsoft Azure est la plateforme de services cloud de Microsoft, qui offre un large éventail de nouvelles possibilités. Désormais, les clients peuvent approvisionner et déprovisionner rapidement des applications en tant que service dans le cloud, en échappant ainsi aux éventuelles restrictions techniques ou budgétaires. Au lieu de consacrer du temps et de l’argent à l’infrastructure matérielle, les entreprises peuvent se concentrer sur l’application, les processus de travail et leurs avantages pour les clients et les utilisateurs.

Avec le service Machines virtuelles Microsoft Azure, Microsoft propose une infrastructure complète sous forme d’infrastructure en tant que service (IaaS). Les applications basées sur SAP NetWeaver sont prises en charge sur Machines virtuelles Azure (IaaS). Ce livre blanc décrit comment planifier et implémenter des applications basées sur SAP NetWeaver dans Microsoft Azure en tant que plateforme de prédilection.

Ce document met l’accent sur deux aspects principaux :

* La première partie décrit deux modèles de déploiement pris en charge pour les applications basées sur SAP NetWeaver dans Azure. La gestion générale d’Azure avec les déploiements SAP est également abordée.
* La deuxième partie explique en détail l’implémentation des deux scénarios mentionnés dans la première partie.

Pour obtenir des ressources supplémentaires, consultez le chapitre [Ressources][planning-guide-1.2] de ce document.

### <a name="definitions-upfront"></a>Préambule : définitions
Les termes suivants sont utilisés dans le document :

* IaaS : Infrastructure as a Service
* PaaS : Platform as a Service
* SaaS : Software as a Service
* ARM : Azure Resource Manager
* Composant SAP : application SAP individuelle telle que ECC, BW, Solution Manager ou EP.  Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
* Environnement SAP : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
* Paysage SAP : ce terme fait référence à l’ensemble des ressources SAP dans le paysage informatique d’un client. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
* Système SAP : ensemble couche SGBD/couche Application, tel que celui d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM, etc. Dans les déploiements Azure, il n’est pas possible de répartir ces deux couches entre des sites locaux et Azure. Cela signifie qu’un système SAP doit être déployé en local ou dans Azure. Toutefois, vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local.
* Déploiement cloud uniquement : déploiement dans lequel l’abonnement Azure n’est pas connecté via une connexion ExpressRoute ou de site à site à l’infrastructure réseau locale. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des déploiements « cloud uniquement ». Les machines virtuelles déployées avec cette méthode sont accessibles via Internet et une adresse IP publique et/ou un nom DNS public affecté aux machines virtuelles dans Azure. Pour Microsoft Windows, le DNS et l’annuaire Active Directory (AD) locaux ne sont pas étendus à Azure dans ces types de déploiement. Par conséquent, les machines virtuelles ne font pas partie de l’annuaire Active Directory local. Il en est de même pour les implémentations de Linux qui utilisent, par exemple, OpenLDAP + Kerberos.

> [!NOTE]
> Dans ce document, les déploiements cloud uniquement sont définis comme des paysages SAP complets, exécutés exclusivement dans Azure sans extension de l’annuaire Active Directory/OpenLDAP ou résolution de noms du site local au cloud public. Les configurations uniquement cloud ne sont pas prises en charge pour les configurations ou systèmes SAP de production dans lesquels des ressources SAP STMS ou d’autres ressources locales doivent être utilisées entre les systèmes SAP hébergés sur Azure et les ressources en local.
>
>

* Intersite : décrit un scénario dans lequel les machines virtuelles sont déployées sur un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, l’annuaire Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure de l’abonnement. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. C’est le scénario que nous prévoyons pour le déploiement de la plupart des ressources SAP. Pour plus d’informations, consultez [cet][vpn-gateway-cross-premises-options] article et [celui-ci][vpn-gateway-site-to-site-create].

> [!NOTE]
> Les déploiements intersites de systèmes SAP dans lesquels des machines virtuelles Azure exécutant des systèmes SAP font partie d’un domaine local sont pris en charge pour les systèmes SAP de production. Les configurations intersites sont prises en charge pour le déploiement d’éléments ou de l’intégralité des paysages SAP dans Azure. Ces machines virtuelles doivent faire partie du domaine et de l’annuaire ADS/OpenLDAP locaux même quand l’intégralité du paysage SAP est exécutée dans Azure. Dans les versions précédentes de la documentation, nous avons parlé des scénarios hybrides, où le terme *hybride* tient au fait qu’il existe une connectivité intersite entre les sites locaux et Azure. Ici, « hybride » signifie également que les machines virtuelles dans Azure font partie de l’annuaire Active Directory/OpenLDAP local.
>
>

Certaines documentations Microsoft décrivent les scénarios intersites de façon légèrement différente, en particulier pour les configurations haute disponibilité SGBD. Dans les documents relatifs à SAP, le scénario intersite se résume simplement à la mise en œuvre d’une connectivité de site à site ou privée (ExpressRoute) et à la répartition du paysage SAP entre les sites locaux et Azure.  

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Ressources
Les déploiements SAP dans Azure sont décrits dans les guides supplémentaires suivants :

* [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver (ce document)][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP NetWeaver][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP NetWeaver][dbms-guide]

> [!IMPORTANT]
> Le cas échéant, un lien pointant vers le guide d’installation de SAP concerné est utilisé (référence InstGuide-01 à l’adresse <http://service.sap.com/instguides>). Lorsqu’il s’agit de la configuration requise et du processus d’installation, les guides d’installation SAP NetWeaver doivent toujours être lus avec attention, car ce document traite uniquement des tâches spécifiques pour les systèmes SAP NetWeaver installés dans une machine virtuelle Microsoft Azure.
>
>

Les notes SAP suivantes sont associées à la rubrique SAP sur Azure :

| Numéro de la note | Intitulé |
| --- | --- |
| [1928533] |Applications SAP sur Azure : dimensionnement et produits pris en charge |
| [2015553] |SAP sur Microsoft Azure : configuration requise |
| [1999351] |Résolution des problèmes de surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1409604] |Virtualisation sur Windows : surveillance améliorée |
| [2191498] |SAP sur Linux avec Azure : surveillance améliorée |
| [2243692] |Linux sur Microsoft Azure Virtual Machines (IaaS) : problèmes de licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12 : Notes d’installation |
| [2002167] |Red Hat Enterprise Linux 7.x : Installation et mise à niveau |
| [2069760] |Installation et mise à niveau SAP pour Oracle Linux 7.x |
| [1597355] |Recommandations relatives à l’espace d’échange pour Linux |

Consultez également le [Wiki SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) qui regroupe toutes les notes SAP pour Linux.

Les limitations générales par défaut et les limitations maximales des abonnements Azure sont exposées dans [cet article][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Scénarios possibles
SAP est souvent considéré comme une des applications les plus critiques au sein des entreprises. L’architecture et les opérations de ces applications sont généralement très complexes. Il est donc important de vous assurer que vous remplissez les conditions relatives à la disponibilité et aux performances.

Ainsi, les entreprises doivent soigneusement déterminer quelles applications peuvent être exécutées dans un environnement de cloud public, quel que soit le fournisseur de cloud choisi.

Les types de systèmes possibles pour le déploiement des applications basées sur SAP NetWeaver dans des environnements de cloud public sont répertoriés ci-dessous :

1. Systèmes de production de taille moyenne
2. Systèmes de développement
3. Systèmes de test
4. Systèmes de prototype
5. Systèmes de démonstration/formation

Pour déployer correctement les systèmes SAP dans Azure IaaS ou IaaS, il est essentiel de comprendre les différences majeures qui existent entre les offres des hébergeurs ou fournisseurs traditionnels et les offres IaaS. Alors que l’hébergeur ou le fournisseur traditionnel propose d’adapter l’infrastructure (type de serveur, stockage et réseau) à la charge de travail que le client souhaite héberger, c’est le client lui-même qui doit choisir la charge de travail appropriée dans le cadre de déploiements IaaS.

Dans un premier temps, les clients doivent vérifier les éléments suivants :

* Les types de machines virtuelles pris en charge par SAP dans Azure
* Les versions/produits pris en charge par SAP dans Azure
* Les versions SGBD et de système d’exploitation prises en charge par les versions SAP spécifiques dans Azure
* Le débit SAPS (SAP Application Performance Standard) fourni par différentes références Azure

Les réponses à ces questions figurent dans la Note de SAP [1928533].

Dans un deuxième temps, les limitations de bande passante et de ressources Azure doivent être comparées à la consommation réelle des ressources des systèmes locaux. Par conséquent, les clients doivent connaître les différentes fonctionnalités des types de machines virtuelles Azure pris en charge par SAP dans les domaines suivants :

* Les ressources processeur et mémoire des différents types de machines virtuelles
* La bande passante et les E/S par seconde des différents types de machines virtuelles
* Les fonctionnalités réseau des différents types de machines virtuelles

Vous trouverez la plupart de ces données [ici (Linux)][virtual-machines-sizes-linux] et [ici (Windows)][virtual-machines-sizes-windows].

N’oubliez pas que les limites répertoriées dans le lien ci-dessus sont des limites maximales. Cela ne signifie pas que les limites pour toutes les ressources, par exemple les E/S par seconde, peuvent être fournies en toutes circonstances. Toutefois, cela ne s’applique pas aux ressources processeur et mémoire d’un type de machine virtuelle donné. Pour les types de machines virtuelles pris en charge par SAP, les ressources processeur et mémoire sont réservées et sont donc disponibles à tout moment pour la consommation dans la machine virtuelle.

La plateforme Microsoft Azure, comme toute plateforme IaaS, est une plateforme multilocataire. Cela signifie que le stockage, le réseau et toutes les autres ressources sont partagés entre des locataires. La logique de quota et de limitation intelligente est utilisée pour empêcher un locataire d’affecter les performances d’un autre locataire (voisin bruyant) de manière radicale. Bien que la logique dans Azure tente de réduire les écarts de bande passante, les plateformes hautement partagées ont tendance à générer des écarts plus importants en termes de disponibilité des ressources/de bande passante que ceux auxquels de nombreux clients sont habitués dans leurs déploiements locaux. Ainsi, vous pouvez rencontrer des différences de niveau de bande passante pour les E/S de stockage ou réseau (volume et latence) d’un instant à l’autre. La probabilité selon laquelle un système SAP sur Azure puisse connaître des écarts plus importants que ceux d’un système local doit être prise en compte.

La dernière étape consiste à évaluer les conditions de disponibilité. Il peut arriver que l’infrastructure Azure sous-jacente doive être mise à jour et qu’elle requière le redémarrage des hôtes exécutant les machines virtuelles. Dans ce cas, les machines virtuelles en cours d’exécution sur ces hôtes doivent également être arrêtées et redémarrées. L’exécution de ces opérations de maintenance a lieu en dehors des heures de bureau pour une région donnée, mais la période de quelques heures durant laquelle un redémarrage sera nécessaire est relativement étendue. Il existe diverses technologies au sein de la plateforme Azure qui peuvent être configurées pour limiter tout ou partie de l’impact de ces mises à jour. Les améliorations futures de la plateforme Azure, de SGBD et de l’application SAP sont conçues pour réduire l’impact de ces redémarrages.

Pour déployer correctement un système SAP dans Azure, les applications SAP, la base de données et le système d’exploitation des systèmes SAP locaux doivent figurer sur la matrice de prise en charge SAP Azure, tenir dans les ressources que l’infrastructure Azure peut fournir et être compatibles avec le contrat de niveau de service pour la disponibilité que Microsoft Azure propose. Comme ces systèmes sont identifiés, vous devez choisir l’un des deux scénarios de déploiement suivants.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client
![Machine virtuelle unique avec scénario de démonstration ou de formation SAP dans Azure][planning-guide-figure-100]

Ce scénario est généralement utilisé pour les systèmes de démonstration ou les formations, dans lesquels tous les composants du logiciel SAP et autre que SAP sont installés dans une seule machine virtuelle. Les systèmes SAP de production ne sont pas pris en charge dans ce scénario de déploiement. En général, ce scénario remplit les conditions suivantes :

* Les machines virtuelles elles-mêmes sont accessibles via le réseau public. Une connectivité réseau directe des applications s’exécutant dans les machines virtuelles au réseau local de l’entreprise détentrice du contenu des démonstrations ou des formations ou à celui du client n’est pas nécessaire.
* Dans le cas où plusieurs machines virtuelles représentent le scénario de démonstration ou de formations, la résolution de noms et les communications réseau doivent fonctionner entre les machines virtuelles. Cependant, les communications entre l’ensemble des machines virtuelles doivent être isolées afin que plusieurs ensembles de machines virtuelles puissent être déployés côte à côte sans interférence.  
* Une connectivité Internet est requise pour l’utilisateur final, afin qu’il puisse se connecter à distance aux machines virtuelles hébergées dans Azure. En fonction du SE invité, Terminal Services/RDS ou VNC/ssh est utilisé pour accéder à la machine virtuelle afin d’accomplir les tâches de formation ou d’effectuer les démonstrations. Si les ports SAP tels que 3200, 3300 et 3600 peuvent également être exposés, l’instance d’application SAP est accessible à partir de tout ordinateur connecté à Internet.
* Les systèmes SAP (et les machines virtuelles) constituent un scénario autonome dans Azure, qui requiert uniquement une connectivité Internet publique pour l’accès de l’utilisateur final et ne nécessite pas de connexion aux autres machines virtuelles dans Azure.
* L’interface utilisateur graphique SAP et un navigateur sont installés et s’exécutent directement sur la machine virtuelle.
* Une réinitialisation rapide d’une machine virtuelle à son état d’origine, ainsi qu’un nouveau déploiement de cet état d’origine sont requis.
* Dans le cas des scénarios de démonstration et de formation, qui sont appliqués à plusieurs machines virtuelles, un annuaire Active Directory/OpenLDAP et/ou un service DNS sont nécessaires pour chaque groupe de machines virtuelles.

![Groupe de machines virtuelles représentant un scénario de formation ou de démonstration dans un service cloud Azure][planning-guide-figure-200]

Il est important de garder à l’esprit que les machines virtuelles de chaque ensemble doivent être déployées en parallèle, dans le cas où les noms des machines virtuelles de chaque ensemble sont identiques.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, pour lequel une intégration complète dans le réseau local est nécessaire
![Réseau privé virtuel avec connectivité de site à site (intersite)][planning-guide-figure-300]

Ce scénario est un scénario intersite où de nombreux modèles de déploiement sont possibles. Il peut être décrit simplement comme l’exécution de certaines parties du paysage SAP en local et l’exécution des autres parties du paysage SAP dans Azure. L’exécution de certains des composant SAP dans Azure doit être transparente pour les utilisateurs finaux. Ainsi, le système de transport et correction SAP (STMS), la communication RFC, l’impression, la sécurité (comme l’authentification unique), etc. fonctionnent de manière fluide pour les systèmes SAP s’exécutant dans Azure. Toutefois, le scénario intersite décrit également un scénario dans lequel l’intégralité du paysage SAP s’exécute dans Azure avec le DNS et le domaine du client étendus dans Azure.

> [!NOTE]
> Il s’agit du scénario de déploiement, pris en charge pour l’exécution de systèmes SAP de production.
>
>

Lisez [cet article][vpn-gateway-create-site-to-site-rm-powershell] pour plus d’informations sur la façon de connecter votre réseau local à Microsoft Azure

> [!IMPORTANT]
> Quand nous parlons de scénarios intersites entre les déploiements client locaux et Azure, nous nous intéressons à la granularité des systèmes SAP entiers. Les scénarios *non pris en charge* pour les situations intersites sont les suivants :
>
> * L’exécution de différentes couches des applications SAP avec diverses méthodes de déploiement. Par exemple, l’exécution de la couche SGBD en local, mais pas celle de la couche d’application SAP dans les machines virtuelles déployées en tant que machines virtuelles Azure ou inversement.
> * Le fractionnement de certains composants d’une couche SAP dans Azure et d’autres en local. Par exemple, le fractionnement d’instances de la couche d’application SAP entre les machines virtuelles Azure et les sites locaux.
> * La répartition de machines virtuelles exécutant des instances d’un système dans plusieurs régions Azure n’est pas prise en charge.
>
> Ces restrictions sont dues au fait qu’un réseau hautes performances à très faible latence est nécessaire dans un système SAP, en particulier entre les instances d’application et la couche SGBD d’un système SAP.
>
>

### <a name="supported-os-and-database-releases"></a>Versions de base de données et de système d’exploitation prises en charge
* Les logiciels serveurs Microsoft pris en charge par les services de machine virtuelle Azure sont répertoriés dans l’article suivant : <http://support.microsoft.com/kb/2721672>.
* Les versions de système d’exploitation et de base de données prises en charge sur les services de machine virtuelle Azure conjointement avec les logiciels SAP sont mentionnées dans la Note de SAP [1928533].
* Les applications et versions de SAP prises en charge sur les services de machine virtuelle Azure sont mentionnées dans la Note de SAP [1928533].
* Seules les images 64 bits sont prises en charge pour être exécutées en tant que machines virtuelles invitées dans Azure pour les scénarios SAP. Cela signifie également que seules les bases de données et applications SAP 64 bits sont prises en charge.

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure Virtual Machine Services
La Plateforme Microsoft Azure est une plateforme Internet de services cloud hébergée et exploitée dans les centres de données Microsoft. La plateforme comprend Microsoft Azure Virtual Machine Services (Infrastructure as a Service ou IaaS) et un ensemble de fonctionnalités riches de type Platform as a Service (PaaS).

La plateforme Azure vous permet de réduire vos achats initiaux d’infrastructure et de technologie. Elle simplifie la gestion et l’exploitation des applications en fournissant le calcul et le stockage à la demande pour héberger, mettre à l’échelle et gérer une application web et les applications connectées. La gestion de l’infrastructure est automatisée grâce à une plateforme conçue pour offrir un haut niveau de disponibilité et une mise à l’échelle dynamique, afin de faire correspondre les besoins d’utilisation à ceux de l’option du modèle de paiement à l’utilisation.

![Positionnement des services Microsoft Azure Virtual Machine][planning-guide-figure-400]

Avec Azure Virtual Machine Services, Microsoft vous offre la possibilité de déployer des images serveur personnalisées vers Azure en tant qu’instances IaaS (voir Figure 4). Les machines virtuelles dans Azure sont basées sur des disques durs virtuels Hyper-V et sont en mesure d’exécuter différents systèmes d’exploitation en tant que SE invités.

D’un point de vue opérationnel, Azure Virtual Machine Services offre des expériences similaires à celles des machines virtuelles déployées en local. Toutefois, ce service vous évite d’acheter, d’administrer et de gérer l’infrastructure. Les développeurs et les administrateurs ont un contrôle total de l’image de système d’exploitation au sein de ces machines virtuelles. Les administrateurs peuvent ouvrir une session à distance sur ces machines virtuelles pour effectuer des tâches de maintenance et de résolution des problèmes, ainsi que des tâches de déploiement logiciel. En ce qui concerne le déploiement, les seules restrictions qui existent concernent les tailles et les fonctionnalités des machines virtuelles Azure. Celles-ci peuvent ne pas être aussi granulaires dans la configuration que lorsque le déploiement est effectué en local. Vous avez le choix entre plusieurs types de machines virtuelles qui combinent les éléments suivants :

* nombre de processeurs virtuels ;
* mémoire ;
* nombre de disques durs virtuels pouvant être attachés ;
* bande passante réseau et de stockage.

La taille et les limites des tailles des différentes machines virtuelles proposées sont répertoriées dans un tableau de [cet article (Linux)][virtual-machines-sizes-linux] et de [cet article (Windows)][virtual-machines-sizes-windows].

Comme vous voyez, il existe diverses familles ou séries de machines virtuelles. Les familles de machines virtuelles suivantes sont disponibles :

* Types de machines virtuelles A0-A7 : elles ne sont pas toutes certifiées pour SAP. Première série de machines virtuelles pour laquelle Azure IaaS a été introduit.
* Types de machines virtuelles A8-A11 : instances de calcul haute performance. Exécution sur des hôtes de calcul différents et plus performants que les autres machines virtuelles de la série A.
* Types de machines virtuelles de la série D/Dv2 : plus performantes que les A0-A7. Tous les types de machines virtuelles ne sont pas certifiés pour SAP.
* Types de machines virtuelles de la série DS/DSv2 : similaires à celles de la série D/Dv2, mais peuvent se connecter à Stockage Premium Azure (voir le chapitre [Stockage Premium Azure][planning-guide-3.3.2] dans ce document). Une fois encore, tous les types de machines virtuelles ne sont pas certifiés pour SAP.
* Types de machines virtuelles de la série G : types de machines virtuelles à mémoire élevée.
* Types de machines virtuelles de la série GS : similaires à celles de la série G et incluant la possibilité d’utiliser Stockage Premium Azure (voir le chapitre [Stockage Premium Azure][planning-guide-3.3.2] dans ce document). Si vous utilisez les machines virtuelles de la série GS en tant que serveurs de base de données, il est obligatoire d’utiliser Stockage Premium pour les fichiers de journal des transactions et de données de base de données.

Vous pouvez trouver les mêmes configurations mémoire et processeur dans différentes séries de machines virtuelles. Néanmoins, lorsque vous vous intéressez aux performances de débit de ces machines virtuelles issues des différentes séries, elles peuvent différer de manière considérable. Et ce, même si elles possèdent une configuration mémoire et processeur identiques. Cela s’explique par le fait que le matériel de serveur hôte sous-jacent à l’introduction des différents types de machines virtuelles présente des caractéristiques de débit distinctes.  Généralement, la différence indiquée en matière de performances de débit se reflète dans le prix des diverses machines virtuelles.

Toutes les différentes séries de machines virtuelles ne sont pas nécessairement proposées dans chacune des régions Azure (pour les régions Azure, consultez le chapitre suivant). Sachez également que toutes les machines virtuelles ou séries de machines virtuelles ne sont pas certifiées pour SAP.

> [!IMPORTANT]
> Pour l’utilisation d’applications basées sur SAP NetWeaver, seul le sous-ensemble de types de machines virtuelles et les configurations répertoriés dans la Note de SAP [1928533] sont pris en charge.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Régions Azure
Microsoft vous offre la possibilité de déployer des machines virtuelles dans les régions appelées *régions Azure*. Une région Azure peut correspondre à un ou plusieurs centres de données situés à proximité les uns des autres. Dans la plupart des régions géopolitiques du monde, Microsoft a au moins deux régions Azure. Par exemple, l’Europe contient les régions Azure *Europe du Nord* et *Europe de l’Ouest*. Une distance suffisamment importante sépare ces deux régions Azure dans une région géopolitique, afin que des catastrophes naturelles ou techniques n’affectent pas les deux régions Azure situées dans la même région géopolitique. Étant donné que Microsoft crée en permanence de nouvelles régions Azure dans les différentes régions géopolitiques du monde, le nombre de ces régions est en perpétuelle augmentation et, depuis décembre 2015, on compte 20 régions Azure en plus de celles déjà annoncées. En tant que client, vous pouvez déployer des systèmes SAP dans toutes ces régions, y compris dans les deux régions Azure présentes en Chine. Pour obtenir des informations actuelles sur les régions Azure, consultez ce site web : <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Concept de la machine virtuelle Microsoft Azure
Microsoft Azure propose une solution de type Infrastructure as a Service (IaaS) pour héberger les machines virtuelles, avec des fonctionnalités identiques à celles d’une solution de virtualisation locale. Vous êtes en mesure de créer des machines virtuelles depuis le portail Azure, PowerShell ou CLI, qui offrent également des fonctionnalités de gestion et de déploiement.

Azure Resource Manager vous permet d’approvisionner vos applications à l'aide d'un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque étape de son cycle de vie.

Vous trouverez plus d’informations sur les modèles Resource Manager ici :

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Une autre fonctionnalité intéressante est la possibilité de créer des images à partir de machines virtuelles, ce qui vous permet de préparer certains dépôts à partir desquels vous pouvez déployer rapidement des instances de machine virtuelle qui répondent à vos besoins.

Pour plus d’informations sur la création d’images à partir de machines virtuelles, consultez [cet article (Linux)][virtual-machines-linux-capture-image-resource-manager] et [cet article (Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domaines d’erreur
Les domaines d’erreur représentent une unité physique de défaillance, très étroitement liée à l’infrastructure physique contenue dans les centres de données, et lorsqu’un rack ou une lame physique peut être considéré comme un domaine d’erreur, il n’existe aucun mappage un à un direct entre les deux.

Lorsque vous déployez plusieurs machines virtuelles dans le cadre d’un système SAP dans Microsoft Azure Virtual Machine Services, vous pouvez influencer le contrôleur de structure Azure pour déployer votre application dans différents domaines d’erreur, ce qui permet de répondre aux exigences du contrat de niveau de service Microsoft Azure. Toutefois, la répartition des domaines d’erreur dans une unité d’échelle Azure (collection de centaines de nœuds de calcul ou de nœuds de stockage et réseau) ou l’attribution de machines virtuelles à un domaine d’erreur spécifique est un élément sur lequel vous n’avez pas de contrôle direct. Pour gérer le contrôleur de structure Azure afin qu’il déploie un ensemble de machines virtuelles sur différents domaines d’erreur, vous devez attribuer un groupe à haute disponibilité Azure aux machines virtuelles au moment du déploiement. Pour plus d’informations sur les groupes à haute disponibilité Azure, voir le chapitre [Groupes à haute disponibilité Azure][planning-guide-3.2.3] dans ce document.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domaines de mise à niveau
Les domaines de mise à niveau représentent une unité logique qui vous aide à déterminer comment une machine virtuelle au sein d’un système SAP, composé d’instances SAP s’exécutant sur plusieurs machines virtuelles, est mise à jour. Lorsqu’une mise à niveau a lieu, Microsoft Azure exécute le processus de mise à jour de ces domaines de mise à niveau un par un. En répartissant les machines virtuelles dans différents domaines de mise à niveau au moment du déploiement, vous pouvez protéger, en partie, votre système SAP des interruptions potentielles. Pour forcer Azure à déployer les machines virtuelles d’un système SAP réparties dans différents domaines de mise à niveau, vous devez définir un attribut spécifique au moment du déploiement de chaque machine virtuelle. Comme pour les domaines d’erreur, une unité d’échelle Azure est divisée en plusieurs domaines de mise à niveau. Pour gérer le contrôleur de structure Azure afin qu’il déploie un ensemble de machines virtuelles sur différents domaines de mise à niveau, vous devez attribuer un groupe à haute disponibilité Azure aux machines virtuelles au moment du déploiement. Pour plus d’informations sur les groupes à haute disponibilité Azure, voir le chapitre[ Groupes à haute disponibilité Azure][planning-guide-3.2.3] ci-dessous.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Groupes à haute disponibilité Azure
Les machines virtuelles Azure au sein d’un seul groupe à haute disponibilité Azure sont réparties par le contrôleur de structure Azure dans différents domaines d’erreur et de mise à niveau. L’objectif de la répartition dans différents domaines d’erreur et de mise à niveau consiste à empêcher l’arrêt de toutes les machines virtuelles d’un système SAP en cas de maintenance ou de défaillance de l’infrastructure dans un domaine d’erreur. Par défaut, les machines virtuelles ne font pas partie d’un groupe à haute disponibilité. La présence d’une machine virtuelle dans un groupe à haute disponibilité est définie au moment du déploiement ou ultérieurement par une reconfiguration et un redéploiement d’une machine virtuelle.

Pour comprendre le concept des groupes à haute disponibilité Azure et leur relation avec les domaines d’erreur et de mise à niveau, consultez [cet article][virtual-machines-manage-availability]

Pour définir des groupes à haute disponibilité pour ARM par le biais d’un modèle JSON, voir les [spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) et rechercher « disponibilité ».

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Stockage : Microsoft Azure Storage et disques de données
Les Microsoft Azure Virtual Machines utilisent différents types de stockage. Pendant l’implémentation SAP sur Azure Virtual Machine Services, il est important de comprendre les différences entre ces deux principaux types de stockage :

* stockage volatil non persistant ;
* stockage persistant.

Le stockage non persistant est directement attaché aux machines virtuelles en cours d’exécution et réside sur les nœuds de calcul, le stockage d’instance local (stockage temporaire). La taille dépend de celle de la machine virtuelle choisie au début du déploiement. Ce type de stockage est volatil et par conséquent, le disque est initialisé lorsqu’une instance de machine virtuelle est redémarrée. En règle générale, le fichier d’échange du système d’exploitation se trouve sur ce disque temporaire.

- - -
> ![Windows][Logo_Windows] Windows
>
> Sur les machines virtuelles Windows, le lecteur temp est monté en tant que lecteur D:\ dans une machine virtuelle déployée.
>
> ![Linux][Logo_Linux] Linux
>
> Sur les machines virtuelles Linux, il est monté en tant que/mnt/resource ou /mnt. Vous trouverez plus de détails ici :
>
> * [Association d’un disque de données à une machine virtuelle Linux][virtual-machines-linux-how-to-attach-disk]
> * <https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux#temporary-disk>
>
>

- - -
Le lecteur réel est volatil, car il est stocké sur le serveur hôte. Si la machine virtuelle a été déplacée durant un redéploiement (par exemple, en raison d’une maintenance sur l’hôte ou d’un arrêt et d’un redémarrage), le contenu du lecteur est perdu. Par conséquent, il n’est pas recommandé de stocker des données importantes sur ce lecteur. Le type de support utilisé pour ce genre de stockage diffère entre les séries de machines virtuelles qui présentent des caractéristiques de performances très variées. Vous trouverez ci-dessous un récapitulatif de ces performances (données datant de juin 2015) :

* A5-A7 : performances très limitées. Déconseillées pour le stockage autre que celui de fichier d’échange.
* A8-A11 : très bonnes caractéristiques de performances avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série D : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série DS : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série G : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série GS : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.

Les chiffres donnés ci-dessus s’appliquent aux types de machines virtuelles certifiés pour SAP. Les séries de machines virtuelles présentant un nombre d’E/S par seconde et un débit excellents sont adaptées à certaines fonctionnalités SGBD. Pour plus d’informations, consultez le [Guide de déploiement SGBD][dbms-guide].

Le service Stockage Microsoft Azure fournit le stockage persistant et les niveaux classiques de protection et de redondance observés pour le stockage SAN. Les disques basés sur Stockage Azure sont des disques durs virtuels situés dans les services de stockage Azure. Le disque de système d’exploitation local (Windows C:\, Linux /dev/sda1) est stocké sur le Stockage Azure, comme les volumes/disques supplémentaires montés sur la machine virtuelle.

Il est possible de télécharger un disque dur virtuel existant depuis un site local ou d’en créer des vides depuis Azure et de les attacher aux machines virtuelles déployées.

Après avoir créé ou téléchargé un disque dur virtuel dans Stockage Azure, il est possible de le monter et de l’attacher à une machine virtuelle existante et de copier le disque dur virtuel (démonté) existant.

Comme ces disques durs virtuels sont conservés, les données et les modifications dans ceux-ci sont protégées lors du redémarrage et de la nouvelle création d’une instance de machine virtuelle. Même si une instance est supprimée, ces disques durs virtuels sont protégés et peuvent être redéployés ou, en cas de disques autres que ceux de systèmes d’exploitation, peuvent être montés sur d’autres machines virtuelles.

Au sein du réseau Stockage Azure, différents niveaux de redondance peuvent être configurés :

* Le niveau minimal sélectionnable est « *redondance locale* », qui équivaut à trois réplicas de données dans le même centre de données d’une région Azure (voir le [chapitre Régions Azure][planning-guide-3.1]).
* Le stockage redondant dans une zone, qui répartit les trois images dans trois centres de données différents au sein de la même région Azure.
* Le niveau de redondance par défaut, qui correspond à la redondance géographique et qui réplique de façon asynchrone le contenu dans trois autres images de données dans une autre région Azure hébergée dans la même région géopolitique.

Pour connaître les différentes options de redondance, consultez le tableau au début de l’article suivant : <https://azure.microsoft.com/pricing/details/storage/>

Vous trouverez plus d’informations sur Stockage Azure ici :

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Stockage Azure Standard
Le stockage Azure Standard était le type de stockage disponible au lancement d’Azure IaaS. Des quotas d’E/S par seconde étaient appliqués pour chaque disque. La latence n’était pas dans la même classe que celle des appareils SAN/NAS généralement déployés pour les systèmes SAP haut de gamme hébergés en local. Néanmoins, le stockage Azure Standard s’est avéré suffisant pour plusieurs centaines de systèmes SAP déployés entre-temps dans Azure.

Les disques qui sont stockés sur des comptes de stockage Azure Standard sont facturés en fonction des données réelles stockées, du volume de transactions de stockage, du transfert de données sortantes et de l’option de redondance choisie. De nombreux disques peuvent être créés (de 1 To maximum), mais tant que ceux-ci sont vides, vous n’êtes pas facturé. Si vous remplissez ensuite un VHD avec 100 Go de données, vous êtes facturé pour le stockage de 100 Go et non pour la taille nominale pour laquelle le VHD a été créé.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
En avril 2015, Microsoft a introduit Stockage Premium Azure. Stockage Premium a été introduit dans le but de fournir :

* une meilleure latence d’E/S ;
* un meilleur débit ;
* une latence d’E/S plus stable.

À cet effet, de nombreuses modifications ont été apportées, dont les deux principales sont :

* l’utilisation de disques SSD dans les nœuds Stockage Azure ;
* un nouveau cache de lecture, reposant sur le disque SSD local d’un nœud de calcul Azure.

À l’inverse du stockage standard, où les capacités n’évoluent pas en fonction de la taille du disque (ou du VHD), le Stockage Premium comprend trois catégories de disques, qui sont présentées dans cet article : <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Vous pouvez constater que les E/S par seconde par disque et le débit du disque par disque dépendent de la catégorie de taille des disques.

Pour Stockage Premium, les coûts ne sont pas basés sur le volume réel de données stocké dans les disques, mais sur la catégorie de taille du disque, sans tenir compte de la quantité de données stockée sur celui-ci.

Vous pouvez également créer des disques dans Stockage Premium qui ne sont pas directement mappés aux catégories de taille présentées. Cela peut être le cas quand vous copiez des disques de Stockage Standard vers Stockage Premium. Dans ce genre de situation, un mappage vers l’option de disque Stockage Premium la plus importante est effectué.

Gardez à l’esprit que seules certaines séries de machines virtuelles sont éligibles pour Stockage Premium Azure. À compter de décembre 2015, il s’agit des séries DS et GS. La série DS est sensiblement identique à la série D, à l’exception du fait que, pour la série D, des machines virtuelles supplémentaires basées sur Stockage Premium peuvent être montées sur les disques hébergés sur le Stockage Azure Standard. Cette même particularité existe pour les séries G et GS.

Si vous consultez la partie de [cet article (Linux)][virtual-machines-sizes-linux] et de [cet article (Windows)][virtual-machines-sizes-windows] consacrée aux machines virtuelles de la série DS, vous constatez qu’il existe des limites de volumes de données sur les disques Stockage Premium en relation avec la granularité du niveau de machine virtuelle. Les différentes machines virtuelles des séries DS ou GS présentent également des limitations diverses relatives au nombre de disques de données qui peuvent être montés. Ces limites sont également documentées dans l’article mentionné ci-dessus. En résumé, cela signifie que si vous montez, par exemple, 32 disques P30 sur une seule machine virtuelle DS14, vous NE pouvez PAS obtenir un débit égal au débit maximum d’un disque P30 multiplié par 32. Au lieu de cela, le débit maximum au niveau de la machine virtuelle décrit dans l’article limite le débit des données.

Vous trouverez plus d’informations sur le stockage Premium ici : <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Disques managés
Les disques managés sont un nouveau type de ressources d’Azure Resource Manager. Ils peuvent être utilisés à la place des disques durs virtuels qui sont stockés dans les comptes de stockage Azure. Les disques managés s’alignent automatiquement sur le groupe à haute disponibilité de la machine virtuelle à laquelle ils sont attachés. De fait, ils augmentent la disponibilité de votre machine virtuelle et des services exécutés sur celle-ci. Pour plus d’informations, consultez [l’article de vue d’ensemble](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Nous vous recommandons d’utiliser des disques managés, car ils simplifient le déploiement et la gestion de vos machines virtuelles.
SAP prend uniquement en charge les disques managés Premium. Pour plus d’informations, consultez la note SAP [1928533].

#### <a name="azure-storage-accounts"></a>Comptes Stockage Azure
Durant le déploiement de services ou de machines virtuelles dans Azure, le déploiement de VHD et d’images de machine virtuelle peut être organisé dans des unités appelées comptes Stockage Azure. Lorsque vous planifiez un déploiement Azure, vous devez tenir compte des restrictions d’Azure. D’un côté, le nombre de comptes de stockage par abonnement Azure est limité. Bien que chaque compte Stockage Azure puisse contenir de nombreux fichiers de disque dur virtuel, une limite fixe d’E/S totales par compte de stockage a été établie. Lorsque vous déployez des centaines de machines virtuelles SAP avec des systèmes SGBD créant des appels d’E/S importants, il est recommandé de répartir les machines virtuelles SGBD présentant un nombre d’E/S par seconde élevé dans plusieurs comptes Stockage Azure. Faites attention à ne pas dépasser la limite actuelle de comptes Stockage Azure par abonnement. Le stockage étant un élément essentiel du déploiement de base de données pour un système SAP, ce concept est décrit plus en détail dans le [Guide de déploiement de SGBD][dbms-guide] déjà mentionné.

Pour plus d’informations sur les comptes de stockage Azure, consultez [cet article][storage-scalability-targets]. En lisant cet article, vous constatez qu’il existe des différences de limitations entre les comptes de stockage Azure Standard et les comptes Stockage Premium. Les principales différences portent sur le volume de données qui peut être stocké dans un compte de stockage de ce type. Dans Stockage Standard, le volume est bien plus important que celui de Stockage Premium. D’un autre côté, le compte Stockage Standard est fortement limité en ce qui concerne les E/S par seconde (voir la colonne **Taux de demandes total**), tandis que le compte Stockage Premium Azure n’a aucune limitation de ce type. Nous discuterons plus précisément de ces différences lorsque nous étudierons les déploiements de systèmes SAP, notamment les serveurs SGBD.

Dans un compte de stockage, vous avez la possibilité de créer des conteneurs distincts pour organiser et classer les différents disques durs virtuels. Ces conteneurs sont généralement utilisés pour, par exemple, séparer les VHD des diverses machines virtuelles. Peu importe le nombre de conteneurs que vous utilisez pour un seul compte Stockage Azure, les performances n’en seront pas affectées.

Dans Azure, un nom de disque dur virtuel suit la connexion d’attribution de noms ci-dessous qui doit fournir un nom unique pour le disque dur virtuel dans Azure :

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Comme indiqué, la chaîne ci-dessus doit identifier de manière unique le disque dur virtuel qui est stocké sur Stockage Azure.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Mise en réseau Microsoft Azure
Microsoft Azure fournit une infrastructure réseau qui permet le mappage de tous les scénarios que nous souhaitons réaliser avec le logiciel SAP. Ses fonctionnalités sont les suivantes :

* accès depuis l’extérieur, directement aux machines virtuelles via Windows Terminal Services ou ssh/VNC ;
* accès aux services et ports spécifiques utilisés par les applications dans les machines virtuelles ;
* communication interne et résolution de noms entre un groupe de machines virtuelles déployées en tant que machines virtuelles Azure ;
* connectivité intersite entre un réseau local du client et le réseau Azure
* connectivité entre le centre de données ou la région Azure entre les sites Azure.

Des informations supplémentaires sont disponibles ici : <https://azure.microsoft.com/documentation/services/virtual-network/>

Il existe de nombreuses façons de configurer la résolution de noms et IP dans Azure. Dans ce document, les scénarios cloud uniquement reposent sur l’utilisation par défaut du DNS Azure (plutôt que de définir un service DNS propre). Il existe également un nouveau service DNS Azure, qui peut être utilisé au lieu de configurer votre propre serveur DNS. Pour plus d’informations, consultez [cet article][virtual-networks-manage-dns-in-vnet] et [cette page](https://azure.microsoft.com/services/dns/).

Pour les scénarios intersites, nous partons du principe que l’annuaire AD/OpenLDAP et le DNS locaux ont été étendus via une connexion VPN ou privée à Azure. Pour certains scénarios documentés ici, il peut être nécessaire de disposer d’un réplica d’annuaire AD/OpenLDAP installé dans Azure.

La mise en réseau et la résolution de noms étant des aspects essentiels du déploiement de base de données pour un système SAP, ce concept est décrit plus en détail dans le [Guide de déploiement de SGBD][dbms-guide].

##### <a name="azure-virtual-networks"></a>Réseaux virtuels Azure
En créant un réseau virtuel Azure, vous pouvez définir la plage d’adresses des adresses IP privées allouées par la fonctionnalité DHCP Azure. Dans les scénarios intersites, la plage d’adresses IP définie est toujours allouée via la fonctionnalité DHCP par Azure. Toutefois, la résolution de noms de domaine est effectuée en local (en supposant que les machines virtuelles font partie d’un domaine local) et peut donc résoudre les adresses au-delà des différents services cloud Azure.

Chaque machine virtuelle dans Azure doit être connectée à un réseau virtuel.

Pour plus d’informations, consultez [cet article][resource-groups-networking] et [cette page](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO Couldn't find an article which includes the OpenLDAP topic + ARM; )
[comment]: <> (MSSedusch &lt;https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL&gt;)

> [!NOTE]
> Par défaut, une fois qu’une machine virtuelle est déployée, vous ne pouvez pas modifier la configuration du réseau virtuel. Les paramètres TCP/IP doivent être conservés sur le serveur DHCP d’Azure. Le comportement par défaut est l’attribution d’adresse IP dynamique.
>
>

L’adresse MAC de la carte de réseau virtuel peut changer (par exemple, après un redimensionnement) et l’OS invité Linux ou Windows sélectionne la nouvelle carte réseau et utilise automatiquement DHCP pour assigner les adresses DNS et IP dans ce cas.

##### <a name="static-ip-assignment"></a>Attribution d’adresse IP statique
Il est possible d’attribuer des adresses IP fixes ou réservées aux machines virtuelles d’un réseau virtuel Azure. Exécuter les machines virtuelles dans un réseau virtuel Azure offre l’opportunité exceptionnelle d’exploiter cette fonctionnalité si cela est nécessaire ou obligatoire pour certains scénarios. L’attribution d’adresse IP reste valide tout au long de l’existence de la machine virtuelle, que celle-ci soit en fonctionnement ou à l’arrêt. Par conséquent, vous devez prendre en compte le nombre total de machines virtuelles (machines virtuelles en fonctionnement et arrêtées) lors de la définition de la plage d’adresses IP pour le réseau virtuel. L’adresse IP reste attribuée jusqu’à ce que la machine virtuelle et son interface réseau soient supprimées ou jusqu’à ce que l’attribution de l’adresse IP soit à nouveau supprimée. Pour plus d’informations, consultez [cet article][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Plusieurs cartes d’interface réseau par machine virtuelle
Vous pouvez définir plusieurs cartes d’interface de réseau virtuel pour une machine virtuelle Azure. Grâce à plusieurs cartes d’interface de réseau virtuel, vous pouvez commencer à configurer la séparation du trafic réseau où, par exemple, le trafic client est acheminé via une carte d’interface de réseau virtuel et le trafic backend est acheminé via une deuxième carte d’interface de réseau virtuel. En fonction du type de machine virtuelle, il existe différentes limitations relatives au nombre de cartes d’interface de réseau virtuel. Vous trouverez des informations précises et des renseignements sur les fonctions et les restrictions dans ces articles :

* [Créer une machine virtuelle Windows avec plusieurs cartes d’interface réseau (NIC)][virtual-networks-multiple-nics-windows]
* [Créer une machine virtuelle Linux avec plusieurs cartes réseau][virtual-networks-multiple-nics-linux]
* [Déployer des machines virtuelles avec plusieurs cartes réseau à l’aide d’un modèle][virtual-network-deploy-multinic-arm-template]
* [Déployer des machines virtuelles avec plusieurs cartes réseau à l’aide de PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Déployer des machines virtuelles avec plusieurs cartes réseau à l'aide de l'interface de ligne de commande Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Connectivité de site à site
La connectivité intersite consiste à lier, via une connexion VPN transparente et permanente, les machines virtuelles Azure et les sites locaux. Cela devrait devenir le modèle de déploiement SAP le plus courant dans Azure. L’hypothèse est que les procédures et les processus opérationnels avec des instances SAP dans Azure doivent fonctionner en toute transparence. Cela signifie que vous devez être en mesure d’imprimer en dehors de ces systèmes, mais également d’utiliser le système de gestion de transport SAP (TMS) pour transporter les changements d’un système de développement dans Azure à un système de test, qui déployé en local. Pour plus d’informations sur la connectivité intersite, consultez [cet article][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Tunnel VPN
Pour créer une connexion de site à site (centre de données local vers centre de données Azure), vous devez obtenir et configurer un appareil VPN ou utiliser le service Routage et accès distant (RRAS) qui a été introduit en tant que composant logiciel avec Windows Server 2012.

* [Créer un réseau virtuel avec une connexion VPN site à site à l’aide de PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [À propos des périphériques VPN pour les connexions de la passerelle VPN de site à site][vpn-gateway-about-vpn-devices]
* [FAQ sur la passerelle VPN][vpn-gateway-vpn-faq]

![Connexion de site à site entre des systèmes locaux et dans Azure][planning-guide-figure-600]

La figure ci-dessus présente deux abonnements Azure qui ont des sous-plages d’adresses IP réservées pour une utilisation dans des réseaux virtuels Azure. La connectivité du réseau local vers Azure est établie via un VPN.

#### <a name="point-to-site-vpn"></a>VPN de point à site
Le VPN de point à site requiert que chaque machine du client se connecte à Azure avec son propre VPN. Pour les scénarios SAP que nous étudions, la connectivité de point à site n’est pas pratique. Ainsi, nous n’en parlons pas davantage.

Des informations supplémentaires sont disponibles dans les documents suivants :
* [Configurer une connexion site à site vers un réseau virtuel à l’aide du portail Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Configurer une connexion site à site vers un réseau virtuel à l’aide de PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN multisite
Actuellement, Azure propose aussi de créer une connexion VPN multisite pour un abonnement Azure. Précédemment, un abonnement était limité à une seule connexion VPN de intersite. Cette limitation a disparu et vous pouvez désormais bénéficier de connexions VPN multisites par abonnement. Cela permet d’exploiter plusieurs régions Azure pour un abonnement spécifique par le biais de configurations intersites.

Pour plus d’informations, consultez [cet article][vpn-gateway-create-site-to-site-rm-powershell].

[comment]: <> (MShermannd TODO found no ARM docu link)

#### <a name="vnet-to-vnet-connection"></a>Connexion de réseau virtuel à réseau virtuel
À l’aide d’un VPN multisite, vous devez configurer un réseau virtuel Azure distinct dans chacune des régions. Cependant, très souvent vous avez besoin que les composants logiciels des différentes régions puissent communiquer entre eux. Dans l’idéal, cette communication ne doit pas être acheminée depuis une région Azure vers un site local, ni depuis ce site local vers l’autre région Azure. En bref, Azure vous offre la possibilité de configurer une connexion depuis un réseau virtuel Azure dans une région vers un autre réseau virtuel Azure hébergé dans l’autre région. Cette fonctionnalité est appelée connexion de réseau virtuel à réseau virtuel. Vous trouverez plus de détails sur cette fonctionnalité ici : <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Connexion privée à Azure - ExpressRoute
Microsoft Azure ExpressRoute permet de créer des connexions privées entre des centres de données Azure et l’infrastructure locale du client ou un environnement de colocalisation. ExpressRoute est proposé par divers fournisseurs VPN (commutation de paquets) MPLS ou d’autres fournisseurs de services réseau. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Les connexions ExpressRoute offrent une sécurité accrue, une plus grande fiabilité via plusieurs circuits parallèles, des vitesses plus rapides et des latences moindres par rapport aux connexions classiques sur Internet.

Vous trouverez plus d’informations sur Azure ExpressRoute et les offres ici : 

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

ExpressRoute permet d’utiliser plusieurs abonnements Azure via un seul circuit ExpressRoute, comme décrit ici

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Tunneling forcé en cas de scénario intersite
Pour les machines virtuelles rejoignant les domaines locaux via une connexion de site à site, de point à site ou ExpressRoute, vous devez vous assurer que les paramètres du proxy Internet sont déployés pour tous les utilisateurs dans ces machines virtuelles. Par défaut, le logiciel s’exécutant dans ces machines virtuelles ou les utilisateurs utilisant un navigateur pour accéder à Internet ne passeront pas par le proxy d’entreprise, mais se connecteront directement via Azure à Internet. Mais, même le paramétrage du proxy ne constitue pas une solution totalement efficace pour diriger le trafic vers le proxy de l’entreprise, puisque ce sont les services et le logiciel qui sont responsables de vérifier le proxy. Si le logiciel exécuté dans la machine virtuelle n’effectue pas cette vérification ou si un administrateur manipule les paramètres, le trafic vers Internet peut à nouveau être détourné directement vers Internet par le biais d’Azure.

Pour éviter cela, vous pouvez configurer le tunneling forcé avec une connectivité de site à site entre le site local et Azure. La description détaillée de la fonctionnalité de tunneling forcé est disponible ici : <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Le tunneling forcé avec ExpressRoute est activé par les clients publiant un itinéraire par défaut via les sessions d’homologation BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Résumé de la mise en réseau Azure
Ce chapitre abordait plusieurs points importants sur la mise en réseau Azure. Voici un résumé des points principaux :

* Les réseaux virtuels Azure permettent de configurer le réseau en fonction de vos besoins.
* Les réseaux virtuels Azure peuvent être exploités pour attribuer des plages d’adresses IP à des machines virtuelles ou pour attribuer des adresses IP fixes à des machines virtuelles.
* Pour configurer une connexion de site à site ou de point à site, vous devez d’abord créer un réseau virtuel Azure.
* Après le déploiement d’une machine virtuelle, il n’est plus possible de modifier le réseau virtuel attribué à la machine virtuelle.

### <a name="quotas-in-azure-virtual-machine-services"></a>Quotas dans Azure Virtual Machine Services
Nous devons être clairs sur le fait que l’infrastructure réseau et de stockage est partagée entre les machines virtuelles exécutant une variété de services dans l’infrastructure Azure. Et, comme dans les propres centres de données du client, un approvisionnement excessif de certaines des ressources d’infrastructure a lieu dans une certaine mesure. La Plateforme Microsoft Azure utilise le disque, le processeur, le réseau et d’autres quotas pour limiter la consommation de ressources et conserver des niveaux de performance cohérents et déterministes.  Les différents types de machines virtuelles (A5, A6, etc.) possèdent des quotas distincts pour le nombre de disques, le processeur, la RAM et le réseau.

> [!NOTE]
> Les ressources processeur et mémoire des types de machines virtuelles pris en charge par SAP sont pré-allouées sur les nœuds hôtes. Cela signifie qu’une fois la machine virtuelle déployée, les ressources sur l’hôte sont disponibles comme défini par le type de machine virtuelle.
>
>

Lors de la planification et du dimensionnement SAP sur les solutions Azure, les quotas pour chaque taille de machine virtuelle doivent être pris en compte. Les quotas de machines virtuelles sont décrits [ici (Linux)][virtual-machines-sizes-linux] et [ici (Windows)][virtual-machines-sizes-windows].

Les quotas décrits représentent les valeurs maximum théoriques.  La limite d’E/S par seconde par disque peut être atteinte avec un faible volume d’E/S (8 Ko), mais ne peut pas être atteinte avec un volume important d’E/S (1 Mo).  La limite d’E/S par seconde est appliquée sur la granularité d’un seul disque.

L’arbre de décision approximatif ci-dessous peut servir à déterminer si un système SAP s’adapte à Azure Virtual Machine Services et à ses fonctionnalités ou si un système existant doit être configuré différemment pour déployer le système sur Azure :

![Arbre de décision pour décider de la capacité à déployer SAP sur Azure][planning-guide-figure-700]

**Étape 1**: il est important de commencer par les informations les plus importantes, à savoir la configuration SAP requise pour un système SAP donné. La configuration SAP doit être séparée dans la partie SGBD (système de gestion de base de données) et dans les applications SAP, même si le système SAP est déjà déployé localement dans une configuration de niveau 2. Pour les systèmes existants, les SAP liés au matériel souvent utilisés peuvent être déterminés ou estimés en fonction de points de référence SAP existants. Les résultats se trouvent ici : <http://global.sap.com/campaigns/benchmark/index.epx>.
Pour les systèmes SAP nouvellement déployés, vous devez avoir effectué un exercice de dimensionnement pour déterminer la configuration SAP du système.
Consultez aussi ce billet de blog et le document joint pour le dimensionnement de SAP sur Azure : <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Étape 2**: pour les systèmes existants, le volume d’E/S et les opérations d’E/S par seconde sur le serveur SGBD (système de gestion de base de données) doivent être mesurés. Pour les systèmes récemment planifiés, l’exercice de dimensionnement pour le nouveau système doit également donner une idée approximative de la configuration des E/S côté SGBD. En cas de doute, vous devrez réaliser une preuve de concept.

**Étape 3**: comparer la configuration SAP requise pour le serveur SGBD (système de gestion de base de données) avec les SAP que peuvent fournir les différents types de machines virtuelles Azure. Les informations sur les SAP des différents types de machines virtuelles Azure se trouvent dans la Note de SAP [1928533]. La machine virtuelle du SGBD doit constituer une priorité, car la couche de base de données correspond à la couche d’un système SAP NetWeaver dont la taille des instances n’est pas augmentée dans la plupart des déploiements. En revanche, la taille des instances pour la couche d’application SAP peut être augmentée. Si aucune machine virtuelle Azure prenant en charge SAP ne peut fournir les SAP requis, la charge de travail du système SAP planifié ne peut alors être exécutée dans Azure. Vous devez déployer le système localement ou modifier le volume de charge de travail pour le système.

**Étape 4** : comme indiqué [ici (Linux)][virtual-machines-sizes-linux] et [ici (Windows)][virtual-machines-sizes-windows], Azure applique un quota d’E/S par seconde par disque, que vous utilisiez un stockage Standard ou Premium. Le nombre de disques de données pouvant être montés varie en fonction du type de machine virtuelle. Par conséquent, vous pouvez calculer le nombre maximal d’E/S par seconde pouvant être obtenues avec chaque type de machine virtuelle. En fonction de la disposition du fichier de base de données, vous pouvez entrelacer les disques pour que ceux-ci ne constituent qu’un seul volume dans le système d’exploitation invité. Toutefois, si le volume d’E/S par seconde actuel d’un système SAP déployé dépasse les limites calculées pour le type de machine virtuelle Azure le plus volumineux, et s’il est impossible de le compenser avec l’ajout de mémoire supplémentaire, la charge de travail du système SAP peut s’en trouver gravement affectée. Dans ce cas, il serait plus judicieux de ne pas déployer le système dans Azure.

**Étape 5** : le système doit probablement être configuré sur trois niveaux dans Azure, surtout dans les systèmes SAP, qui sont déployés localement avec des configurations à deux niveaux. Dans cette étape, vous devez vérifier s’il existe un composant dans la couche d’application SAP, dont la taille des instances ne peut être augmentée et qui n’est pas compatible avec les ressources processeur et mémoire fournies par les différents types de machine virtuelle Azure. Si c’est le cas, le système SAP et sa charge de travail ne peuvent pas être déployés vers Azure. Cependant, si vous pouvez augmenter la taille des instances des composants d’application SAP dans plusieurs machines virtuelles Azure, le système peut être déployé vers Azure.

**Étape 6**: si les composants de la couche d’application SAP et SGBD (système de gestion de base de données) peuvent être exécutés sur des machines virtuelles Azure, la configuration doit être définie en prenant en considération ce qui suit :

* Nombre de machines virtuelles Azure
* Types de machine virtuelle pour les composants individuels
* Nombre de disques durs virtuels dans la machine virtuelle du SGBD (afin de fournir suffisamment d’E/S par seconde).

## <a name="managing-azure-assets"></a>Gestion des ressources Azure
### <a name="azure-portal"></a>Portail Azure
Le portail Azure constitue l’une des trois interfaces destinées à la gestion des déploiements des machines virtuelles Azure. Les tâches de gestion de base, telles que le déploiement des machines virtuelles à partir d’images, peuvent être effectuées via le portail Azure. En outre, le portail Azure gère également avec facilité la création de comptes de stockage, de réseaux virtuels ainsi que d’autres composants Azure. Toutefois, des fonctionnalités telles que le chargement de VHD locaux sur Azure ou la copie d’un VHD au sein d’Azure sont des tâches qui nécessitent des outils tiers ou une administration via PowerShell ou l’interface de lignes de commandes.

![Portail Microsoft Azure - vue d’ensemble de la machine virtuelle][planning-guide-figure-800]

[comment]: <> (MSSedusch * &lt;https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/&gt;)
[comment]: <> (MSSedusch * &lt;https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/&gt;)

Les tâches d’administration et de configuration de l’instance de la machine virtuelle sont possibles au sein du portail Azure.

Outre le redémarrage et l’arrêt d’une machine virtuelle, vous pouvez attacher, détacher et créer des disques de données pour l’instance de machine virtuelle, afin de capturer l’instance pour la préparation de l’image et configurer la taille de l’instance de machine virtuelle.

Le portail Azure fournit des fonctionnalités de base pour déployer et configurer des machines virtuelles et de nombreux autres services Azure. Cependant, toutes les fonctionnalités disponibles ne sont pas couvertes par le portail Azure. Dans le portail Azure, il est impossible d’effectuer des tâches telles que :

* le téléchargement de disques durs virtuels vers Azure ; 
* la copie de machines virtuelles.

[comment]: <> (MShermannd TODO what about automation service for SAP VMs ? )
[comment]: <> (MSSedusch deployment of multiple VMs os meanwhile possible)
[comment]: <> (MSSedusch Also any type of automation regarding deployment is not possible with the Azure portal. Le portail Azure n’autorise pas des tâches telles que les déploiements de plusieurs machines virtuelles via un script.)

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestion par le biais des applets de commande Microsoft Azure PowerShell
Windows PowerShell est une infrastructure puissante et extensible, largement adoptée par les clients qui déploient un nombre plus important de systèmes dans Azure. Après avoir installé des applets de commande PowerShell sur un ordinateur de bureau, un ordinateur portable ou un poste de gestion dédié, les applets de commande PowerShell peuvent être exécutées à distance.

Le processus permettant à un ordinateur local (de bureau ou portable) d’utiliser des applets de commande Azure PowerShell, et la manière de configurer ces dernières pour une utilisation avec des abonnements Azure sont décrits dans [cet article][powershell-install-configure].

Des étapes plus détaillées sur l’installation, la mise à jour et la configuration des applets de commande Azure PowerShell sont également décrites dans ce [chapitre du Guide de déploiement][deployment-guide-4.1].

Le retour de nos clients a permis de déterminer que PowerShell (PS) constitue l’outil le plus puissant pour déployer des machines virtuelles et créer des étapes personnalisées lors de leur déploiement. Tous les clients exécutant des instances SAP dans Azure utilisent des applets de commande PS pour compléter des tâches de gestion qu’ils effectuent dans le portail Azure. Certains utilisent même exclusivement des applets de commande PS pour gérer leurs déploiements dans Azure. Étant donné que les applets de commande dédiées à Azure partagent la même convention d’affectation de noms que les applets de commande Windows (dont le nombre dépasse 2000), les administrateurs Windows peuvent facilement tirer parti de ces applets de commande.

Consultez l’exemple ici : <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO describe new CLI command when tested )
Le déploiement de l’extension Surveillance Azure pour SAP (consultez le chapitre [Solution de surveillance Azure pour SAP][planning-guide-9.1] dans ce document) n’est possible que via PowerShell ou l’interface de ligne de commande. Ainsi, il est obligatoire d’installer et de configurer PowerShell ou l’interface de ligne de commandes durant le déploiement ou l’administration d’un système SAP NetWeaver dans Azure.  

Étant donné qu’Azure propose davantage de fonctionnalités, de nouvelles applets de commande PS vont être ajoutées ; celles-ci nécessiteront une mise à jour des applets de commande. Il est donc recommandé de consulter le site de téléchargement Azure <https://azure.microsoft.com/downloads/> au moins une fois par mois, pour vérifier si une nouvelle version des applets de commande est disponible. La nouvelle version est installée par-dessus l’ancienne version.

Pour une liste générale des commandes Azure PowerShell, consultez : <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestion à l’aide des commandes CLI Microsoft Azure
Pour les clients utilisant Linux et souhaitant gérer des ressources Azure, Powershell peut ne pas être une option. Microsoft propose la CLI Microsoft Azure en guise d’alternative.
La CLI Microsoft Azure offre un ensemble de commandes multiplateforme open source pour l’utilisation de la plateforme Microsoft Azure. La CLI Microsoft Azure offre des fonctionnalités similaires à celles du portail Azure.

Pour plus d’informations sur l’installation, la configuration et l’utilisation des commandes CLI permettant de réaliser des tâches Azure, voir

* [Installer l’interface de ligne de commande Microsoft Azure][xplat-cli]
* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager][xplat-cli-azure-resource-manager]

Pour savoir comment utiliser l’interface de ligne de commande Azure dans le but de déployer l’extension de surveillance Azure pour SAP, consultez le chapitre [Interface de ligne de commande Azure pour machines virtuelles Linux][deployment-guide-4.5.2] du [Guide de déploiement][planning-guide].

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Méthodes différentes pour le déploiement de machines virtuelles pour SAP dans Azure
Dans ce chapitre, vous découvrez les différentes manières de déployer une machine virtuelle dans Azure. Des procédures de préparation supplémentaires, ainsi que la gestion des disques durs virtuels et des machines virtuelles dans Azure, sont présentées dans ce chapitre.

### <a name="deployment-of-vms-for-sap"></a>Déploiement de machines virtuelles pour SAP
Microsoft Azure offre plusieurs modes de déploiement de machines virtuelles et des disques associés. Ainsi, il est très important de comprendre les différences dans la mesure où les préparations des machines virtuelles peuvent différer en fonction du déploiement. Globalement, nous étudions les scénarios suivants :

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé
Vous envisagez de déplacer un système SAP spécifique local vers Azure. Pour ce faire, vous pouvez charger le VHD, qui contient le système d’exploitation, les fichiers binaires SAP et les fichiers binaires SGBD, ainsi que les VHD contenant les données et les fichiers journaux du SGBD vers Azure. Contrairement au [scénario 2 ci-dessous][planning-guide-5.1.2], vous conservez le nom d’hôte, le SID SAP et les comptes d’utilisateur SAP dans la machine virtuelle Azure tels qu’ils ont été configurés dans l’environnement local. Par conséquent, il n’est pas nécessaire de généraliser l’image. Pour plus d’informations sur les étapes de préparation en local et le chargement des machines virtuelles et VHD non généralisés sur Azure, consultez le chapitre [Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé][planning-guide-5.2.1] dans ce document. Pour obtenir la procédure détaillée du déploiement d’une telle image dans Azure, consultez le chapitre [Scénario 3 : déplacer une machine virtuelle locale à l’aide d’un VHD non généralisé avec SAP][deployment-guide-3.4] dans le [Guide de déploiement][deployment-guide].

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Déploiement d’une machine virtuelle avec une image spécifique du client
En raison des exigences spécifiques de correctif par rapport aux versions de votre système d’exploitation ou SGBD, les images fournies provenant de la Place de marché Azure peuvent ne pas répondre à vos besoins. Ainsi, vous devrez peut-être créer une machine virtuelle à l’aide de votre propre image « privée » de machine virtuelle du système d’exploitation/SGBD, qui peut être déployée plusieurs fois par la suite. Pour préparer une image « privée » de ce type à des fins de duplication, les éléments suivants doivent être pris en considération :

- - -
> ![Windows][Logo_Windows] Windows
>
> Pour plus de détails : <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Les paramètres de Windows (tels que le nom d’hôte et le SID Windows) doivent être extraits/généralisés sur la machine virtuelle locale via la commande sysprep.
>
>
> ![Linux][Logo_Linux] Linux
>
> Pour préparer un VHD à un chargement sur Azure, suivez les étapes décrites dans les articles suivants pour [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] ou [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle].
>
>

- - -
Si vous avez déjà installé du contenu SAP sur votre machine virtuelle locale (en particulier pour les systèmes à 2 niveaux), vous pouvez adapter les paramètres du système SAP après le déploiement de la machine virtuelle Azure à l’aide de la procédure de modification du nom d’instance prise en charge par le gestionnaire de déploiement de logiciels SAP (Note de SAP [1619720]). Pour en savoir plus sur les étapes de préparation en local et le chargement d’une machine virtuelle généralisée sur Azure, consultez les chapitres [Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP][planning-guide-5.2.2] et [Chargement d’un VHD local vers Azure][planning-guide-5.3.2] dans ce document. Pour obtenir la procédure détaillée de déploiement d’une image dans Azure, consultez le chapitre [Scénario 2 : déployer une machine virtuelle avec une image personnalisée pour SAP][deployment-guide-3.3] du [Guide de déploiement][deployment-guide].

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Déploiement d’une machine virtuelle provenant de la Place de marché Azure
Vous voulez utiliser une image fournie par Microsoft ou par un tiers provenant de la Place de marché Azure pour déployer votre machine virtuelle. Une fois que vous avez déployé votre machine virtuelle dans Azure, vous utilisez les mêmes instructions et outils pour installer les logiciels SAP et/ou SGBD (système de base de données) au sein de votre machine virtuelle comme vous le feriez dans un environnement local. Pour une description plus détaillée du déploiement, voir le chapitre [Scénario 1 : déployer une machine virtuelle à partir de la Place de marché Azure pour SAP][deployment-guide-3.2] du [Guide de déploiement][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Préparation de machines virtuelles avec SAP pour Azure
Avant de charger des machines virtuelles dans Azure, vous devez vous assurer que les machines virtuelles et les disques durs virtuels répondent à certaines exigences. Il existe de légères différences selon la méthode de déploiement utilisée.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé
Une méthode de déploiement courante consiste à déplacer une machine virtuelle existante, qui exécute un système SAP, depuis l’emplacement local vers Azure. Cette machine virtuelle et le système SAP de la machine virtuelle doivent simplement s’exécuter dans Azure à l’aide du même nom d’hôte et très probablement le même SID SAP. Dans ce cas, le SE invité de la machine virtuelle ne doit pas être généralisé pour plusieurs déploiements. Si le réseau local a été étendu à Azure (consultez le chapitre [Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, pour lequel une intégration complète dans le réseau local est nécessaire][planning-guide-2.2] dans ce document), les comptes de domaine utilisés dans la machine virtuelle peuvent être les mêmes que ceux utilisés précédemment en local.

Les exigences à respecter pour la préparation de votre propre disque de machine virtuelle Azure sont les suivantes :

* À l’origine, la taille maximale du disque dur virtuel contenant le système d’exploitation était seulement de 127 Go. Cette limite a été repoussée en mars 2015. Désormais, la taille du disque dur virtuel contenant le système d’exploitation peut aller jusqu’à 1 To, comme tout disque dur virtuel hébergé sur Azure Storage.
* Il doit être au format VHD fixe. Les disques durs virtuels ou les disques durs virtuels au format VHDx ne sont pas encore pris en charge sur Azure. Les disques durs virtuels dynamiques seront convertis en disques durs virtuels statiques lorsque vous chargez le disque dur virtuel avec les applets de commande PowerShell ou l’interface CLI
* Les disques durs virtuels montés sur la machine virtuelle, et devant être montés de nouveau sur celle-ci dans Azure, doivent également être au format VHD fixe. Pour connaître les limites de taille des disques de données, consultez [cet article (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) et [cet article (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows). Les disques durs virtuels dynamiques seront convertis en disques durs virtuels statiques lorsque vous chargez le disque dur virtuel avec les applets de commande PowerShell ou l’interface CLI
* Ajoutez un autre compte local doté de privilèges d’administrateur, pouvant être utilisé par le support technique Microsoft ou pouvant être affecté en tant que contexte d’exécution pour les services et les applications, jusqu’à ce que la machine virtuelle soit déployée et des utilisateurs plus appropriés puissent être utilisés.
* Dans le cas d’un déploiement de type cloud uniquement (voir le chapitre [Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client][planning-guide-2.1] dans ce document) en association avec cette méthode de déploiement, il se peut que les comptes de domaine ne fonctionnent pas une fois que le disque Azure est déployé dans Azure. Cela s’applique particulièrement aux comptes qui sont utilisés pour exécuter des services tels que les applications SGBD ou SAP. Par conséquent, vous devez remplacer ces comptes de domaine avec des comptes locaux de machine virtuelle et supprimer les comptes de domaine locaux dans la machine virtuelle. La conservation des utilisateurs du domaine local dans l’image de machine virtuelle ne constitue pas un problème quand la machine virtuelle est déployée dans le cadre d’une connexion intersite (consultez le chapitre [Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, pour lequel une intégration complète dans le réseau local est nécessaire][planning-guide-2.2] dans ce document).
* Si les comptes de domaine ont été utilisés en tant que connexions SGBD ou en tant qu’utilisateurs lorsque le système était exécuté localement et que ces machines virtuelles sont censées être déployées dans des scénarios cloud uniquement, les utilisateurs du domaine doivent être supprimés. Vous devez vous assurer que l’administrateur local et un autre utilisateur local de machine virtuelle sont ajoutés en tant que connexion/utilisateur dans le SGBD ainsi qu’en tant qu’administrateurs.
* Ajoutez d’autres comptes locaux, car ceux-ci peuvent être nécessaires pour le scénario de déploiement spécifique.

- - -
> ![Windows][Logo_Windows] Windows
>
> Dans ce scénario, aucune généralisation (sysprep) de la machine virtuelle n’est requise pour charger et déployer la machine virtuelle sur Azure.
> Vérifiez que le lecteur D:\ n’est pas utilisé.
> Définissez le montage automatique de disque pour les disques attachés comme décrit dans le chapitre [Paramétrage du montage automatique pour les disques attachés][planning-guide-5.5.3] dans ce document.
>
> ![Linux][Logo_Linux] Linux
>
> Dans ce scénario, aucune généralisation (waagent -deprovision) de la machine virtuelle n’est requise pour charger et déployer la machine virtuelle sur Azure.
> Assurez-vous que/mnt/resource n’est pas utilisée et que TOUS les disques sont montés via uuid. Pour le disque de système d’exploitation, vérifiez que l’entrée du chargeur de démarrage correspond également au montage uuid.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP
Les fichiers VHD contenant un système d’exploitation généralisé sont stockés dans des conteneurs dans les comptes de stockage Azure ou en tant qu’images de disque managé. Vous pouvez déployer une nouvelle machine virtuelle à partir d’une telle image en référençant le VHD ou l’image de disque managé comme source dans vos fichiers de modèle de déploiement. Consultez le chapitre [Scénario 2 : déployer une machine virtuelle avec une image personnalisée pour SAP][deployment-guide-3.3] du [Guide de déploiement][deployment-guide].

Les exigences à respecter pour la préparation de votre propre image de machine virtuelle Azure sont les suivantes :

* À l’origine, la taille maximale du disque dur virtuel contenant le système d’exploitation était seulement de 127 Go. Cette limite a été repoussée en mars 2015. Désormais, la taille du disque dur virtuel contenant le système d’exploitation peut aller jusqu’à 1 To, comme tout disque dur virtuel hébergé sur Azure Storage.
* Il doit être au format VHD fixe. Les disques durs virtuels ou les disques durs virtuels au format VHDx ne sont pas encore pris en charge sur Azure. Les disques durs virtuels dynamiques seront convertis en disques durs virtuels statiques lorsque vous chargez le disque dur virtuel avec les applets de commande PowerShell ou l’interface CLI
* Les disques durs virtuels montés sur la machine virtuelle, et devant être montés de nouveau sur celle-ci dans Azure, doivent également être au format VHD fixe. Pour connaître les limites de taille des disques de données, consultez [cet article (Linux)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-linux) et [cet article (Windows)](https://docs.microsoft.com/azure/storage/storage-about-disks-and-vhds-windows). Les disques durs virtuels dynamiques seront convertis en disques durs virtuels statiques lorsque vous chargez le disque dur virtuel avec les applets de commande PowerShell ou l’interface CLI
* Étant donné que tous les utilisateurs du domaine inscrits en tant qu’utilisateurs dans la machine virtuelle n’existent pas dans le cas d’un déploiement cloud uniquement, il se peut que des services tels que les comptes de domaine ne fonctionnent pas une fois l’image déployée dans Azure (voir le chapitre [Cloud uniquement : déploiement de machines virtuelles dans Azure sans dépendances du réseau local du client][planning-guide-2.1] dans ce document). Cela s’applique particulièrement aux comptes qui sont utilisés pour exécuter des services tels que les applications SGBD ou SAP. Par conséquent, vous devez remplacer ces comptes de domaine avec des comptes locaux de machine virtuelle et supprimer les comptes de domaine locaux dans la machine virtuelle. La conservation des utilisateurs du domaine local dans l’image de machine virtuelle ne constitue pas un problème quand la machine virtuelle est déployée dans le cadre d’une connexion intersite (consultez le chapitre [Intersite : déploiement d’une ou plusieurs machines virtuelles SAP dans Azure, pour lequel une intégration complète dans le réseau local est nécessaire][planning-guide-2.2] dans ce document).
* Ajoutez un autre compte local doté de privilèges d’administrateur, pouvant être utilisé par le support technique Microsoft pour examiner des problèmes ou pouvant être affecté en tant que contexte d’exécution pour les services et les applications, jusqu’à ce que la machine virtuelle soit déployée et des utilisateurs plus appropriés puissent être utilisés.
* Dans le cas de déploiements cloud uniquement et si les comptes de domaine ont été utilisés en tant que connexions SGBD ou en tant qu’utilisateurs lorsque le système était exécuté localement, les utilisateurs du domaine doivent être supprimés. Vous devez vous assurer que l’administrateur local et un autre utilisateur local de machine virtuelle sont ajoutés en tant que connexion/utilisateur dans le SGBD ainsi qu’en tant qu’administrateurs.
* Ajoutez d’autres comptes locaux, car ceux-ci peuvent être nécessaires pour le scénario de déploiement spécifique.
* Si l’image contient une installation de SAP NetWeaver et si le renommage du nom d’hôte au moment du déploiement Azure est probable, il est recommandé de copier les dernières versions du DVD du gestionnaire de déploiement de logiciels SAP dans le modèle. Cela vous permettra d’utiliser la fonction de renommage fournie par SAP pour adapter le nom d’hôte modifié et/ou changer le SID du système SAP dans l’image de machine virtuelle déployée dès qu’une nouvelle copie est démarrée.

- - -
> ![Windows][Logo_Windows] Windows
>
> Assurez-vous que le lecteur D:\ n’est pas un montage automatique de disque défini pour les disques attachés, comme décrit dans le chapitre [Paramétrage du montage automatique pour les disques attachés][planning-guide-5.5.3] dans ce document.
>
> ![Linux][Logo_Linux] Linux
>
> Assurez-vous que/mnt/resource n’est pas utilisée et que TOUS les disques sont montés via uuid. Pour le disque de système d’exploitation, vérifiez que l’entrée du chargeur de démarrage correspond également au montage uuid.
>
>

- - -
* L’interface utilisateur graphique SAP peut être installée dans un tel modèle (à des fins administratives et de configuration).
* D’autres logiciels nécessaires à l’exécution réussie des machines virtuelles dans les scénarios de coexistence entre sites peuvent être installés tant que ceux-ci fonctionnent avec la machine virtuelle renommée.

Si la préparation de la machine virtuelle permet à cette dernière d’être générique, voire indépendante des comptes/utilisateurs non disponibles pour le scénario de déploiement Azure ciblé, la dernière étape de préparation de la généralisation d’une telle image est effectuée.

##### <a name="generalizing-a-vm"></a>Généralisation d’une machine virtuelle
- - -
> ![Windows][Logo_Windows] Windows
>
> La dernière étape consiste à se connecter à une machine virtuelle à l’aide d’un compte d’administrateur. Ouvrez une fenêtre de commande Windows en tant *qu’administrateur*. Accédez à %windir%\windows\system32\sysprep et exécutez sysprep.exe.
> Une petite fenêtre s’affiche. Il est important de cocher l’option **Généraliser** (vide par défaut) et de faire passer l’option d’extinction de sa valeur par défaut (Redémarrer) à « Arrêt ». Cette procédure suppose que le processus sysprep est exécuté localement dans le SE invité d’une machine virtuelle.
> Si vous souhaitez exécuter la procédure avec une machine virtuelle en cours d’exécution dans Azure, suivez les étapes décrites dans [cet article](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).
>
> ![Linux][Logo_Linux] Linux
>
> [Comment capturer une machine virtuelle Linux à utiliser comme modèle Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transfert de machines virtuelles et de disques durs virtuels locaux vers Azure
Étant donné que le chargement d’images de machine virtuelle et de disques sur Azure n’est pas possible via le portail Azure, vous devez utiliser les applets de commande Azure PowerShell ou l’interface de ligne de commande. Une autre possibilité serait d’utiliser l’outil « AzCopy ». Cet outil peut copier les disques durs virtuels locaux vers Azure (et vice-versa). Il peut également copier les disques durs virtuels entre différentes régions Azure. Consultez [la documentation suivante][storage-use-azcopy] pour en savoir plus sur le téléchargement et l’utilisation d’AzCopy.

Une troisième alternative consisterait à utiliser divers outils tiers orientés GUI. Toutefois, assurez-vous que ces outils prennent en charge les objets blob de pages Azure. Dans notre cas, nous devons utiliser le magasin d’objets blob de pages Azure (les différences sont décrites ici : <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Les outils fournis par Azure sont très efficaces pour la compression des machines virtuelles et des disques durs virtuels devant être chargés. Il s’agit d’un élément important car plus la compression est efficace, plus le temps de chargement est réduit. (Ce temps varie tout de même en fonction du lien de chargement vers Internet depuis l’emplacement local et de la région de déploiement Azure ciblée). Il est logique de supposer que le chargement d’une machine virtuelle ou d’un VHD à partir d’un emplacement européen sur un centre de données Azure se trouvant aux États-Unis prendra plus de temps que le chargement de ces machines virtuelles/VHD vers les centres de données Azure européens.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Chargement d’un disque dur virtuel local vers Azure
Pour charger une machine virtuelle ou un disque dur virtuel existants à partir du réseau local, ces derniers doivent répondre aux exigences décrites dans le chapitre [Préparation du déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé][planning-guide-5.2.1] dans ce document.

Cette machine virtuelle n’a PAS besoin d’être généralisée et peut être chargée dans l’état et la forme dans lesquels elle se trouvait après l’arrêt du côté local. Il en est de même pour les disques durs virtuels supplémentaires qui ne contiennent aucun système d’exploitation.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Chargement d’un disque dur virtuel en vue d’en faire un disque Azure
Dans ce scénario, nous souhaitons charger un disque dur virtuel, avec ou sans système d’exploitation, et le monter sur une machine virtuelle en tant que disque de données ou l’utiliser en tant que disque de système d’exploitation. Ce processus comprend plusieurs étapes

**PowerShell**

* Connectez-vous à votre abonnement en entrant *Login-AzureRmAccount*
* Définissez l’abonnement de votre contexte en entrant *Set-AzureRmContext* et le paramètre SubscriptionId ou SubscriptionName - consultez <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Chargez le disque dur virtuel avec *Add-AzureRmVhd* sur un compte de stockage Azure - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Facultatif) Créez un disque géré à partir du disque dur virtuel avec *New-AzureRmDisk* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk>
* Définissez le disque du système d’exploitation d’une nouvelle configuration de machine virtuelle sur le disque dur virtuel ou disque géré avec *Set-AzureRmVMOSDisk* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
* Créez une machine virtuelle à partir de la configuration de machine virtuelle avec *New-AzureRmVM* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>
* Ajoutez un disque de données à une nouvelle machine virtuelle en entrant *Add-AzureRmVMDataDisk* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk>

**Azure CLI 2.0**

* Connectez-vous à votre abonnement avec *az login*
* Sélectionnez votre abonnement en entrant *az account set --subscription `<subscription name or id`>*
* Chargez le VHD en entrant *az storage blob upload*. Consultez [Utilisation de l’interface de ligne de commande Azure avec Stockage Azure][storage-azure-cli]
* (Facultatif) Créez un disque managé à partir du VHD avec *az disk create*. Consultez https://docs.microsoft.com/cli/azure/disk#az_disk_create
* Créez une machine virtuelle et spécifiez le disque managé ou le VHD chargé en tant que disque du système d’exploitation en entrant *az vm create* et le paramètre *--attach-os-disk*
* Ajoutez un disque de données à une nouvelle machine virtuelle en entrant *az vm disk attach* et le paramètre *--new*

**Modèle**

* Chargez le disque dur virtuel avec Powershell ou l’interface de ligne de commande Azure
* (Facultatif) Créez un disque managé à partir du VHD avec Powershell, l’interface de ligne de commande Azure ou le portail Azure
* Déployez la machine virtuelle avec un gabarit JSON en référençant le VHD comme indiqué dans [cet exemple de gabarit JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json)ou en utilisant des disques managés comme indiqué dans [cet exemple de gabarit JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-disk-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Déploiement d’une image de machine virtuelle
Pour charger une machine virtuelle ou un VHD existants à partir du réseau local afin de les utiliser en tant qu’image de machine virtuelle Azure, cette machine ou VHDs doivent satisfaire aux exigences décrites dans le chapitre [Préparation du déploiement d’une machine virtuelle avec une image spécifique du client pour SAP][planning-guide-5.2.2] dans ce document.

* Utilisez *sysprep* sur Windows ou *waagent -deprovision* sur Linux afin de généraliser votre machine virtuelle. Voir [Informations techniques de référence de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pour Windows ou [Comment capturer une machine virtuelle Linux à utiliser en tant que modèle Resource Manager][capture-image-linux-step-2-create-vm-image] pour Linux
* Connectez-vous à votre abonnement en entrant *Login-AzureRmAccount*
* Définissez l’abonnement de votre contexte en entrant *Set-AzureRmContext* et le paramètre SubscriptionId ou SubscriptionName - consultez <https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext>
* Chargez le disque dur virtuel avec *Add-AzureRmVhd* sur un compte de stockage Azure - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd>
* (Facultatif) Créez une image de disque géré à partir du disque dur virtuel avec *New-AzureRmImage* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage>
* Définissez le disque du système d’exploitation d’une nouvelle configuration de machine virtuelle sur
  * Le VHD avec *Set-AzureRmVMOSDisk -SourceImageUri -CreateOption fromImage*. consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk>
  * Image de disque géré *Set-AzureRmVMSourceImage* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage>
* Créez une machine virtuelle à partir de la configuration de machine virtuelle avec *New-AzureRmVM* - consultez <https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm>

**Azure CLI 2.0**

* Utilisez *sysprep* sur Windows ou *waagent -deprovision* sur Linux afin de généraliser votre machine virtuelle. Voir [Informations techniques de référence de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pour Windows ou [Comment capturer une machine virtuelle Linux à utiliser en tant que modèle Resource Manager][capture-image-linux-step-2-create-vm-image] pour Linux
* Connectez-vous à votre abonnement avec *az login*
* Sélectionnez votre abonnement en entrant *az account set --subscription `<subscription name or id`>*
* Chargez le VHD en entrant *az storage blob upload*. Consultez [Utilisation de l’interface de ligne de commande Azure avec Stockage Azure][storage-azure-cli]
* (Facultatif) Créez une image de disque managé à partir du VHD avec *az image create*. Consultez https://docs.microsoft.com/cli/azure/image#az_image_create
* Créez une machine virtuelle et spécifiez l’image de disque managé ou le VHD chargé en tant que disque du système d’exploitation en entrant *az vm create* et le paramètre *--image*

**Modèle**

* Utilisez *sysprep* sur Windows ou *waagent -deprovision* sur Linux afin de généraliser votre machine virtuelle. Voir [Informations techniques de référence de Sysprep](https://technet.microsoft.com/library/cc766049.aspx) pour Windows ou [Comment capturer une machine virtuelle Linux à utiliser en tant que modèle Resource Manager][capture-image-linux-step-2-create-vm-image] pour Linux
* Chargez le disque dur virtuel avec Powershell ou l’interface de ligne de commande Azure
* (Facultatif) Créez une image de disque managé à partir du VHD avec Powershell, l’interface de ligne de commande Azure ou le portail Azure
* Déployez la machine virtuelle avec un gabarit JSON en référençant le VHD image comme indiqué dans [cet exemple de gabarit JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sap-2-tier-user-image/azuredeploy.json)ou en utilisant l’image de disque managé comme indiqué dans [cet exemple de gabarit JSON](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Téléchargement de VHD ou de disques managés au niveau local
IaaS permet bien plus que le chargement de disques durs virtuels et de systèmes SAP. Vous pouvez également déplacer les systèmes SAP de nouveau en local à partir d’Azure.

Les VHD ou les disques managés ne peuvent pas être actifs pendant le téléchargement. La machine virtuelle doit être désactivée, même pendant le téléchargement des disques montés sur la machine virtuelle. Si vous voulez seulement télécharger le contenu de la base de données devant ensuite être utilisé pour configurer un nouveau système local, et s’il est acceptable que le système reste fonctionnel dans Azure pendant la durée du téléchargement et de l’installation du nouveau système, vous pouvez éviter un long temps d’arrêt en effectuant une sauvegarde de base de données compressée dans un disque et en téléchargeant simplement ce disque au lieu de télécharger également la machine virtuelle de base du système d’exploitation.

#### <a name="powershell"></a>PowerShell

  * Téléchargement d’un disque managé  
  Vous devez d’abord obtenir l’accès à l’objet blob sous-jacent du disque managé. Vous pouvez ensuite copier l’objet blob sous-jacent dans un nouveau compte de stockage et télécharger l’objet blob à partir de ce compte de stockage.

  ```powershell
  $access = Grant-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzureRmStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzureStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzureStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzureStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzureRmVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzureRmDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

  * Téléchargement d’un VHD  
  Une fois que le système SAP est arrêté et la machine virtuelle désactivée, vous pouvez utiliser l’applet de commande PowerShell Save-AzureRmVhd sur la cible locale pour télécharger les VHD en local. Pour ce faire, vous avez besoin de l’URL du VHD, que vous trouverez dans la section Stockage du portail Azure (accédez au compte de stockage et au conteneur de stockage dans lequel le VHD a été créé), ainsi que de savoir où le VHD doit être copié.

  Vous pouvez ainsi utiliser la commande en définissant simplement le paramètre SourceUri comme l’URL du disque dur virtuel à télécharger et LocalFilePath comme l’emplacement physique du disque dur virtuel (y compris son nom). La commande pourrait ressembler à :

  ```powerhell
  Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Pour plus d’informations sur l’applet de commande Save-AzureRmVhd, consultez la page <https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvhd>.

#### <a name="cli-20"></a>CLI 2.0
  * Téléchargement d’un disque managé  
  Vous devez d’abord obtenir l’accès à l’objet blob sous-jacent du disque managé. Vous pouvez ensuite copier l’objet blob sous-jacent dans un nouveau compte de stockage et télécharger l’objet blob à partir de ce compte de stockage.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

  * Téléchargement d’un VHD   
  Une fois que le système SAP est arrêté et la machine virtuelle désactivée, vous pouvez utiliser la commande de l’interface de ligne de commande Azure _azure storage blob download_ sur la cible locale pour télécharger les VHD en local. Pour ce faire, vous avez besoin du nom et du conteneur du VHD, que vous trouverez dans la section Stockage du portail Azure (accédez au compte de stockage et au conteneur de stockage dans lequel le VHD a été créé), ainsi que de savoir où le VHD doit être copié.

  Vous pouvez ainsi utiliser la commande en définissant simplement les paramètres du blob et du conteneur du disque dur virtuel à télécharger et la destination comme l’emplacement physique cible du disque dur virtuel (y compris son nom). La commande pourrait ressembler à :

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transfert de machines virtuelles et de disques au sein d’Azure
#### <a name="copying-sap-systems-within-azure"></a>Copie des systèmes SAP au sein d’Azure
Un système SAP ou même un serveur SGBD dédié prenant en charge une couche d’application SAP est constitué de plusieurs disques, contenant le système d’exploitation avec des fichiers binaires ou des fichiers journaux et données de la base de données SAP. Les fonctionnalités Azure de copie de disques et d’enregistrement de disques sur un disque local n’ont pas de mécanisme de synchronisation permettant de capturer l’instantané de plusieurs disques de façon synchrone. Ainsi, l’état des disques copiés ou enregistrés serait différent, même si ceux-ci sont montés sur la même machine virtuelle. Cela signifie que, si les fichiers données et journaux contenus dans les divers disques étaient différents, la base de données serait incohérente.

**Conclusion : pour copier ou enregistrer des disques faisant partie de la configuration d’un système SAP, vous devez arrêter celui-ci, ainsi que la machine virtuelle déployée. Ce n’est qu’à cette condition que vous pouvez copier ou télécharger l’ensemble de disques afin de créer une copie du système SAP dans Azure ou en local.**

Les disques de données peuvent être stockés en tant que fichiers VHD dans un compte de stockage Azure et peuvent être directement attachés à une machine virtuelle ou utilisés en tant qu’image. Dans ce cas, le VHD est copié vers un autre emplacement avant d’être attaché à la machine virtuelle. Le nom complet du fichier VHD dans Azure doit être unique dans Azure. Comme mentionné plus tôt, le nom est constitué de trois éléments :

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Les disques de données peuvent également être des disques managés. Dans ce cas, le disque managé est utilisé pour créer un disque managé avant d’être attaché à la machine virtuelle. Le nom du disque managé doit être unique au sein d’un groupe de ressources.

##### <a name="powershell"></a>PowerShell
Vous pouvez utiliser des applets de commande Azure PowerShell pour copier un disque dur virtuel, comme illustré dans [cet article][storage-powershell-guide-full-copy-vhd]. Pour créer un disque managé, utilisez New-AzureRmDiskConfig et New-AzureRmDisk comme indiqué dans l’exemple suivant.

```powershell
$config = New-AzureRmDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzureRmDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="cli-20"></a>CLI 2.0
Vous pouvez utiliser l’interface de ligne de commande Azure pour copier un VHD, comme illustré dans [cet article][storage-azure-cli-copy-blobs]. Pour créer un disque managé, utilisez *az disk create* comme indiqué dans l’exemple suivant.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Outils Azure Storage
* <http://storageexplorer.com/>

Des éditions professionnelles de l’Explorateur de stockage Azure se trouvent ici :

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

La copie d’un disque dur virtuel, se trouvant dans un compte de stockage, est un processus qui dure quelques secondes seulement (semblable à du matériel SAN créant des captures d’instantanés avec copie différée et copie sur écriture). Une fois que vous avez une copie du fichier VHD, vous pouvez l’attacher à une machine virtuelle ou l’utiliser en tant qu’image pour joindre des copies du disque dur virtuel aux machines virtuelles.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzureRmVM

# attach a copy of the managed disk to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzureRmDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzureRmDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzureRmVM
```
##### <a name="cli-20"></a>CLI 2.0
```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copie de disques entre comptes Azure Storage
Il est impossible d’effectuer cette tâche sur le portail Azure. Vous pouvez utiliser les applets de commande Azure PowerShell, l’interface de ligne de commande Azure ou un navigateur de stockage tiers. Les applets de commande PowerShell ou les commandes CLI peuvent créer et gérer des objets blob. Elles permettent de copier des objets blob de façon asynchrone entre comptes de stockage et régions au sein de l’abonnement Azure.

##### <a name="powershell"></a>PowerShell
Vous pouvez également copier des VHD entre des abonnements. Pour plus d’informations, consultez [cet article][storage-powershell-guide-full-copy-vhd].

Le flux de base de la logique d’applet de commande PS ressemble à ceci :

* Créez un contexte pour le compte de stockage **source** en entrant *New-AzureStorageContext* - consultez <https://msdn.microsoft.com/library/dn806380.aspx>
* Créez un contexte pour le compte de stockage **cible** en entrant *New-AzureStorageContext* - consultez <https://msdn.microsoft.com/library/dn806380.aspx>
* Démarrer la copie avec

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Vérifier l’état de la copie dans une boucle avec

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Joindre le nouveau disque dur virtuel à une machine virtuelle comme décrit ci-dessus.

Pour découvrir des exemples, consultez [cet article][storage-powershell-guide-full-copy-vhd].

##### <a name="cli-20"></a>CLI 2.0
* Démarrer la copie avec

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Vérifier l’état de la copie dans une boucle avec

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Joindre le nouveau disque dur virtuel à une machine virtuelle comme décrit ci-dessus.

Pour découvrir des exemples, consultez [cet article][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Gestion de disque
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Structure de machine virtuelle/disque pour les déploiements SAP
Dans l’idéal, la gestion de la structure d’une machine virtuelle et des disques associés doit être très simple. Dans les installations locales, les clients ont développé de nombreuses méthodes pour structurer une installation du serveur.

* Un disque de base qui contient le système d’exploitation et tous les fichiers binaires du SGBD (système de gestion de base de données) et/ou du SAP. Depuis mars 2015, ce disque peut atteindre une taille allant jusqu’à 1 To, au lieu des 127 Go précédents.
* Un ou plusieurs disques contenant le fichier journal SGBD de la base de données SAP et le fichier journal de la zone de stockage temporaire SGBD (si le SGBD les prend en charge). Si les exigences d’E/S par seconde du journal de base de données sont élevées, vous devez entrelacer plusieurs disques afin d’atteindre le volume d’E/S par seconde requis.
* Un certain nombre de disques contenant un ou deux fichiers de base de données de la base de données SAP ainsi que les fichiers de données temporaires SGBD (si le SGBD les prend en charge).

![Configuration de référence de la machine virtuelle IaaS Azure pour SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd  TODO describe Linux structure  )

- - -
> ![Windows][Logo_Windows] Windows
>
> Pour de nombreux clients, nous avons remarqué des configurations où les fichiers binaires SAP et SGBD n’étaient pas installés sur le lecteur c:\ où le système d’exploitation a été installé, par exemple. Plusieurs facteurs pouvaient expliquer cela, mais lorsque nous avons examiné la cause racine, il s’est avéré que les lecteurs étaient petits et les mises à niveau du système d’exploitation nécessitaient un espace supplémentaire il y a de cela 10 à 15 ans. Ces deux conditions s’appliquent bien moins souvent de nos jours. Aujourd’hui, le lecteur c:\ peut être mappé sur des machines virtuelles ou des disques de volume important. Afin de conserver une structure simple des déploiements, il est recommandé de suivre le modèle de déploiement suivant pour les systèmes SAP NetWeaver dans Azure
>
> Le fichier d’échange du système d’exploitation Windows doit se trouver sur le lecteur D: (disque non persistant)
>
> ![Linux][Logo_Linux] Linux
>
> Placez le fichier d’échange Linux dans /mnt/mnt/resource sous Linux, comme décrit dans [cet article][virtual-machines-linux-agent-user-guide]. Le fichier d’échange peut être configuré dans le fichier de configuration de l’agent Linux /etc/waagent.conf. Ajoutez ou modifiez les paramètres suivants :
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Pour activer les modifications, vous devez redémarrer l’agent Linux avec

```
sudo service waagent restart
```

Pour plus d’informations sur la taille recommandée du fichier d’échange, voir la Note de SAP [1597355]

- - -
Le nombre de disques utilisés pour les fichiers de données SGBD et le type de stockage Azure sur lequel ces disques sont hébergés doivent être déterminés par rapport aux exigences d’E/S par seconde et par la latence requise. Les quotas exacts sont décrits dans [cet article (Linux)][virtual-machines-sizes-linux] et [cet article (Windows)][virtual-machines-sizes-windows].

Les leçons que nous avons tirées de l’expérience des déploiements SAP au cours des 2 dernières années peuvent être résumées comme suit :

* Le trafic d’E/S par seconde vers différents fichiers de données n’est pas toujours le même dans la mesure où les systèmes clients existants peuvent contenir des fichiers de données représentant leur(s) base(s) de données SAP de taille différente. Ainsi, l’utilisation d’une configuration RAID s’est avérée plus efficace que plusieurs disques pour placer des fichiers de données LUN issus de ceux-ci. Dans certaines situations, surtout en ce qui concerne le stockage Azure Standard, la fréquence d’E/S par seconde atteignait le quota d’un seul disque par rapport au journal des transactions SGBD. Dans de tels scénarios, l’utilisation de Stockage Premium est recommandée, voire l’agrégation de plusieurs disques de stockage standard à l’aide d’un RAID logiciel.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Configuration d’un RAID logiciel sur Linux][virtual-machines-linux-configure-raid]
> * [Configurer LVM sur une machine virtuelle Linux dans Azure][virtual-machines-linux-configure-lvm]
> * [Secrets d’Azure Storage et optimisations d’E/S dans Linux](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Premium Storage affiche des performances en hausse, en particulier pour les écritures du journal des transactions critiques. Pour les scénarios SAP censés stimuler les performances de production, il est recommandé d’utiliser des séries de machines virtuelles pouvant tirer parti du stockage Azure Premium Storage.

N’oubliez pas que le disque qui contient le système d’exploitation et, comme nous le recommandons, les fichiers binaires de SAP ainsi que la base de données (et la machine virtuelle de base), n’est plus limité à 127 Go. Sa taille peut désormais atteindre 1 To. Cette taille devrait être suffisante pour conserver tous les fichiers nécessaires, par exemple, les journaux de traitements par lots SAP.

Pour obtenir davantage de suggestions et de détails, en particulier pour les machines virtuelles SGBD, voir le [Guide de déploiement de SGBD][dbms-guide]

#### <a name="disk-handling"></a>Gestion de disque
Dans la plupart des scénarios, vous devez créer des disques supplémentaires afin de déployer la base de données SAP vers la machine virtuelle. Nous avons abordé les éléments à prendre en compte pour un certain nombre de disques dans le chapitre [Structure de machine virtuelle/disque pour les déploiements SAP][planning-guide-5.5.1] dans ce document. Le portail Azure permet d’attacher et de détacher des disques de données une fois qu’une machine virtuelle de base est déployée. Les disques peuvent être attachés/détachés lorsque la machine virtuelle est en cours d’exécution, ainsi que lorsque celle-ci est arrêtée. Quand un disque est attaché, le portail Azure propose d’attacher un disque vide ou un disque existant qui n’est pas attaché à une autre machine virtuelle à ce stade.

**Remarque** : Des disques ne peuvent être attachés qu’à une seule machine virtuelle à la fois.

![Attacher/Détacher des disques avec le stockage Azure standard][planning-guide-figure-1400]

Pendant le déploiement d’une nouvelle machine virtuelle, vous pouvez décider si vous souhaitez utiliser des disques managés ou placer vos disques sur des comptes de stockage Azure. Si vous souhaitez utiliser un Stockage Premium, nous vous recommandons de recourir à des disques managés.

Ensuite, vous devez décider si vous souhaitez créer un disque vide ou si vous souhaitez sélectionner un disque existant qui a été chargé plus tôt et doit désormais être attaché à la machine virtuelle.

**IMPORTANT** : vous **NE DEVEZ PAS** utiliser la mise en cache de l’hôte avec un stockage Azure standard. Vous devez conserver les préférences de Cache hôte sur Aucun par défaut. Avec Azure Premium Storage, vous devez activer la Mise en cache en lecture si les caractéristiques d’E/S se lisent principalement comme du trafic d’E/S standard vis-à-vis des fichiers de données de base de données. En présence d’un fichier journal des transactions de base de données, la mise en cache n’est pas recommandée.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Attachement d’un disque de données dans le portail Azure][virtual-machines-linux-attach-disk-portal]
>
> Si les disques sont attachés, vous devez vous connecter à la machine virtuelle pour ouvrir le Gestionnaire de disque Windows. Si le montage automatique n’est pas activé comme le recommande le chapitre [Paramétrage du montage automatique pour les disques attachés][planning-guide-5.5.3], le volume nouvellement attaché doit être mis en ligne et initialisé.
>
> ![Linux][Logo_Linux] Linux
>
> Si les disques sont attachés, vous devez vous connecter à la machine virtuelle et initialiser les disques, comme décrit dans [cet article][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

- - -
Si le nouveau disque est un disque vide, vous devez également le formater. Pour la mise en forme, surtout en ce qui concerne les fichiers journaux et données SGBD, les mêmes recommandations que pour les déploiements sur système nu SGBD s’appliquent.

Comme indiqué dans le chapitre [Concept de la machine virtuelle Microsoft Azure][planning-guide-3.2], un compte de stockage Azure ne fournit pas de ressources infinies en termes de volume d’E/S, d’E/S par seconde, et de volume de données. Généralement, les machines virtuelles SGBD en sont les plus affectées. Il peut être préférable d’utiliser un compte de stockage distinct pour chaque machine virtuelle si vous avez la possibilité de déployer quelques machines virtuelles à un volume d’E/S élevé afin de rester dans la limite du volume du compte de stockage Azure. Dans le cas contraire, vous devez déterminer comment répartir de manière équilibrée ces machines virtuelles entre les différents comptes de stockage sans atteindre la limite de chaque compte de stockage. Pour plus de détails, voir le [Guide de déploiement de SGBD][dbms-guide]. Vous devez également garder ces limitations à l’esprit pour les machines virtuelles de serveur d’application SAP pures ou d’autres machines virtuelles pouvant potentiellement nécessiter des disques durs virtuels supplémentaires. Ces restrictions ne s’appliquent pas si vous utilisez des disques managés. Si vous envisagez d’utiliser un Stockage Premium, nous vous recommandons de recourir à un disque managé.

La géo-réplication des disques durs virtuels dans un compte de stockage est également un sujet pertinent en ce qui concerne les comptes de stockage. La géo-réplication est activée ou désactivée au niveau du compte de stockage et non au niveau de la machine virtuelle. Si la géo-réplication est activée, les disques durs virtuels au sein du compte de stockage sont répliqués dans un autre centre de données Azure se trouvant dans la même région. Avant de prendre votre décision, considérez la restriction suivante :

La géo-réplication Azure fonctionne localement sur chaque disque dur virtuel d’une machine virtuelle et ne réplique pas les E/S par ordre chronologique sur plusieurs disques durs virtuels d’une machine virtuelle. Par conséquent, le disque dur virtuel qui représente la machine virtuelle de base, ainsi que tout disque dur virtuel supplémentaire attaché à la machine virtuelle, est répliqué indépendamment des autres disques durs virtuels. Ainsi, il n’existe aucune synchronisation des modifications des différents disques durs virtuels. Le fait que les E/S sont répliquées indépendamment de l’ordre dans lequel elles sont écrites signifie que la géo-réplication n’a pas de valeur pour les serveurs de base de données dont les bases de données sont distribuées sur plusieurs disques durs virtuels. Outre le SGBD, il existe peut-être d’autres applications dans lesquelles les processus écrivent ou manipulent des données dans différents disques durs virtuels et où il est important de conserver l’ordre des modifications. Si ce dernier est important, la géo-réplication dans Azure ne doit pas être activée. Si vous souhaitez ou avez besoin de la géo-réplication pour un ensemble de machines virtuelles, mais pas pour un autre ensemble, vous pouvez déjà catégoriser les machines virtuelles et leurs disques durs virtuels correspondants dans différents comptes de stockage dont la géo-réplication est activée ou désactivée.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Paramétrage du montage automatique pour les disques attachés
- - -
> ![Windows][Logo_Windows] Windows
>
> Pour les machines virtuelles créées à partir de vos propres images ou disques, il est nécessaire de vérifier et éventuellement de définir le paramètre de montage automatique. Ce paramètre permet à la machine virtuelle de monter les lecteurs attachés/montés de nouveau de manière automatique suite à un redémarrage ou un redéploiement dans Azure .
> Le paramètre est défini pour les images fournies par Microsoft dans la Place de marché Azure.
>
> Pour activer le montage automatique, consultez la documentation de la ligne de commande exécutable diskpart.exe ici :
>
> * [Options de ligne de commande DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Montage automatique](http://technet.microsoft.com/library/cc753703.aspx)
>
> La fenêtre de ligne de commande Windows doit être ouverte en tant qu’administrateur.
>
> Si les disques sont attachés, vous devez vous connecter à la machine virtuelle pour ouvrir le Gestionnaire de disque Windows. Si le montage automatique n’est pas activé comme le recommande le chapitre [Paramétrage du montage automatique pour les disques attachés][planning-guide-5.5.3], le volume nouvellement attaché doit être mis en ligne et initialisé.
>
> ![Linux][Logo_Linux] Linux
>
> Vous devez initialiser un disque vide nouvellement attaché comme décrit dans [cet article][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Vous devez également ajouter de nouveaux disques au /etc/fstab.
>
>

- - -
### <a name="final-deployment"></a>Déploiement final
Pour le déploiement final et ses étapes détaillées, consultez le [Guide de déploiement][deployment-guide], particulièrement en ce qui concerne le déploiement de la surveillance étendue SAP (SAP Extended Monitoring).

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Accès aux systèmes SAP s’exécutant dans des machines virtuelles Azure
Pour les scénarios cloud uniquement, vous pouvez vous connecter à ces systèmes SAP via l’Internet public à l’aide de l’interface utilisateur graphique de SAP. Dans ce cas, les procédures suivantes doivent être appliquées.

Plus loin dans le document, nous aborderons l’autre scénario principal : la connexion aux systèmes SAP dans les déploiements entre locaux ayant une connexion site à site (tunnel VPN) ou une connexion Azure ExpressRoute entre les systèmes locaux et les systèmes Azure.

### <a name="remote-access-to-sap-systems"></a>Accès à distance aux systèmes SAP
Avec Azure Resource Manager, ce modèle ne présente plus de points de terminaison par défaut, comme c’était le cas de l’ancien modèle classique. Tous les ports d’une machine virtuelle ARM Azure sont ouverts tant que :

1. Aucun groupe de sécurité réseau n’est défini pour le sous-réseau ou l’interface réseau. Le trafic réseau vers les machines virtuelles Azure peut être sécurisé via ce que l’on appelle « groupes de sécurité réseau ». Pour plus d’informations, consultez [Présentation du groupe de sécurité réseau][virtual-networks-nsg]
2. Aucun équilibrage de charge Azure Load Balancer n’est défini pour l’interface réseau   

Voir la différence d’architecture entre le modèle classique et l’ARM, comme décrit dans [cet article][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloud-only-scenario"></a>Configuration de la connectivité du système SAP et de l’interface graphique utilisateur de SAP pour le scénario de cloud uniquement
Consultez cet article qui décrit en détail cette rubrique : <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Modification des paramètres de pare-feu au sein de la machine virtuelle
Il peut être nécessaire de configurer le pare-feu sur vos machines virtuelles pour autoriser le trafic entrant vers le système SAP.

- - -
> ![Windows][Logo_Windows] Windows
>
> Par défaut, le Pare-feu Windows au sein d’une machine virtuelle Azure déployée est activé. Vous devez à présent autoriser l’ouverture du port SAP, sinon l’interface utilisateur graphique du SAP ne pourra pas se connecter.
> Pour ce faire :
>
> * Ouvrez Panneau de configuration\Système et sécurité\Pare-feu Windows et sélectionnez **Paramètres avancés**.
> * Cliquez avec le bouton droit sur Règles de trafic entrant, puis cliquez sur **Nouvelle règle**.
> * Dans l’Assistant qui s’ouvre, créez une règle **Port**.
> * Dans l’étape suivante de l’Assistant, laissez le paramètre sur TCP et tapez le numéro de port à ouvrir. Étant donné que notre ID d’instance SAP est 00, nous avons choisi 3200. Si votre instance possède un numéro différent, le port que vous avez défini précédemment en fonction du numéro d’instance doit être ouvert.
> * Dans l’étape suivante de l’Assistant, laissez la case **Autoriser la connexion** cochée.
> * Dans l’étape suivante de l’Assistant, vous devez définir si la règle s’applique au réseau Domaine, Privé et Public. Adaptez la règle en fonction de vos besoins. Toutefois, si vous vous connectez avec l’interface graphique utilisateur SAP de l’extérieur via le réseau public, vous devez appliquer la règle au réseau public.
> * Dans la dernière étape de l’Assistant, nommez la règle et enregistrez votre travail en appuyant sur **Terminer**.
>
> La règle entre immédiatement en vigueur.
>
> ![Définition des règles de port][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Les images Linux dans la Place de marché Azure n’activent pas le pare-feu iptables par défaut et la connexion à votre système SAP devrait fonctionner. Si vous avez activé iptables ou un autre pare-feu, consultez la documentation correspondante pour autoriser le trafic TCP entrant sur le port 32xx (où xx est le numéro de votre système SAP).
>
>

- - -
#### <a name="security-recommendations"></a>Recommandations de sécurité
L’interface graphique utilisateur SAP ne se connecte pas immédiatement aux instances SAP (port 32xx) en cours d’exécution ; elle se connecte d’abord via le port ouvert vers le processus de serveur de messages SAP (port 36xx). Par le passé, le même port a été utilisé par le serveur de messages pour la communication interne vers les instances d’application. Les ports de communication interne peuvent être modifiés pour empêcher la communication par inadvertance entre les serveurs d’application locaux et les serveurs de messages dans Azure. Sur les systèmes ayant été clonés à partir de systèmes locaux, par exemple un clone de développement pour les tests de projet, il est recommandé de basculer la communication interne entre le serveur de messages SAP et ses instances d’application sur un autre numéro de port. Cela peut être fait avec le paramètre de profil par défaut :

> rdisp/msserv_internal
>
>

comme décrit dans [Security Settings for the SAP Message Server](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm) (Paramètres de sécurité du serveur de messagerie SAP)

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Concepts de déploiement cloud uniquement d’instances SAP
### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Machine virtuelle unique avec scénario de démonstration/formation SAP NetWeaver
![Exécution de systèmes de démonstration SAP à machine virtuelle unique portant les mêmes noms de machine virtuelle isolés dans des services cloud Azure][planning-guide-figure-1700]

Dans ce scénario (voir le chapitre [Cloud uniquement][planning-guide-2.1] dans ce document), nous implémentons un système de formation et de démonstration classique dans lequel le scénario complet de formation et de démonstration est contenu dans une seule machine virtuelle. Nous partons du principe que le déploiement s’effectue via des modèles d’image de machine virtuelle. Nous supposons également que plusieurs de ces machines virtuelles de démonstration et de formation doivent être déployées avec les machines virtuelles portant le même nom.

L’hypothèse est que vous avez créé une image de machine virtuelle, comme le décrivent certaines sections du chapitre [Préparation de machines virtuelles avec SAP pour Azure][planning-guide-5.2] dans ce document.

La séquence d’événements pour implémenter le scénario ressemble à ceci :

##### <a name="powershell"></a>PowerShell
* Création d’un groupe de ressources pour chaque paysage de formation et de démonstration

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```
* Création d’un compte de stockage si vous ne souhaitez pas utiliser de disques managés

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Création d’un réseau virtuel pour chaque paysage de formation et de démonstration pour rendre possible l’utilisation du même nom d’hôte et des mêmes adresses IP. Le réseau virtuel est protégé par un groupe de sécurité réseau qui autorise uniquement le trafic vers le port 3389 afin de rendre possible l’accès au Bureau à distance et activer le port 22 pour SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Création d’une adresse IP publique pouvant être utilisée pour accéder à la machine virtuelle à partir d’Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Création d’une interface réseau pour la machine virtuelle

```powershell
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Création d’une machine virtuelle Pour le scénario de cloud uniquement, chaque machine virtuelle aura le même nom. Le SID SAP des instances SAP NetWeaver dans ces machines virtuelles sera également le même. Dans un groupe de ressources Azure, le nom de la machine virtuelle doit être unique, mais dans des groupes de ressources Azure différents, vous pouvez exécuter des machines virtuelles portant le même nom. Le compte par défaut Administrateur pour Windows ou « Root » pour Linux ne sont pas valides. Par conséquent, un nouveau nom d’utilisateur administrateur ainsi qu’un nouveau mot de passe doivent être définis. La taille de la machine virtuelle doit également être définie.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzureRmVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Si vous le souhaitez, ajoutez des disques supplémentaires et restaurez le contenu nécessaire. N’oubliez pas que tous les noms d’objets blob (les URL vers les objets blob) doivent être uniques dans Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM

# Optional: Attach additional Managed Disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzureRmVM
```

##### <a name="cli"></a>Interface de ligne de commande
L’exemple de code suivant peut être utilisé sur Linux. Pour Windows, utilisez PowerShell (comme décrit ci-dessus) ou adaptez l’exemple de façon à utiliser %rgName% au lieu de $rgName, et définissez la variable d’environnement à l’aide de la commande Windows *set*.

* Création d’un groupe de ressources pour chaque paysage de formation et de démonstration

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Création d’un nouveau compte de stockage

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Création d’un réseau virtuel pour chaque paysage de formation et de démonstration pour rendre possible l’utilisation du même nom d’hôte et des mêmes adresses IP. Le réseau virtuel est protégé par un groupe de sécurité réseau qui autorise uniquement le trafic vers le port 3389 afin de rendre possible l’accès au Bureau à distance et activer le port 22 pour SSH.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Création d’une adresse IP publique pouvant être utilisée pour accéder à la machine virtuelle à partir d’Internet

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Création d’une interface réseau pour la machine virtuelle

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Création d’une machine virtuelle Pour le scénario de cloud uniquement, chaque machine virtuelle aura le même nom. Le SID SAP des instances SAP NetWeaver dans ces machines virtuelles sera également le même. Dans un groupe de ressources Azure, le nom de la machine virtuelle doit être unique, mais dans des groupes de ressources Azure différents, vous pouvez exécuter des machines virtuelles portant le même nom. Le compte par défaut Administrateur pour Windows ou « Root » pour Linux ne sont pas valides. Par conséquent, un nouveau nom d’utilisateur administrateur ainsi qu’un nouveau mot de passe doivent être définis. La taille de la machine virtuelle doit également être définie.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Si vous le souhaitez, ajoutez des disques supplémentaires et restaurez le contenu nécessaire. N’oubliez pas que tous les noms d’objets blob (les URL vers les objets blob) doivent être uniques dans Azure.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modèle
Vous pouvez utiliser les exemples de modèle du référentiel azure-quickstart-templates sur Github.

* [Machine virtuelle Linux simple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Machine virtuelle Windows simple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Machine virtuelle à partir d’une image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implémentation d’un ensemble de machines virtuelles qui doivent communiquer au sein d’Azure
Ce scénario de cloud uniquement est le scénario classique de formation et de démonstration dans lequel le logiciel représentant la démonstration/formation est réparti sur plusieurs machines virtuelles. Les différents composants installés dans les différentes machines virtuelles doivent communiquer entre eux. Dans ce scénario, aucune communication réseau locale ou scénario entre sites locaux n’est nécessaire.

Ce scénario est une extension de l’installation que décrit le chapitre [Machine virtuelle unique avec scénario de démonstration/formation SAP NetWeaver][planning-guide-7.1] dans ce document. Dans ce cas, des machines virtuelles supplémentaires seront ajoutées à un groupe de ressources existant. Dans l’exemple suivant, le paysage de formation se compose d’un ASCS SAP/machine virtuelle SCS, d’une machine virtuelle exécutant un système SGBD et d’une machine virtuelle de serveur d’applications SAP.

Avant de créer ce scénario, vous devez considérer les paramètres de base déjà abordés dans le scénario précédent.

#### <a name="resource-group-and-virtual-machine-naming"></a>Dénomination des groupes de ressources et des machines virtuelles
Tous les noms de groupe de ressources doivent être uniques. Développez votre propre schéma de dénomination de vos ressources, tel que `<rg-name`>-suffixe.

Le nom de la machine virtuelle doit être unique dans le groupe de ressources.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Configurer un réseau pour communiquer entre les différentes machines virtuelles
![Ensemble de machines virtuelles au sein d’un réseau virtuel Azure][planning-guide-figure-1900]

Pour éviter des conflits de dénomination avec des clones du même paysage de formation/démonstration, vous devez créer un réseau virtuel Azure pour chaque paysage. La résolution de noms DNS est assurée par Azure, ou vous pouvez configurer votre propre serveur DNS en dehors d’Azure (ne sera pas abordé plus en détail ici). Dans ce scénario, nous ne configurons pas nos propres serveurs DNS. La communication via les noms d’hôte sera activée pour toutes les machines virtuelles du réseau virtuel Azure.

Les raisons de la répartition des paysages de formation ou de démonstration en réseaux virtuels et pas seulement en groupes de ressources pourraient être les suivantes :

* Le paysage SAP tel que configuré a besoin de son propre service AD/OpenLDAP et chacun des paysages doit intégrer un serveur de domaine.  
* Le paysage SAP tel que configuré possède des composants qui doivent fonctionner avec des adresses IP fixes.

Pour plus de détails sur les réseaux virtuels Azure et leur définition, consultez [cet article][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Déployer des machines virtuelles SAP avec une connectivité réseau d’entreprise (intersite)
Vous exécutez un paysage SAP et vous souhaitez répartir le déploiement entre un système nu pour les serveurs haut de gamme du SGBD (système de gestion de base de données), des environnements virtualisés locaux pour les couches d’application et des systèmes SAP configurés à 2 niveaux plus petits et Azure IaaS. Le principe de base est que les systèmes SAP au sein d’un paysage SAP unique doivent communiquer entre eux et avec de nombreux autres composants logiciels déployés dans l’entreprise, indépendamment de leur forme de déploiement. Par ailleurs, aucune différence ne doit être introduite par la forme de déploiement pour la connexion de l’utilisateur final à l’interface utilisateur graphique SAP ou à d’autres interfaces. Ces conditions ne peuvent être satisfaites que si des services Active Directory/OpenLDAP et DNS sont étendus aux systèmes Azure via la connectivité de site à site/multisites ou des connexions privées comme Azure ExpressRoute.

Pour obtenir plus d’informations sur les détails d’implémentation de SAP sur Azure, nous vous conseillons de lire le chapitre [Concepts de déploiement cloud uniquement d’instances SAP][planning-guide-7] dans ce document, qui décrit certaines des constructions de base d’Azure et leur utilisation avec les applications SAP dans Azure.

### <a name="scenario-of-an-sap-landscape"></a>Scénario d’un paysage SAP
Le graphique ci-dessous permet de décrire approximativement le scénario intersite :

![Connectivité de site à site entre des ressources locales et Azure][planning-guide-figure-2100]

Le scénario illustré ci-dessus décrit un scénario où les services AD/OpenLDAP et DNS locaux sont étendus à Azure. Côté local, une certaine plage d’adresses IP est réservée par abonnement Azure. La plage d’adresses IP est affectée à un réseau virtuel Azure côté Azure.

#### <a name="security-considerations"></a>Considérations relatives à la sécurité
La configuration minimale requise est l’utilisation de protocoles de communication sécurisés tels que SSL/TLS pour l’accès au navigateur ou de connexions VPN pour l’accès du système aux services Azure. L’hypothèse est que les entreprises gèrent la connexion VPN entre leur réseau d’entreprise et Azure de manière très différente. Il se peut que certaines sociétés ouvrent d’emblée tous les ports. D’autres souhaiteront peut-être préciser très exactement quels ports doivent être ouverts, etc.

Les ports de communication SAP classiques sont répertoriés dans le tableau ci-dessous. À la base, l’ouverture du port de la passerelle SAP suffit.

| de diffusion en continu | Nom du port | Exemple `<nn`> = 01 | Plage par défaut (min-max.) | Commentaire |
| --- | --- | --- | --- | --- |
| Répartiteur |sapdp`<nn>` voir * |3201 |3200 - 3299 |Répartiteur SAP, utilisé par l’interface utilisateur graphique SAP pour Windows et Java |
| Serveur de messagerie |sapms`<sid`> voir ** |3600 |sapms gratuit`<anySID`> |sid = SAP-System-ID |
| Passerelle |sapgw`<nn`> voir * |3301 |gratuit |Passerelle SAP, utilisée pour les communications CPIC et RFC |
| Routeur SAP |sapdp99 |3299 |gratuit |Seuls les noms de service de l’instance centrale peuvent être réaffectés dans /etc/services à une valeur arbitraire après l’installation. |

*) nn = Numéro d’instance SAP

\**) sid = SAP-System-ID

Pour plus d’informations sur les ports nécessaires pour les différents produits ou services SAP, consultez <http://scn.sap.com/docs/DOC-17124>.
Ce document va vous permettre d’ouvrir des ports dédiés sur le périphérique VPN nécessaire pour les scénarios et produits SAP spécifiques.

Lors du déploiement de machines virtuelles dans un tel scénario, les autres mesures de sécurité peuvent consister à créer un [groupe de sécurité réseau][virtual-networks-nsg] pour définir des règles d’accès.

### <a name="dealing-with-different-virtual-machine-series"></a>Traiter les différentes séries de machines virtuelles
Au cours des 12 derniers mois Microsoft a ajouté divers types de machines virtuelles qui diffèrent en termes de nombre de processeurs virtuels, de mémoire, ou plus important, du matériel utilisé pour leur exécution. Toutes ces machines virtuelles ne sont pas prises en charge par SAP (voir les types de machines virtuelles pris en charge dans la Note de SAP [1928533]). Certaines de ces machines virtuelles s’exécutent sur diverses générations de matériel hôte. Ces générations de matériel hôte sont déployées selon la granularité d’une unité d’échelle Azure. Ce qui signifie qu’il peut arriver que les diverses tailles de machines virtuelles choisies ne puissent pas s’exécuter sur la même unité d’échelle. Un groupe à haute disponibilité est limité dans sa capacité à couvrir les unités d’échelle en fonction de différents matériels.  Par exemple, si vous souhaitez exécuter le SGBD (système de gestion de base de données) sur des machines virtuelles A5-A11 et la couche d’application SAP sur des machines virtuelles de série G, vous devrez déployer un seul système SAP ou différents systèmes SAP dans différents groupes à haute disponibilité.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimer sur une imprimante réseau local à partir d’une instance SAP dans Azure
##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Impression via TCP/IP dans un scénario intersite
La configuration de vos imprimantes réseau locales TCP/IP sur une machine virtuelle Azure est globalement identique à une configuration de même type dans votre réseau d’entreprise, en supposant qu’une connexion ExpressRoute ou « Tunnel » de site à site soit établie.

- - -
> ![Windows][Logo_Windows] Windows
>
> Pour ce faire :
>
> * Certaines imprimantes réseau incluent un assistant de configuration qui facilite leur configuration dans une machine virtuelle Azure. Si aucun assistant n’est fourni avec l’imprimante, la méthode manuelle de configuration de l’imprimante consiste à créer un nouveau port d’imprimante TCP/IP.
> * Ouvrez Panneau de configuration -> Périphériques et imprimantes -> Ajouter une imprimante.
> * Cliquez sur Ajouter une imprimante à l’aide d’une adresse TCP/IP ou d’un nom d’hôte
> * Tapez l’adresse IP de l’imprimante
> * Le port standard de l’imprimante est 9100
> * Si besoin, installez manuellement le pilote d’imprimante qui convient.
>
> ![Linux][Logo_Linux] Linux
>
> * Comme pour Windows, il suffit de suivre la procédure standard d’installation d’une imprimante réseau.
> * Suivez simplement les guides Linux publics pour [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat et Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) sur l’ajout d’une imprimante.
>
>

- - -
![Impression en réseau][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Imprimante basée sur l’hôte via SMB (imprimante partagée) dans un scénario intersite
Les imprimantes basées sur l’hôte ne sont pas compatibles réseau par défaut. Toutefois, une imprimante basée sur l’hôte peut être partagée entre plusieurs ordinateurs sur un réseau tant que l’imprimante est connectée à un ordinateur sous tension. Connectez votre réseau via une connexion de site à site ou ExpressRoute et partagez votre imprimante locale. Le protocole SMB utilise NetBIOS au lieu de DNS comme service de noms. Le nom d’hôte NetBIOS peut être différent du nom d’hôte DNS. Dans une situation standard, les noms d’hôtes NetBIOS et DNS sont identiques. Le domaine DNS est inutile dans l’espace de noms NetBIOS. En conséquence, le nom d’hôte DNS complet comprenant le nom d’hôte DNS et le domaine DNS ne doit pas être utilisé dans l’espace de noms NetBIOS.

Le partage d’imprimante est identifié par le biais d’un nom unique dans le réseau :

* Nom de l’hôte SMB (toujours obligatoire)
* Nom du partage (toujours obligatoire)
* Nom du domaine si le partage d’imprimante n’est pas dans le même domaine que le système SAP.
* En outre, un nom d’utilisateur et un mot de passe peuvent être requis pour accéder au partage d’imprimante.

Activation

- - -
> ![Windows][Logo_Windows] Windows
>
> Partagez votre imprimante locale.
> Dans la machine virtuelle Azure, ouvrez l’Explorateur Windows et tapez le nom du partage d’imprimante.
> Un assistant installation d’imprimante vous guidera tout au long du processus d’installation.
>
> ![Linux][Logo_Linux] Linux
>
> Voici quelques exemples de documents relatifs à la configuration des imprimantes réseau sous Linux ou comprenant un chapitre concernant l’impression sous Linux. Le fonctionnement dans une machine virtuelle Azure Linux est identique tant que la machine virtuelle fait partie d’un réseau VPN :
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL ou Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>Imprimante USB (réacheminement d’imprimante)
La capacité des Services Bureau à distance à fournir aux utilisateurs l’accès à leurs périphériques d’imprimante locale dans une session distante n’est pas disponible dans Azure.

- - -
> ![Windows][Logo_Windows] Windows
>
> Pour plus d’informations sur l’impression avec Windows, consultez : <http://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Intégration de systèmes Azure SAP au système de transport et correction SAP (TMS) en local
Le système de transport et correction SAP (TMS) doit être configuré pour exporter et importer des demandes de transport entre les systèmes du paysage. Nous partons du principe que les instances de développement d’un système SAP (DEV) se trouvent dans Azure, tandis que l’assurance qualité (QA) et les systèmes de production (PRD) se trouvent en local. En outre, nous supposons qu’il existe un répertoire de transport central.

##### <a name="configuring-the-transport-domain"></a>Configurer le domaine de Transport
Configurez votre domaine de transport sur le système que vous avez désigné en tant que contrôleur de domaine de transport, comme décrit dans [Configuring the Transport Domain Controller](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)(Configuration du contrôleur de domaine de transport). Un utilisateur système TMSADM sera créé et la destination RFC nécessaire sera générée. Vous pouvez vérifier ces connexions RFC à l’aide de la transaction SM59. La résolution de nom d’hôte doit être activée dans votre domaine de transport.

Activation

* Dans notre scénario, nous avons décidé que le système d’assurance qualité local sera le contrôleur de domaine CTS. Appelez le STMS de transaction. La boîte de dialogue du TMS s’affiche. Une boîte de dialogue de configuration du domaine de transport s’affiche. (cette boîte de dialogue apparaît uniquement si vous n’avez pas encore configuré de domaine de transport).
* Assurez-vous que l’utilisateur automatiquement créé TMSADM est autorisé (SM59 -> ABAP Connection -> Details -> Utilities(M) -> Authorization Test) (SM59 -> Connexion ABAP -> TMSADM@E61.DOMAIN_E61 -> Détails -> Utilitaires(M) -> Test d’autorisation. L’écran initial du STMS de transaction doit indiquer que ce système SAP fonctionne désormais comme contrôleur du domaine de transport comme indiqué ici :

![Écran initial du STMS de transaction sur le contrôleur de domaine][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inclure des systèmes SAP dans le domaine de transport
La séquence d’intégration d’un système SAP dans un domaine de transport se présente comme suit :

* Sur le système de développement dans Azure, accédez au système de transport (Client 000) et appelez le STMS de transaction. Choisissez Other Configuration (Autre configuration) dans la boîte de dialogue et passez à Include System in Domain (Inclure le système dans le domaine). Définissez le contrôleur de domaine en tant qu’hôte cible ([Inclure des systèmes SAP dans le domaine de transport](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Le système attend maintenant d’être inclus dans le domaine de transport.
* Pour des raisons de sécurité, vous devez ensuite revenir au contrôleur de domaine pour confirmer votre demande. Choisissez System Overview (Vue d’ensemble du système) et Approve of the waiting system (Approbation du système en attente). Puis, confirmez l’invite pour que la configuration soit distribuée.

Ce système SAP contient à présent les informations nécessaires sur tous les autres systèmes SAP du domaine de transport. Dans le même temps, les données d’adresse du nouveau système SAP sont envoyées à tous les autres systèmes SAP, et le système SAP est entré dans le profil de transport du programme de contrôle de transport. Vérifiez si les RFC et l’accès au répertoire de transport du domaine fonctionnent.

Poursuivez par la configuration de votre système de transport comme vous en avez l’habitude, en procédant de la manière décrite dans la documentation [Change and Transport System](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)(Correction et système de Transport).

Activation

* Assurez-vous que votre STMS en local est configuré correctement.
* Assurez-vous que le nom d’hôte du contrôleur de domaine de Transport peut être résolu par votre machine virtuelle sur Azure et vice versa.
* Appelez le STMS de transaction -> Other Configuration -> Include System in Domain (-> Autre configuration -> Inclure le système dans le domaine).
* Confirmez la connexion dans le système TMS local.
* Configurez les itinéraires de transport, les groupes et les couches comme d’habitude.

Dans les scénarios de connexion de site à site, la latence entre les systèmes locaux et Azure peut néanmoins être importante. Si nous suivons la séquence de transport d’objets des systèmes de développement et de test à la production ou si nous envisageons d’appliquer des packages de transport ou de prise en charge aux différents systèmes, vous vous rendez compte qu’en fonction de l’emplacement du répertoire de transport central, certains systèmes subissent une latence élevée en termes de lecture et d’écriture de données dans ce dernier. La situation est semblable aux configurations de paysage SAP où les différents systèmes sont répartis sur différents centres de données très éloignés les uns des autres.

Pour pallier cette latence et permettre aux systèmes d’accélérer la lecture ou l’écriture vers ou depuis le répertoire de transport, vous pouvez configurer deux domaines de transport STMS (un pour les systèmes locaux et un pour les systèmes dans Azure) et lier les domaines de transport. Consultez la documentation qui explique les principes de ce concept dans SAP TMS : <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Activation

* Configurer un domaine de transport sur chaque emplacement (local et Azure) à l’aide du STMS de transaction <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Associez les domaines avec un lien de domaine et confirmez le lien entre les deux domaines.
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuez la configuration au système lié.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Trafic RFC entre des instances SAP situées dans Azure et en local (intersite)
Le trafic RFC entre les systèmes locaux et dans Azure doit fonctionner. Pour configurer une connexion, appelez la transaction SM59 dans un système source où vous devez définir une connexion RFC vers le système cible. La configuration est similaire à la configuration standard d’une connexion RFC.

Nous supposons que dans les scénarios intersite, les machines virtuelles exécutant des systèmes SAP devant communiquer entre eux se trouvent dans le même domaine. Par conséquent, la configuration d’une connexion RFC entre les systèmes SAP ne diffère pas des étapes d’installation et des entrées des scénarios locaux.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Accéder à des partages de fichiers locaux à partir d’instances SAP situées dans Azure ou inversement
Les instances SAP situées dans Azure doivent accéder aux partages de fichiers se trouvant dans les locaux de l’entreprise. En outre, les instances SAP locales ont besoin d’accéder aux partages de fichiers se trouvant dans Azure. Pour activer les partages de fichiers, vous devez configurer les options d’autorisation et de partage sur le système local. Veillez à ouvrir les ports sur la connexion VPN ou ExpressRoute entre Azure et votre centre de données.

## <a name="supportability"></a>Prise en charge
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Solution de surveillance Azure pour SAP
Pour activer la surveillance des systèmes SAP stratégiques sur Azure, les outils de surveillance SAP SAPOSCOL ou SAP Host Agent obtiennent des données à partir de l’hôte du service de machine virtuelle Azure via une extension de surveillance Azure pour SAP. Dans la mesure où les exigences de SAP étaient très spécifiques aux applications SAP, Microsoft a décidé de ne pas implémenter de manière générique la fonctionnalité requise dans Azure, mais de la laisser aux clients pour le déploiement des composants de surveillance et des configurations nécessaires sur leurs machines virtuelles exécutant Azure. Toutefois, la gestion du cycle de vie et du déploiement des composants de surveillance est principalement automatisée par Azure.

#### <a name="solution-design"></a>Conception de la solution
La solution développée pour activer la surveillance SAP repose sur l’architecture de l’infrastructure de l’agent et des extensions de machine virtuelle Azure. L’idée de l’infrastructure de l’agent et des extensions de machine virtuelle Azure consiste à autoriser l’installation d’applications logicielles disponibles dans la galerie d’extensions de machines virtuelles Azure dans une machine virtuelle. Le principe à l’origine de ce concept est de permettre (dans les cas d’extension de la surveillance Azure pour SAP), le déploiement de fonctionnalités spéciales sur une machine virtuelle et la configuration de ces logiciels au moment du déploiement.

L’« agent de machine virtuelle Azure » qui permet une gestion des extensions de machines virtuelles Azure spécifiques au sein de la machine virtuelle est intégré aux machines virtuelles Windows par défaut pendant la création de machines virtuelles dans le portail Azure. Dans le cas de SUSE, Red Hat ou Oracle Linux, l’agent de machine virtuelle fait déjà partie de l’image de la Place de marché Azure. Dans le cas du chargement d’une machine virtuelle LINUX d’un système local vers Azure, l’agent de machine virtuelle doit être installé manuellement.

Les blocs de construction de base de la solution de surveillance dans Azure pour SAP ressemble à ceci :

![Composants d’extension Microsoft Azure][planning-guide-figure-2400]

Comme indiqué dans le diagramme de blocs ci-dessus, une partie de la solution de surveillance pour SAP est hébergée dans l’image de machine virtuelle Azure et la galerie d’extensions Azure, qui est un référentiel à réplication globale géré par des opérations Azure. L’équipe SAP/MS travaillant à l’implémentation Azure de SAP est chargée d’utiliser les opérations Azure pour publier de nouvelles versions de l’extension de surveillance Azure pour SAP.

Lorsque vous déployez une nouvelle machine virtuelle Windows, l’agent de machine virtuelle Azure est automatiquement ajouté à la machine virtuelle. La fonction de cet agent consiste à coordonner le chargement et la configuration des extensions Azure pour la surveillance des systèmes SAP NetWeaver. Pour les machines virtuelles LINUX, l’agent de machine virtuelle Azure fait déjà partie de l’image du système d’exploitation de la Place de marché Azure.

Toutefois, il reste une étape à exécuter par le client. Il s’agit de l’activation et de la configuration de la collecte des performances. Le processus relatif à la configuration est automatisé par un script PowerShell ou une commande d’interface de ligne de commande. Le script PowerShell peut être téléchargé à partir du Centre de scripts Microsoft Azure, comme décrit dans le [Guide de déploiement][deployment-guide].

L’Architecture globale de la solution de surveillance Azure pour SAP ressemble à ce qui suit :

![Solution de surveillance Azure pour SAP NetWeaver][planning-guide-figure-2500]

**Pour connaître la procédure exacte et obtenir les étapes détaillées concernant l’utilisation de ces applets de commande PowerShell ou la commande de l’interface de ligne de commande au cours des déploiements, suivez les instructions fournies dans le [Guide de déploiement][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Intégration d’instances SAP situées dans Azure dans SAProuter
Les instances SAP s’exécutant dans Azure doivent également être accessibles à partir de SAProuter.

![Connexion réseau du routeur SAP][planning-guide-figure-2600]

Un programme SAProuter permet la communication TCP/IP entre systèmes participants en l’absence de connexion IP directe. Cela présente l’avantage de ne pas devoir recourir à une connexion de bout en bout entre les partenaires de communication au niveau du réseau. Le programme SAProuter est à l’écoute du port 3299 par défaut.
Pour connecter des instances SAP via un programme SAProuter, vous devez fournir la chaîne et le nom d’hôte SAProuter lors de toute tentative de connexion.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Jusqu’à présent, ce document a porté sur SAP NetWeaver en général ou sur la pile SAP NetWeaver ABAP. Cette brève section répertorie les considérations spécifiques à la pile SAP Java. L’une des applications les plus importantes exclusivement basées sur SAP NetWeaver Java est le portail d’entreprise SAP. Les autres applications, telles que SAP PI et SAP Solution Manager utilisent toutes deux les piles SAP NetWeaver ABAP et Java. Par conséquent, il est certainement nécessaire de prendre en compte également les aspects spécifiques relatifs à la pile SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>Portail d’entreprise SAP
La configuration d’un portail SAP dans une machine virtuelle Azure ne diffère pas d’une installation locale si vous effectuez un déploiement dans des scénarios intersite. Étant donné que le DNS s’effectue en local, les paramètres de port des différentes instances peuvent être considérés comme étant configurés localement. Les recommandations et restrictions décrites jusqu’ici dans ce document s’appliquent en général à une application comme le portail d’entreprise SAP ou la pile SAP NetWeaver Java.

![Portail SAP exposé][planning-guide-figure-2700]

Un scénario de déploiement spécifique par certains clients est l’exposition directe du portail d’entreprise SAP à Internet tandis que l’hôte de machine virtuelle est connecté au réseau d’entreprise via une connexion Tunnel VPN de site à site ou ExpressRoute. Pour ce scénario, vous devez vous assurer que certains ports sont ouverts et ne sont pas bloqués par un pare-feu ou un groupe de sécurité réseau. Le même mécanisme devrait être appliqué lorsque vous souhaitez vous connecter à une instance SAP Java à partir d’un système local dans un scénario de cloud uniquement.

L’URI du portail initial est http(s):`<Portalserver`>:5XX00/irj où le port est formé par 50000 (numéro du système ?? 100). L’URI du portail par défaut du système SAP 00 est `<dns name`>.`<azure region`>.Cloudapp.azure.com:PublicPort/irj. Pour plus d’informations, consultez <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuration du point de terminaison][planning-guide-figure-2800]

Si vous souhaitez personnaliser l’URL et/ou les ports de votre portail d’entreprise SAP, consultez la documentation suivante :

* [Change Portal URL (Modifier l’URL du portail)](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Change Default port numbers, Portal port numbers (Modifier les numéros de port par défaut et les numéros de ports du portail)](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Haute disponibilité (HA) et récupération d’urgence (DR)pour SAP NetWeaver s’exécutant sur des machines virtuelles Azure
### <a name="definition-of-terminologies"></a>Définition des termes
Le terme **haute disponibilité (HA)** est généralement associé à un ensemble de technologies qui réduisent les interruptions de service informatique en assurant la continuité des services informatiques par le biais de composants redondants, tolérants aux pannes ou protégés contre le basculement, dans le **même** centre de données. Dans notre cas, au sein d’une région Azure.

La **récupération d’urgence (DR)** vise également à réduire l’interruption des services informatiques et leur récupération, mais entre **différents** centres de données, généralement éloignés de plusieurs centaines de kilomètres les uns des autres. Dans notre cas, généralement entre différentes régions Azure de la même région géopolitique ou tel que défini par vous en tant que client.

### <a name="overview-of-high-availability"></a>Vue d’ensemble de la haute disponibilité
Nous pouvons séparer la discussion concernant la haute disponibilité SAP dans Azure en deux parties :

* La **haute disponibilité de l’infrastructure Azure**, par exemple, celle du calcul (machines virtuelles), du réseau, du stockage, etc., et ses avantages en termes d’augmentation de la disponibilité des applications SAP.
* La **haute disponibilité des applications SAP**, par exemple, celle des composants logiciels SAP :
  * Serveurs d’application SAP
  * Instance SAP ASCS/SCS
  * Serveur de base de données

et comment il peut être combiné avec la haute disponibilité de l’infrastructure Azure.

La haute disponibilité SAP dans Azure présente des différences par rapport à la haute disponibilité SAP dans un environnement physique local ou virtuel. Le document de SAP suivant décrit les configurations de haute disponibilité SAP standard dans des environnements virtualisés sous Windows : <http://scn.sap.com/docs/DOC-44415>. Il n’existe aucune configuration haute disponibilité SAP intégrée pour Linux comparable à celle de Windows. Des informations concernant la haute disponibilité SAP en local pour Linux sont disponibles ici : <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>haute disponibilité de l’infrastructure Azure
Il existe un contrat de niveau de service pour machine virtuelle unique de 99,9 %. Pour avoir une idée de ce à quoi peut ressembler la disponibilité d’une machine virtuelle unique, vous pouvez simplement calculer le produit des différents contrats SLA Azure disponibles : <https://azure.microsoft.com/support/legal/sla/>.

La base de calcul est de 30 jours par mois ou 43 200 minutes. Par conséquent, le temps d’interruption de 0,05 % correspond à 21,6 minutes. Comme d’habitude, la disponibilité des différents services se multiplie de la façon suivante :

(Service de disponibilité #1/100) * (Service de disponibilité #2/100) * (Service de disponibilité #3/100) 

comme ce qui suit :

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975, soit une disponibilité globale de 99,75 %.

#### <a name="virtual-machine-vm-high-availability"></a>Haute disponibilité de la machine virtuelle
Deux types d'événements de plateforme Azure peuvent avoir un effet sur la disponibilité de vos machines virtuelles : la maintenance planifiée et la maintenance non planifiée.

* Les événements de maintenance planifiés sont des mises à jour périodiques effectuées par Microsoft sur la plateforme sous-jacente Azure pour améliorer la fiabilité, les performances et la sécurité de l'infrastructure hébergeant vos machines virtuelles.
* Les événements de maintenance non planifiés ont lieu lorsque l'infrastructure physique ou matérielle sous-jacente de votre machine virtuelle a connu une défaillance. Cela comprend les défaillances du réseau local, du disque local ou au niveau du rack. Lorsqu’une défaillance de ce type est détectée, la plateforme Azure migre automatiquement votre machine virtuelle du serveur physique défectueux vers un serveur physique sain. De tels événements sont rares, mais peuvent entraîner un redémarrage de votre machine virtuelle.

Pour plus d’informations, consultez cette documentation : <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Redondance d’Azure Storage
Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité et une haute disponibilité, conformément au contrat de niveau de service Stockage Azure, même en cas de défaillances matérielles temporaires.

Dans la mesure où Stockage Azure consiste à conserver trois images des données par défaut, RAID5 ou RAID1 sur plusieurs disques Azure n’est pas nécessaire.

Vous trouverez de plus amples instructions dans cet article : <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utiliser le redémarrage de la machine virtuelle d’infrastructure Azure pour permettre une plus haute disponibilité des applications SAP
Si vous décidez de ne pas utiliser de fonctionnalités telles que le clustering de basculement Windows Server (WSFC) ou Pacemaker sur Linux (uniquement pris en charge pour SLES versions 12 et ultérieures), le redémarrage de la machine virtuelle Azure permet de protéger un système SAP contre les interruptions de services planifiées ou non de l’infrastructure du serveur physique Azure et de la plateforme Azure sous-jacente globale.

> [!NOTE]
> Il est important de mentionner que le redémarrage de la machine virtuelle Azure protège principalement les machines virtuelles et PAS les applications. Le redémarrage de la machine virtuelle n’offre pas de haute disponibilité pour les applications SAP, mais un certain niveau de disponibilité de l’infrastructure et, par conséquent, indirectement une « plus haute disponibilité » des systèmes SAP. Par ailleurs, il n’existe actuellement aucun contrat de niveau de service (SLA) concernant le temps nécessaire au redémarrage d’une machine virtuelle après une interruption d’hôte planifiée ou non. Par conséquent, cette méthode de haute disponibilité n’est pas adaptée aux composants essentiels d’un système SAP, tel qu’un (A)SCS ou un SGBD (système de gestion de base de données).
>
>

Le stockage est un autre élément important d’infrastructure pour la haute disponibilité. Par exemple, le contrat de niveau de service Stockage Azure assure une disponibilité de 99,9 %. Si une personne déploie toutes les machines virtuelles et ses disques sur un compte Azure Storage unique, l’indisponibilité potentielle d’Azure Storage entraînera celle de toutes les machines virtuelles placées dans ce compte Azure Storage et également celle de tous les composants SAP s’exécutant sur ces dernières.  

Au lieu de placer toutes les machines virtuelles dans un seul compte Azure Storage, vous pouvez également utiliser les comptes de stockage dédiés pour chaque machine virtuelle et ainsi augmenter la disponibilité globale des machines virtuelles et des applications SAP en utilisant plusieurs comptes Azure Storage indépendants.

Les disques managés Azure sont automatiquement placés dans le domaine d’erreur de la machine virtuelle à laquelle ils sont attachés. Si vous placez deux machines virtuelles dans un groupe à haute disponibilité et que vous utilisez des disques managés, la plateforme se charge également de distribuer ces derniers dans différents domaines d’erreur. Si vous envisagez d’utiliser un Stockage Premium, nous vous recommandons vivement de recourir à des disques managés également.

Voici un exemple de ce à quoi pourrait ressembler une architecture de système SAP NetWeaver utilisant la haute disponibilité d’infrastructure Azure et des comptes de stockage :

![Utiliser la haute disponibilité d’infrastructure Azure pour permettre une « plus haute » disponibilité de l’application SAP][planning-guide-figure-2900]

Voici un exemple de ce à quoi pourrait ressembler une architecture de système SAP NetWeaver utilisant la haute disponibilité d’infrastructure Azure et des disques managés :

![Utiliser la haute disponibilité d’infrastructure Azure pour permettre une « plus haute » disponibilité de l’application SAP][planning-guide-figure-2901]

Pour les composants SAP stratégiques, nous avons obtenu jusqu’ici ce qui suit :

* La haute disponibilité des serveurs d’applications SAP

  Les instances de serveur d’applications SAP sont des composants redondants. Chaque instance de serveur d’applications SAP est déployée sur sa propre machine virtuelle s’exécutant dans un domaine de mise à niveau et d’erreur différent (voir les chapitres [Domaines d’erreur][planning-guide-3.2.1] et [Domaines de mise à niveau][planning-guide-3.2.2]). Cela est assuré par l’utilisation des groupes à haute disponibilité Azure (voir le chapitre [Groupes à haute disponibilité Azure][planning-guide-3.2.3]). L’indisponibilité planifiée ou non planifiée potentielle d’un domaine de mise à niveau ou d’erreur Azure entraîne l’indisponibilité d’un nombre limité de machines virtuelles avec leurs instances de serveurs d’applications SAP.

  Chaque instance de serveur d’applications SAP est placée dans son propre compte Azure Storage : l’indisponibilité potentielle d’un compte de stockage Azure provoquera l’indisponibilité d’une seule machine virtuelle et de son instance de serveur d’applications SAP. Toutefois, sachez qu’un abonnement Azure est limité quand au nombre de comptes Azure Storage. Pour assurer le démarrage automatique de l’instance (A)SCS après le redémarrage de la machine virtuelle, veillez à définir le paramètre de démarrage automatique dans le profil de démarrage de l’instance (A)SCS décrit dans le chapitre [Utilisation du démarrage automatique pour les instances SAP][planning-guide-11.5].
  Pour plus de détails, voir également le chapitre [Haute disponibilité pour les serveurs d’applications SAP][planning-guide-11.4.1].

  Même si vous utilisez des disques managés, ces disques sont également stockés dans un compte de stockage Azure et peuvent être indisponibles en cas d’une panne de stockage.

* *plus haute* disponibilité de l’instance (A)SCS SAP

  Ici, nous utilisons le redémarrage de la machine virtuelle Azure afin de protéger la machine virtuelle avec l’instance (A)SCS SAP installée. En cas d’interruption planifiée ou non de serveurs Azure, les machines virtuelles sont redémarrées sur un autre serveur disponible. Comme mentionné précédemment, le redémarrage de la machine virtuelle Azure protège les machines virtuelles mais PAS les applications, qui sont dans ce cas l’instance (A)SCS. Ce redémarrage permet d’obtenir indirectement une « plus haute disponibilité » de l’instance (A)SCS SAP. Pour assurer le démarrage automatique de l’instance (A)SCS après le redémarrage de la machine virtuelle, veillez à définir le paramètre de démarrage automatique du profil de démarrage de l’instance (A)SCS décrit dans le chapitre [Utilisation du démarrage automatique pour les instances SAP][planning-guide-11.5]. Cela signifie que l’instance (A)SCS en tant que point de défaillance unique s’exécutant sur une machine virtuelle unique sera un facteur déterminant de la disponibilité de l’ensemble du paysage SAP.

* *plus haute* disponibilité du serveur du SGBD (système de gestion de base de données)

  Ici, comme pour le cas d’utilisation de l’instance (A)SCS SAP, nous utilisons le redémarrage de la machine virtuelle pour protéger la machine virtuelle sur laquelle est installé le logiciel de SGBD (système de gestion de base de données). Ce redémarrage nous permet également d’obtenir une « plus haute disponibilité » du logiciel de SGBD (système de gestion de base de données).
  Le SGBD (système de gestion de base de données) s’exécutant sur une seule machine virtuelle est également un point de défaillance unique, qui est le facteur déterminant de disponibilité de l’ensemble du paysage SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Haute disponibilité de l’application SAP sur Azure IaaS
Pour obtenir une haute disponibilité de l’ensemble du système SAP, nous devons protéger tous les composants critiques du système SAP, par exemple, les serveurs d’applications SAP redondants et les composants uniques (par exemple, le point de défaillance unique) comme l’instance (A)SCS SAP et le SGBD (système de gestion de base de données).

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Haute disponibilité pour les serveurs d’applications SAP
Pour les instances de boîte de dialogue/serveurs d’applications SAP, il n’est pas nécessaire de réfléchir à une solution de haute disponibilité spécifique. La haute disponibilité peut être obtenue simplement par la redondance afin d’obtenir une présence suffisante dans les différentes machines virtuelles. Ces instances doivent toutes être placées dans le même groupe à haute disponibilité Azure pour éviter que les machines virtuelles soient mises à jour simultanément lors d’une interruption de maintenance planifiée. La fonctionnalité de base qui repose sur différents domaines de mise à niveau et d’erreur au sein d’une unité d’échelle Azure a déjà été présentée dans le chapitre [Domaines de mise à niveau][planning-guide-3.2.2]. Les groupes à haute disponibilité Azure ont été présentés dans le chapitre [Groupes à haute disponibilité Azure][planning-guide-3.2.3] dans ce document.

Le nombre de domaines de mise à niveau et d’erreur pouvant être utilisé par un groupe à haute disponibilité Azure au sein d’une unité d’échelle Azure est limité. Cela signifie que si vous placez un certain nombre de machines virtuelles dans un groupe à haute disponibilité, tôt ou tard plusieurs machines virtuelles aboutissent au même domaine de mise à niveau ou d’erreur.

En déployant plusieurs instances de serveurs d’applications SAP dans leurs machines virtuelles dédiées et en supposant que nous avons cinq domaines de mise à niveau, nous obtenons l’image suivante. Le nombre maximal réel de domaines d’erreur et de mise à jour au sein d’un groupe à haute disponibilité peut changer à l’avenir :

![Haute disponibilité des serveurs d’applications SAP dans Azure][planning-guide-figure-3000]

Pour plus d’informations, consultez cette documentation : <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Haute disponibilité pour l’instance (A)SCS SAP sous Windows
Le cluster de basculement Windows Server (WSFC) est une solution fréquemment utilisée pour protéger l’instance (A)SCS SAP. Il est également intégré à sapinst sous forme d’une « installation à haute disponibilité ». À ce stade, l’infrastructure Azure n’est pas en mesure de fournir la fonctionnalité pour configurer le cluster de basculement Windows Server comme cela est effectué en local.

Depuis janvier 2016, la plateforme de cloud Azure exécutant le système d’exploitation Windows ne donne pas la possibilité d’utiliser un volume partagé de cluster sur un disque partagé entre deux machines virtuelles Azure.

Cependant, une solution valide consiste dans l’utilisation d’un logiciel tiers qui fournit un volume partagé par la réplication de disque synchrone et transparente pouvant être intégré au WSFC. Cette approche implique que seul le nœud du cluster actif soit en mesure d’accéder à l’une des copies de disque à un moment donné. Depuis janvier 2016, cette configuration de haute disponibilité est prise en charge pour protéger l’instance (A)SCS SAP sur le SE invité Windows des machines virtuelles Azure en combinaison avec SIOS DataKeeper du logiciel tiers.

La solution SIOS DataKeeper fournit une ressource de cluster de disque partagée pour les clusters de basculement Windows grâce aux éléments suivants :

* Disque dur Azure virtuel supplémentaire attaché à chacune des machines virtuelles se trouvant dans une configuration de cluster Windows
* SIOS DataKeeper Cluster Edition s’exécutant sur les deux nœuds de machine virtuelle
* Configuration de SIOS DataKeeper Cluster Edition de sorte qu’il mette en miroir de manière synchrone le contenu du volume attaché au disque dur virtuel supplémentaire depuis les machines virtuelles source sur le volume attaché du disque dur virtuel supplémentaire de la machine virtuelle cible.
* SIOS DataKeeper fait abstraction des volumes locaux source et cible et les présente à un cluster de basculement Windows comme disque partagé unique.

Vous trouverez tous les détails concernant l’installation d’un cluster de basculement Windows avec SIOS DataKeeper et SAP dans le livre blanc [Clustering SAP ASCS Instance using Windows Server Failover Cluster on Azure with SIOS DataKeeper][ha-guide-classic] (Clustering de l’instance ASCS SAP à l’aide du cluster de basculement Windows Server sur Azure avec SIOS DataKeeper).

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Haute disponibilité pour l’instance (A)SCS SAP sous Linux
Depuis décembre 2015, il n’existe aucun équivalent du WSFC de disque partagé pour les machines virtuelles Linux sur Azure. Les autres solutions consistant à utiliser un logiciel tiers, tel que SISO pour Windows, ne sont pas encore validée pour l’exécution de SAP sous Linux sur Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Haute disponibilité pour l’instance de base de données SAP
La configuration de la haute disponibilité du SGBD (système de gestion de base de données) SAP classique repose sur deux machines virtuelles de SGBD où la fonctionnalité de haute disponibilité du SGBD sert à répliquer les données issues de l’instance de SGBD active sur l’instance de SGBD passive de la seconde machine virtuelle.

La fonctionnalité de haute disponibilité et de récupération d’urgence pour les SGBD (systèmes de gestion de base de données) en général, ainsi que pour les SGBD spécifiques est décrite dans le [Guide de déploiement de SGBD][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Haute disponibilité de bout en bout pour l’ensemble du système SAP
Voici deux exemples d’architecture de haute disponibilité SAP NetWeaver complète dans Azure (un concernant Windows et un autre pour Linux).

Disques managés uniquement : comme expliqué ci-dessous, il se peut que les concepts soient légèrement modifiés quand vous déployez de nombreux systèmes SAP et que le nombre de machines virtuelles déployées dépasse la limite maximale de comptes de stockage par abonnement. Dans ces cas, les disques durs virtuels doivent être combinés au sein d’un compte de stockage. En général, vous pourriez procéder ainsi en combinant les disques virtuels des machines virtuelles de la couche d’application SAP de différents systèmes SAP.  Nous avons également associé différents disques durs virtuels de plusieurs machines virtuelles de SGBD (système de gestion de base de données) de différents systèmes SAP dans un compte Azure Storage. Tout en gardant à l’esprit les limites d’IOPS des comptes de stockage Azure (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Haute disponibilité sous Windows
![Architecture de haute disponibilité de l’application SAP NetWeaver avec SQL Server dans Azure IaaS][planning-guide-figure-3200]

Les constructions Azure suivantes sont utilisées pour le système SAP NetWeaver afin de réduire l’impact par la correction de l’hôte et des problèmes d’infrastructure :

* Le système complet est déployé sur Azure (obligatoire : la couche SGBD (système de gestion de base de données), l’instance (A)SCS et la couche d’application complète doivent s’exécuter au même emplacement).
* Le système complet s’exécute au sein d’un abonnement Azure (requis).
* Le système complet s’exécute au sein d’un réseau virtuel Azure (requis).
* La séparation des machines virtuelles d’un système SAP en trois groupes à haute disponibilité est possible même si toutes les machines virtuelles appartiennent au même réseau virtuel.
* Toutes les machines virtuelles exécutant des instances de SGBD (système de gestion de base de données) d’un système SAP se trouvent dans un groupe à haute disponibilité. Nous partons du principe qu’il existe plusieurs machines virtuelles exécutant des instances de SGBD (système de gestion de base de données) par système depuis l’utilisation des fonctionnalités de haute disponibilité de SGBD natives, telles que SQL Server AlwaysOn ou Oracle Data Guard.
* Toutes les machines virtuelles exécutant des instances de SGBD (système de gestion de base de données) utilisent leur propre compte de stockage. Les fichiers de données et journaux du SGBD (système de gestion de base de données) sont répliqués à partir d’un compte de stockage vers un autre compte de stockage à l’aide des fonctions de haute disponibilité du SGBD (système de gestion de base de données) qui synchronisent les données. L’indisponibilité d’un compte de stockage entraîne l’indisponibilité d’un nœud de cluster Windows SQL, mais pas du service SQL Server tout entier.
* Toutes les machines virtuelles exécutant l’instance (A)SCS d’un système SAP se trouvent dans un groupe à haute disponibilité. Un cluster de basculement Windows Server est configuré au sein de ces machines virtuelles pour protéger l’instance (A)SCS.
* Toutes les machines virtuelles exécutant des instances (A)SCS utilisent leur propre compte de stockage. Les fichiers d’instance (A)SCS et le dossier global SAP sont répliqués à partir d’un compte de stockage sur un autre compte de stockage à l’aide de la réplication SIOS DataKeeper. L’indisponibilité d’un compte de stockage entraîne l’indisponibilité d’un nœud de cluster Windows du service (A)SCS, mais pas de l’intégralité de ce dernier.
* TOUTES les machines virtuelles représentant la couche du serveur d’applications SAP se trouvent dans un groupe à haute disponibilité tiers.
* TOUTES les machines virtuelles exécutant des serveurs d’applications SAP utilisent leur propre compte de stockage. L’indisponibilité d’un compte de stockage entraîne l’indisponibilité d’un serveur d’applications SAP, où d’autres serveurs d’applications SAP continuent de s’exécuter.

La figure suivante illustre le même paysage à l’aide de disques managés.

![Architecture de haute disponibilité de l’application SAP NetWeaver avec SQL Server dans Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Haute disponibilité sous Linux
L’architecture de haute disponibilité SAP sous Linux sur Azure est principalement la même que pour Windows comme décrit ci-dessus. Consultez la note SAP [1928533] pour obtenir la liste des solutions de haute disponibilité prises en charge.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Utilisation du démarrage automatique pour les instances SAP
SAP a proposé une fonctionnalité permettant de démarrer automatiquement des instances SAP immédiatement après le démarrage du système d’exploitation au sein de la machine virtuelle. Les étapes exactes ont été documentées dans l’article de base de connaissances SAP [1909114]. Toutefois, SAP recommande de ne plus utiliser ce paramètre, car aucun contrôle n’est exercé sur l’ordre de redémarrage de l’instance, en supposant que plusieurs machines virtuelles ont été affectées ou que plusieurs instances s’exécutent sur une machine virtuelle. Dans l’hypothèse d’un scénario Azure classique d’un serveur d’applications SAP sur une machine virtuelle et dans le cas du redémarrage éventuel d’une seule machine virtuelle, le démarrage automatique n’est pas vraiment critique et peut être activé en ajoutant ce paramètre :

    Autostart = 1

Dans le profil de démarrage de l’instance ABAP et/ou Java SAP.

> [!NOTE]
> Le paramètre de démarrage automatique peut avoir également quelques inconvénients. Le paramètre déclenche le démarrage d’une instance ABAP ou Java SAP au démarrage du service Windows/Linux associé de l’instance. Cela est certainement le cas au démarrage du système d’exploitation. Toutefois, les redémarrages des services SAP sont également courants pour la fonctionnalité de gestion du cycle de vie du logiciel SAP, telle que SUM ou d’autres mises à jour et mises à niveau. Ces fonctionnalités n’attendent pas du tout le redémarrage automatique d’une instance. Par conséquent, le paramètre de démarrage automatique doit être désactivé avant d’exécuter ces tâches. Le paramètre de démarrage automatique ne doit pas être utilisé pour les instances SAP en cluster, telles que ASCS/SCS/CI.
>
>

Consultez les informations supplémentaires concernant le démarrage automatique des instances SAP ici :

* [Start/Stop SAP along with your Unix Server Start/Stop (Démarrage/Arrêt de SAP à l’aide de la fonctionnalité correspondante de votre serveur Unix)](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starting and Stopping SAP NetWeaver Management Agents (Démarrage et arrêt des agents de gestion SAP NetWeaver)](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [How to enable auto Start of HANA Database (Activation du démarrage automatique de la base de données HANA)](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Systèmes SAP à 3 couches plus vastes
Les aspects relatifs à la haute disponibilité des configurations SAP à 3 couches ont déjà été abordés dans les sections précédentes. Mais qu’en est-il des systèmes où la configuration requise du serveur du SGBD (système de gestion de base de données) est trop volumineuse pour se trouver dans Azure, alors que la couche d’application SAP pourrait y être déployée ?

#### <a name="location-of-3-tier-sap-configurations"></a>Emplacement des configurations SAP à 3 couches
Le fractionnement de la couche Application elle-même ou de la couche Application et SGBD (système de gestion de base de données) en local et dans Azure n’est pas pris en charge. Un système SAP est totalement déployé localement OU dans Azure. Il est également impossible que certains serveurs d’applications s’exécutent en local et d’autres dans Azure. Il s’agit du point de départ de la discussion. Le déploiement des composants du SGBD (système de gestion de base de données) d’un système SAP et de la couche du serveur d’applications SAP dans deux régions Azure différentes n’est pas non plus pris en charge. Par exemple, le SGBD (système de gestion de base de données) à l’Ouest des États-Unis et la couche d’application SAP au Centre des États-Unis. La non prise en charge de telles configurations s’explique par la sensibilité de latence de l’architecture de SAP NetWeaver.

Toutefois, au cours de l’année dernière, les partenaires des centres de données ont développé la colocalisation dans les régions Azure. Ces colocalisations sont souvent très proches des centres de données Azure physiques au sein d’une région Azure. La courte distance et la connexion des ressources au sein de la colocalisation via ExpressRoute dans Azure peuvent entraîner une latence inférieure à 2 ms. Dans ce cas, il est possible de rechercher la couche SGBD (système de gestion de base de données) (y compris le stockage SAN/NAS) dans une colocalisation de ce type, ainsi que la couche Application dans Azure. Depuis décembre 2015, nous n’assistons à aucun déploiement de ce type. Toutefois, divers clients effectuant des déploiements d’applications autres que SAP utilisent déjà de telles approches.

### <a name="offline-backup-of-sap-systems"></a>Sauvegarde hors connexion de systèmes SAP
En fonction de la configuration SAP choisie (à 2 ou 3 couches), il peut être nécessaire de procéder à une sauvegarde. du contenu de la machine virtuelle elle-même et de la base de données. Les sauvegardes associées au SGBD (système de gestion de base de données) doivent être effectuées avec des méthodes de base de données. Pour une description détaillée des différentes bases de données, voir le [Guide SGBD (système de gestion de base de données)][dbms-guide]. En revanche, les données SAP peuvent être sauvegardées hors connexion (y compris le contenu de la base de données) comme décrit dans cette section ou en ligne comme indiqué dans la section suivante.

La sauvegarde hors connexion nécessite essentiellement l’arrêt de la machine virtuelle via le portail Azure et une copie du disque de la machine virtuelle de base et de tous les disques attachés à la machine virtuelle. Cela permet de préserver une image dans le temps de la machine virtuelle et des disques associés à celle-ci. Il est recommandé de copier les « sauvegardes » dans un autre compte Azure Storage. Par conséquent, la procédure décrite dans le chapitre [Copie de disques entre comptes Azure Storage][planning-guide-5.4.2] dans ce document s’applique.
Outre à l’aide du portail Azure, l’arrêt est possible via PowerShell ou l’interface CLI comme indiqué ici : <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Une restauration de cet état consisterait en la suppression de la machine virtuelle de base, ainsi que de ses disques d’origine et des disques montés, la copie des disques enregistrés dans le compte de stockage d’origine ou le groupe de ressources pour les disques managés, puis le redéploiement du système.
Cet article montre comment rédiger le script de ce processus dans PowerShell : <http://www.westerndevs.com/azure-snapshots/>

Veillez à installer une nouvelle licence SAP dans la mesure où la restauration d’une sauvegarde de machine virtuelle, comme décrit ci-dessus, entraîne la création d’une clé matérielle.

### <a name="online-backup-of-an-sap-system"></a>Sauvegarde en ligne d’un système SAP
La sauvegarde du SGBD (système de gestion de base de données) est effectuée à l’aide de méthodes de SGBD spécifiques, comme décrit dans le [Guide SGBD (système de gestion de base de données)][dbms-guide].

Il est possible de sauvegarder d’autres machines virtuelles dans le système SAP à l’aide de la fonctionnalité de sauvegarde de machines virtuelles Azure. La fonctionnalité de sauvegarde de machines virtuelles Azure a été commercialisée au début de l’année 2015. Il s’agit désormais de la méthode de sauvegarde standard d’une machine virtuelle complète dans Azure. Elle permet de stocker les sauvegardes dans Azure et de restaurer une machine virtuelle.

> [!NOTE]
> Depuis décembre 2015, l’utilisation de la sauvegarde de machine virtuelle NE permet pas de conserver l’ID de machine virtuelle unique utilisé pour la gestion des licences SAP. Cela signifie qu’une restauration à partir d’une sauvegarde de machine virtuelle nécessite l’installation d’une nouvelle clé de licence SAP, dans la mesure où la machine virtuelle restaurée est considérée comme une nouvelle machine virtuelle et non un remplacement de l’ancienne machine sauvegardée.
>
> ![Windows][Logo_Windows] Windows
>
> En théorie, les machines virtuelles qui exécutent des bases de données peuvent également être sauvegardées de manière cohérente si les systèmes SGBD prennent en charge Windows VSS (Volume Shadow Copy Service <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) comme le fait SQL Server, par exemple.
> Toutefois, n’oubliez pas qu’une restauration dans le temps des bases de données peut ne pas être possible, selon les sauvegardes de machine virtuelle Azure. Ainsi, il est recommandé d’effectuer des sauvegardes de bases de données avec des systèmes SGBD plutôt que de compter sur la sauvegarde de machines virtuelles Azure.
>
> Pour vous familiariser avec la sauvegarde de machines virtuelles Azure, commencez ici : <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Il est également possible de combiner Microsoft Data Protection Manager installé sur une machine virtuelle Azure et la sauvegarde Azure pour sauvegarder/restaurer des bases de données. Des informations supplémentaires sont disponibles ici : <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Il n’existe pas d’équivalent de Windows VSS sous Linux. Par conséquent, seules les sauvegardes cohérentes au niveau des fichiers sont possibles. Les sauvegardes cohérentes au niveau de l’application ne sont pas prises en charge. La sauvegarde du SGBD (système de gestion de base de données) SAP doit être effectuée à l’aide de la fonctionnalité SGBD. Le système de fichiers, qui comprend les données SAP, peut être enregistré, par exemple, à l’aide de tar, comme indiqué ici : <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure comme site de récupération d’urgence pour les paysages SAP de production
Depuis le milieu de l’année 2014, des extensions de divers composants relatifs à Hyper-V, System Center et Azure permettent l’utilisation d’Azure comme site de récupération d’urgence pour les machines virtuelles basées sur Hyper-V et exécutées en local.

Un billet de blog détaillant comment déployer cette solution est documenté ici : <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Résumé
Voici les points clés de la haute disponibilité des systèmes SAP dans Azure :

* À ce stade, le point de défaillance unique de SAP ne peut pas être sécurisé exactement comme il peut l’être dans des déploiements en local. La raison est que les clusters de disques partagés sont encore impossibles à créer dans Azure sans utiliser de logiciels tiers.
* Pour la couche SGBD (système de gestion de base de données), vous devez utiliser la fonctionnalité SGBD qui ne repose pas sur la technologie de cluster de disque partagé. Pour plus de détails à ce sujet, voir le [Guide SGBD (système de gestion de base de données)][dbms-guide].
* Pour réduire l’impact des problèmes au sein des domaines d’erreur dans l’infrastructure Azure ou de la maintenance des hôtes, vous devez utiliser les groupes à haute disponibilité Azure :
  * Il est recommandé de disposer d’un groupe à haute disponibilité pour la couche d’application SAP.
  * Il est recommandé de disposer d’un groupe à haute disponibilité distinct pour la couche SGBD (système de gestion de base de données) SAP.
  * Il n’est PAS recommandé d’appliquer le même groupe à haute disponibilité pour les machines virtuelles de systèmes SAP différents.
  * Nous vous recommandons d’utiliser des disques managés Premium.
* Pour la sauvegarde de la couche SGBD (système de gestion de base de données) SAP, voir le [Guide SGBD (système de gestion de base de données)][dbms-guide].
* La sauvegarde des instances de boîte de dialogue SAP n’est pas très utile, dans la mesure où il est généralement plus rapide de redéployer des instances de boîte de dialogue simples.
* La sauvegarde de la machine virtuelle qui contient le répertoire global du système SAP, et tous les profils des différentes instances, est utile et doit être effectuée avec la sauvegarde Windows ou, par exemple, tar sous Linux. Dans la mesure où il existe des différences entre Windows Server 2008 (R2) et Windows Server 2012 (R2), qui facilitent la sauvegarde à l’aide des versions Release les plus récentes de Windows Server, nous vous recommandons d’exécuter Windows Server 2012 (R2) en tant que système d’exploitation invité Windows.
