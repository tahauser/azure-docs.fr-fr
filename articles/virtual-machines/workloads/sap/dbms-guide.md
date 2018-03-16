---
title: "Déploiement SGBD de machines virtuelles Azure pour SAP NetWeaver | Microsoft Docs"
description: "Déploiement SGBD de machines virtuelles Azure pour SAP NetWeaver"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 855fc155bbdef9a3a0ab8415067102e607fab0f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Déploiement SGBD de machines virtuelles Azure pour SAP NetWeaver
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ce guide fait partie de la documentation sur l’implémentation et le déploiement des logiciels SAP sur Microsoft Azure. Avant de lire ce guide, consultez le [Guide de planification et d’implémentation][planning-guide]. Ce document présente le déploiement de différents systèmes de gestion de base de données relationnelle (SGBDR) et des produits associés avec SAP sur Microsoft Azure Virtual Machines à l’aide des fonctionnalités Infrastructure as a Service (IaaS) d’Azure.

Ce document vient compléter la documentation sur l’installation SAP et les notes SAP, qui constituent les principales ressources pour l’installation et le déploiement des logiciels SAP sur des plateformes.

## <a name="general-considerations"></a>Considérations d’ordre général
Ce chapitre aborde l’exécution des systèmes SGBD de type SAP sur les machines virtuelles Azure. Il comporte peu de références à des systèmes SGBD spécifiques. Les systèmes SGBD spécifiques sont couverts dans la suite de ce document, après ce chapitre.

### <a name="definitions-upfront"></a>Préambule : définitions
Les termes suivants seront utilisés dans le document :

* IaaS : Infrastructure as a Service.
* PaaS : Platform as a Service.
* SaaS : Software as a Service.
* Composant SAP : application SAP telle que ECC, BW, Solution Manager ou EP.  Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
* Environnement SAP : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier telle que le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
* Paysage SAP : ce terme fait référence à l’ensemble des ressources SAP dans le paysage informatique d’un client. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
* Système SAP : ensemble couche SGBD/couche Application, tel que celui d’un système de développement SAP ERP, d’un système de test SAP BW, d’un système de production SAP CRM, etc. Dans les déploiements Azure, il n’est pas possible de répartir ces deux couches entre un système local et Azure. Cela signifie qu’un système SAP doit être déployé en local ou dans Azure. Vous pouvez toutefois déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local.
* Déploiement cloud uniquement : déploiement dans lequel l’abonnement Azure n’est pas connecté via une connexion ExpressRoute ou de site à site à l’infrastructure réseau locale. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des déploiements « cloud uniquement ». Les machines virtuelles déployées avec cette méthode sont accessibles via Internet et des points de terminaison Internet publics affectés aux machines virtuelles dans Azure. Le répertoire Active Directory et le serveur DNS locaux ne sont pas étendus à Azure dans ces types de déploiements. Par conséquent, les machines virtuelles ne font pas partie de l’annuaire Active Directory local. Remarque : Dans ce document, les déploiements « cloud uniquement » sont définis comme des paysages SAP complets exécutés uniquement dans Azure, sans extension d’Active Directory ni passage d’une résolution de noms locale à une résolution dans le cloud public. Les configurations uniquement cloud ne sont pas prises en charge pour les configurations ou systèmes SAP de production dans lesquels des ressources SAP STMS ou d’autres ressources locales doivent être utilisées entre les systèmes SAP hébergés sur Azure et les ressources en local.
* Déploiement entre différents locaux : décrit un scénario dans lequel les machines virtuelles sont déployées dans un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou ExpressRoute entre les différents centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, les instances locales d’Active Directory et le serveur DNS local à Azure. Le paysage local est étendu aux ressources Azure de l’abonnement. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services (SGBD, par exemple) sur ces machines virtuelles. La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure est possible. Ce scénario devrait être le plus souvent utilisé pour le déploiement de ressources SAP sur Azure. Pour plus d’informations, consultez [cet article][vpn-gateway-cross-premises-options] et [cet article][vpn-gateway-site-to-site-create].

> [!NOTE]
> Les déploiements intersites de systèmes SAP dans lesquels des machines virtuelles Azure exécutant des systèmes SAP font partie d’un domaine local sont pris en charge pour les systèmes SAP de production. Les configurations entre différents locaux sont prises en charge pour le déploiement d’éléments ou de l’intégralité des paysages SAP dans Azure. Ces machines virtuelles doivent faire partie du domaine et des services Active Directory locaux même lorsque l’intégralité du paysage SAP est exécutée dans Azure. Dans les versions précédentes de la documentation, nous avons parlé des scénarios hybrides, où le terme *hybride* tient au fait qu’il existe une connectivité intersite entre les sites locaux et Azure. Dans ce cas, *hybride* signifie également que les machines virtuelles dans Azure font partie du répertoire Active Directory local.
> 
> 

Certaines documentations Microsoft décrivent les scénarios intersites de façon légèrement différente, en particulier pour les configurations haute disponibilité SGBD. Dans les documents portant sur SAP, le scénario de déploiement sur plusieurs sites se résume à l’établissement d’une connectivité site à site ou privée (ExpressRoute) et à la répartition du paysage SAP entre les sites locaux et Azure.

### <a name="resources"></a>Ressources
Les déploiements SAP sur Azure sont décrits dans les guides suivants :

* [Planification et implémentation de machines virtuelles Azure pour SAP NetWeaver][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP NetWeaver][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP NetWeaver (le présent document)][dbms-guide]

Les notes SAP suivantes concernent le déploiement SAP sur Azure :

| Numéro de la note | Intitulé |
| --- | --- |
| [1928533] |Applications SAP sur Azure : produits et types de machines virtuelles pris en charge |
| [2015553] |SAP sur Microsoft Azure : configuration requise |
| [1999351] |Résolution des problèmes de surveillance Azure améliorée pour SAP |
| [2178632] |Métriques de surveillance clés pour SAP sur Microsoft Azure |
| [1409604] |Virtualisation sur Windows : surveillance améliorée |
| [2191498] |SAP sur Linux avec Azure : surveillance améliorée |
| [2039619] |Exécution d’applications SAP sur Microsoft Azure à l’aide d’Oracle Database : produits et versions pris en charge |
| [2233094] |DB6 : Exécution d’applications SAP sur Azure à l’aide d’IBM DB2 pour Linux, UNIX et Windows - Informations supplémentaires |
| [2243692] |Linux sur Microsoft Azure Virtual Machines (IaaS) : problèmes de licence SAP |
| [1984787] |SUSE LINUX Enterprise Server 12 : Notes d’installation |
| [2002167] |Red Hat Enterprise Linux 7.x : Installation et mise à niveau |
| [2069760] |Installation et mise à niveau SAP pour Oracle Linux 7.x |
| [1597355] |Recommandations relatives à l’espace d’échange pour Linux |
| [2171857] |Oracle Database 12C - Prise en charge du système de fichiers dans Linux |
| [1114181] |Oracle Database 11g - Prise en charge du système de fichiers dans Linux |


Consultez également le [Wiki SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) qui regroupe toutes les notes SAP pour Linux.

Vous devez avoir une connaissance pratique de l’architecture Microsoft Azure, ainsi que du déploiement et du fonctionnement des machines virtuelles Microsoft Azure Virtual Machines. Pour plus d’informations, consultez <https://azure.microsoft.com/documentation/>

> [!NOTE]
> Nous ne traitons **pas** ici des offres Platform as a Service (PaaS) de la plateforme Microsoft Azure. Ce document explique comment exécuter un système de gestion de base de données (SGBD) sur des machines virtuelles Microsoft Azure (IaaS) de la même manière que dans un environnement local. Les capacités et fonctionnalités de base de données de ces deux services sont très différentes et ne doivent pas être confondues. Voir aussi : <https://azure.microsoft.com/services/sql-database/>
> 
> 

Comme il est ici question de l’IaaS, l’installation et la configuration de Windows, de Linux et du SGBD sont globalement les mêmes que pour une machine virtuelle ou un ordinateur nu que vous installeriez en local. Cependant, les décisions relatives à l’implémentation de la gestion de l’architecture et des systèmes diffèrent sur certains points. Ce document a pour objet d’expliquer les différences spécifiques de gestion de l’architecture et des systèmes auxquelles vous devez être préparé lors de l’utilisation de l’IaaS.

Les différences abordées dans ce document portent sur les points généraux suivants :

* Planification de la disposition des machines virtuelles/disques durs virtuels des systèmes SAP pour s’assurer que la disposition des fichiers de données est appropriée et que le nombre potentiel d’E/S par seconde est suffisant pour la charge de travail.
* Considérations relatives à la mise en réseau lors de l’utilisation de l’IaaS.
* Fonctionnalités de base de données spécifiques à utiliser afin d’optimiser la disposition des bases de données.
* Considérations relatives à la sauvegarde et à la restauration de l’IaaS.
* Utilisation de différents types d’images pour le déploiement.
* Haute disponibilité dans Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Structure d’un déploiement SGBDR
Pour pouvoir suivre ce chapitre, il est nécessaire de comprendre le contenu de [ce chapitre][deployment-guide-3] du [Guide de déploiement][deployment-guide]. Vous devez savoir ce qui distingue les différentes séries de machines virtuelles et connaître les différences entre les offres de stockage Azure Standard et Premium Storage avant de lire ce chapitre.

Jusqu’à mars 2015, la taille des disques contenant un système d’exploitation était limitée à 127 Go. Cette limitation a été supprimée en mars 2015 (pour plus d’informations, consultez <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). Depuis lors, les disques contenant le système d’exploitation peuvent présenter la même taille que n’importe quel autre disque. Cependant, nous continuons à privilégier une structure de déploiement dans laquelle le système d’exploitation, le SGBD et les fichiers binaires SAP éventuels sont séparés des fichiers de base de données. Les systèmes SAP exécutés dans Machines Virtuelles Azure doivent donc avoir la même machine virtuelle (ou le même disque) de base installée avec le système d’exploitation, les exécutables du système de gestion de base de données (SGBD) et les exécutables SAP. Les fichiers de données et les journaux du SGBD sont stockés dans le stockage Azure (Standard ou Stockage Premium) dans des fichiers de disques durs virtuels distincts et attachés en tant que disques logiques à la machine virtuelle image du système d’exploitation Azure d’origine. 

Selon que vous tirez parti du stockage Azure Standard ou du Stockage Premium (par exemple, en utilisant les machines virtuelles de la série DS ou de la série GS), il existe d’autres quotas dans Azure qui sont expliqués [ici (pour Linux)][virtual-machines-sizes-linux] et [ici (pour Windows)][virtual-machines-sizes-windows]. Lors de la planification des disques, vous devez trouver le meilleur compromis entre les quotas pour les éléments suivants :

* Le nombre de fichiers de données.
* Le nombre de disques qui contiennent les fichiers
* Les quotas d’E/S par seconde pour un même disque
* Le débit de données par disque
* Le nombre de disques supplémentaires possibles par taille de machine virtuelle
* Le débit de stockage global qu’une machine virtuelle peut offrir.

Azure applique un quota d’E/S par seconde pour chaque disque de données. Ces quotas sont différents selon que les disques sont hébergés dans le stockage Azure Standard ou le Stockage Premium. En outre, les latences d’E/S diffèrent considérablement entre ces deux types de stockage, Stockage Premium offrant de bien meilleures latences d’E/S. Pour chaque type de machines virtuelles, vous ne pouvez attacher qu’un nombre limité de disques. Une autre restriction concerne le fait que seuls certains types de machines virtuelles peuvent tirer parti d’Azure Premium Storage. Cela signifie que le choix d’un certain type de machines virtuelles peut non seulement être guidé par les besoins en puissance de processeur et en mémoire, mais également par les exigences relatives aux E/S par seconde, à la latence et au débit de disque, auxquelles permettent généralement de répondre l’augmentation du nombre de disques et le Stockage Premium. Avec le Stockage Premium en particulier, la taille d’un disque peut également être dictée par le nombre d’E/S et le débit qui doit être atteint par chaque disque.

Étant donné que le taux global d’E/S par seconde, le nombre de disques montés et la taille de la machine virtuelle sont tous liés, une configuration Azure d’un système SAP peut être différente de celle de son déploiement local. Les limites d’E/S par seconde par LUN sont généralement configurables dans les déploiements locaux, alors qu’avec Azure Storage elles sont fixes ou, dans le cas de Premium Storage, elles dépendent du type de disque. Avec des déploiements locaux, nous voyons des configurations client de serveurs de base de données qui utilisent de nombreux volumes différents pour des exécutables spéciaux tels que SAP et le SGBD, ou des volumes spéciaux pour les bases de données ou les espaces de table temporaires. La migration d’un système local de ce type vers Azure peut donner lieu à une perte de bande passante d’E/S par seconde en gaspillant un disque pour des exécutables ou des bases de données qui n’effectuent pas un grand nombre d’E/S par seconde, voire aucune. Dans les machines virtuelles Azure, nous recommandons donc si possible d’installer le SGBD et les exécutables SAP sur le disque du système d’exploitation.

L’emplacement des fichiers de base de données et des fichiers journaux, ainsi que le type de stockage Azure utilisé, doivent être dictés par les besoins en E/S par seconde, en latence et en débit. Afin de bénéficier d’un nombre suffisant d’E/S par seconde pour le journal des transactions, il se peut que vous soyez obligé d’utiliser plusieurs disques pour le fichier du journal des transactions ou d’utiliser un plus grand disque Stockage Premium. Dans ce cas, vous devez créer un RAID logiciel (par exemple, un pool de stockage Windows pour Windows ou MDADM et LVM (Logical Volume Manager) pour Linux) avec les disques qui contiennent le journal des transactions.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Dans une machine virtuelle Azure, le lecteur D:\ est un lecteur non persistant soutenu par des disques locaux présents sur le nœud de calcul Azure. Comme il est non persistant, cela signifie que toutes les modifications apportées au contenu sur le lecteur D:\ sont perdues lors du redémarrage de la machine virtuelle. Par modifications, nous entendons les fichiers enregistrés, les répertoires créés, les applications installées, etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> Les machines virtuelles Azure Linux montent automatiquement un lecteur à l’emplacement /mnt/resource. Il s’agit d’un lecteur non persistant soutenu par des disques locaux présents sur le nœud de calcul Azure. Comme il est non persistant, toutes les modifications apportées au contenu de l’emplacement /mnt/resource seront perdues si vous redémarrez la machine virtuelle. Par modifications, nous entendons les fichiers enregistrés, les répertoires créés, les applications installées, etc.
> 
> 

- - -
Selon la série de machines virtuelles Azure, les disques locaux du nœud de calcul affichent des performances différentes qui peuvent être classées de la façon suivante :

* A0-A7 : performances très limitées. Non utilisables pour autre chose que le fichier d’échange Windows.
* A8-A11 : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série D : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série DS : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série G : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.
* Série GS : très bonnes caractéristiques de performances, avec quelque 10 000 E/S par seconde et un débit supérieur à 1 Go/s.

Les chiffres donnés ci-dessus s’appliquent aux types de machines virtuelles certifiés pour SAP. Les séries de machines virtuelles présentant un nombre d’E/S par seconde et un débit excellents sont adaptées à certaines fonctionnalités de SGBD, telles que tempdb ou l’espace de table temporaire.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Mise en cache pour les machines virtuelles et les disques de données
Lorsque nous créons des disques de données dans le portail ou montons les disques chargés sur des machines virtuelles, nous pouvons choisir de mettre ou non en cache le trafic d’E/S entre la machine virtuelle et les disques situés dans le stockage Azure. Azure Standard et Premium Storage font appel à deux technologies différentes pour ce type de mise en cache. Dans les deux cas, la mise en cache est soutenue par des disques sur les mêmes lecteurs que ceux utilisés par le disque temporaire (D:\ dans Windows ou/mnt/resource dans Linux) de la machine virtuelle.

Pour le stockage Azure Standard, les types de mise en cache possibles sont les suivantes :

* Aucune mise en cache
* Mise en cache en lecture
* Mise en cache en lecture et en écriture

Pour obtenir des performances homogènes et déterministes, vous devez définir la mise en cache dans le stockage Azure Standard de tous les disques contenant **les fichiers de données SGBD, les fichiers journaux et l’espace de table sur AUCUNE**. La valeur par défaut peut être conservée pour la mise en cache de la machine virtuelle.

Pour le stockage Azure Premium Storage, les options de mise en cache suivantes sont disponibles :

* Aucune mise en cache
* Mise en cache en lecture

Pour le stockage Premium Azure, il est recommandé d’utiliser la **mise en cache de lecture pour les fichiers de données** de la base de données SAP et de choisir l’option avec laquelle **aucune mise en cache n’est effectuée pour les disques des fichiers journaux**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID logiciel
Comme nous l’avons déjà indiqué plus haut, vous devez équilibrer le nombre d’E/S par seconde nécessaire pour les fichiers de base de données en fonction du nombre de disques configurables et du nombre maximal d’E/S par seconde qu’une machine virtuelle offre par disque ou type de disque Stockage Premium. Le moyen le plus simple pour gérer la charge d’E/S sur les disques consiste à créer un RAID logiciel avec les différents disques. Ensuite, placez un certain nombre de fichiers de données du SGBD SAP sur les LUN issus du RAID logiciel. En fonction des exigences, il peut être préférable d’utiliser le stockage Premium. En effet, deux des trois disques Stockage Premium offrent un quota d’E/S par seconde supérieur à celui des disques basés sur le stockage Standard. De plus, Azure Premium Storage garantit une latence d’E/S nettement inférieure. 

Il en va de même pour le journal des transactions des différents systèmes de SGBD. Le fait qu’un grand nombre d’entre eux ajoutent d’autres fichiers Tlog n’aide en rien, car les systèmes de SGBD n’écrivent que dans un seul fichier à la fois. Si des taux d’E/S par seconde supérieurs à ce qu’un disque basé sur le stockage Standard peut offrir sont nécessaires, vous pouvez agréger plusieurs disques Standard ou utiliser un type de disque Stockage Premium plus grand qui, en plus d’un taux d’E/S par seconde supérieur, assure une latence plusieurs fois inférieure pour l’écriture des E/S dans le journal des transactions.

Les situations rencontrées dans les déploiements Azure qui justifient l’utilisation d’un RAID logiciel sont les suivantes :

* Le journal des transactions et le journal de rétablissement nécessitent plus d’E/S par seconde que ce qu’un disque Azure unique peut offrir. Comme indiqué ci-dessus, vous pouvez résoudre ce problème en créant un LUN sur plusieurs disques à l’aide d’un RAID logiciel.
* La charge de travail d’E/S est inégale sur les différents fichiers de données de la base de données SAP. Un fichier de données peut par exemple atteindre très souvent le quota, tandis que les autres fichiers de données n’approchent même pas du quota d’E/S par seconde d’un disque unique. Dans ce cas, la solution la plus simple consiste à créer un LUN sur plusieurs disques à l’aide d’un RAID logiciel. 
* Vous ne connaissez pas la charge de travail d’E/S exacte par fichier de données et avez seulement une idée grossière de la charge de travail d’E/S globale requise pour le SGBD. Le plus simple consiste à créer un LUN à l’aide d’un RAID logiciel. La somme des quotas des disques formant ce LUN devrait satisfaire le taux d’E/S par seconde connu.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Il est conseillé d’utiliser des espaces de stockage Windows si vous exécutez Windows Server 2012 ou version ultérieure. Cette méthode est plus efficace que l’agrégation de versions antérieures de Windows. Vous devrez peut-être créer les pools de stockage et les espaces de stockage Windows à l’aide de commandes PowerShell lorsque vous utilisez Windows Server 2012 comme système d’exploitation. Les commandes PowerShell sont disponibles ici : <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Seuls MDADM et LVM (Logical Volume Manager) sont pris en charge pour créer un RAID logiciel sur Linux. Pour plus d’informations, consultez les articles suivants :
> 
> * [Configuration d’un RAID logiciel sur Linux][virtual-machines-linux-configure-raid] (pour MDADM)
> * [Configurer LVM sur une machine virtuelle Linux dans Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
Voici les points à noter concernant l’utilisation de séries de machines virtuelles compatibles avec le Stockage Premium Azure :

* Exigence de latences d’E/S proches de celles offertes par les périphériques SAN/NAS.
* Exigence d’une latence d’E/S plusieurs fois inférieure à celle offerte par le stockage Azure Standard.
* Nombre d’E/S par seconde par machine virtuelle plus élevé que ce que permettent d’atteindre plusieurs disques Standard avec un certain type de machine virtuelle.

Comme le stockage Azure sous-jacent réplique chaque disque sur au moins trois nœuds de stockage, une simple agrégation RAID 0 peut être utilisée. Il est inutile d’utiliser RAID 5 ou RAID 1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Le Stockage Microsoft Azure stocke la machine virtuelle de base (avec le système d’exploitation) et les disques ou objets blob sur au moins trois nœuds de stockage distincts. Lors de la création d’un compte de stockage ou d’un disque managé, plusieurs options de protection sont proposées :

![Géoréplication activée pour le compte Azure Storage][dbms-guide-figure-100]

La réplication locale Azure Storage (localement redondante) fournit plusieurs niveaux de protection contre la perte de données en cas de défaillance de l’infrastructure que peu de clients pourraient se permettre de déployer. Comme illustré ci-dessus, il existe quatre options différentes, avec une cinquième option qui est une variante de l’une des trois premières. En les examinant de plus près, nous pouvons distinguer :

* **Stockage localement redondant Premium (LRS)**: Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d’E/S. Trois réplicas des données sont créés au sein du même centre de données d’une région Azure. Les copies se trouvent dans des domaines d’erreur et de mise à niveau distincts (pour plus d’informations sur ces concepts, consultez [ce chapitre][planning-guide-3.2] du [Guide de planification][planning-guide]). Dans le cas où une réplica des données devient hors service en raison d’une défaillance de nœud de stockage ou de disque, un nouveau réplica est généré automatiquement.
* **Stockage localement redondant (LRS)** : trois réplicas des données sont créés au sein du même centre de données d’une région Azure. Les copies se trouvent dans des domaines d’erreur et de mise à niveau distincts (pour plus d’informations sur ces concepts, consultez [ce chapitre][planning-guide-3.2] du [Guide de planification][planning-guide]). Dans le cas où une réplica des données devient hors service en raison d’une défaillance de nœud de stockage ou de disque, un nouveau réplica est généré automatiquement. 
* **Stockage géo-redondant (GRS)** : dans ce cas, trois réplicas supplémentaires des données sont envoyés par réplication asynchrone dans une autre région Azure située la plupart du temps dans la même région géographique (par exemple, Europe du Nord ou Europe de l’Ouest). Cela crée trois réplicas supplémentaires, donc six au total. Une variante de cette option permet d’utiliser les données stockées dans la région Azure géo-répliquée à des fins de lecture (Stockage géo-redondant avec accès en lecture).
* **Stockage redondant dans une zone (ZRS)** : dans ce cas, les trois réplicas des données restent dans la même région Azure. Comme l’explique [ce][planning-guide-3.1] chapitre du [Guide de planification][planning-guide], une région Azure peut correspondre à plusieurs centres de données situés à proximité les uns des autres. Avec le stockage localement redondant, les réplicas seraient réparties dans les différents centres de données qui forment une région Azure unique.

Des informations supplémentaires sont disponibles [ici][storage-redundancy].

> [!NOTE]
> Pour les déploiements SGBD, l’utilisation du stockage géo-redondant est déconseillée.
> 
> La géo-réplication Azure Storage est asynchrone. Les disques montés sur une même machine virtuelle ne sont pas répliqués simultanément. Par conséquent, il ne convient pas de répliquer les fichiers SGBD qui sont répartis sur différents disques ou déployés sur un RAID logiciel basé sur plusieurs disques. Les logiciels de SGBD nécessitent que le stockage sur disque persistant soit synchronisé précisément sur les différents LUN, et disques ou broches sous-jacents. Les logiciels de SGBD font appel à différents mécanismes pour ordonner les activités d’écriture d’E/S. Un logiciel SGBD signale que le stockage sur disque ciblé par la réplication est endommagé si les activités varient ne serait-ce que de quelques millisecondes. Par conséquent, si vous avez besoin d’une configuration de base de données avec une base de données répartie sur plusieurs disques géorépliqués, cette réplication doit être effectuée à l’aide des fonctionnalités de la base de données. Vous ne devez pas vous appuyer sur la géo-réplication Azure Storage pour accomplir cette tâche. 
> 
> Le problème est plus simple à expliquer avec un exemple de système. Supposons que vous disposiez d’un système SAP chargé dans Azure qui utilise huit disques contenant les fichiers de données du SGBD, et un disque contenant le fichier journal de transactions. Sur chacun de ces neuf disques, les données seront écrites à l’aide d’une même méthode dépendant du SGBD, que les données soient écrites dans les fichiers de données ou le fichier journal de transactions.
> 
> Afin de géorépliquer correctement les données et de conserver une image de base de données cohérente, il faudrait répliquer le contenu des neuf disques dans l’ordre exact où les opérations d’E/S y ont été exécutées. Toutefois, la géoréplication du stockage Azure ne permet pas de déclarer des dépendances entre différents disques. Cela signifie que la géoréplication du Stockage Microsoft Azure ignore que le contenu de ces neuf disques est lié, et que les modifications de données ne sont cohérentes que quand la réplication est effectuée dans l’ordre où les opérations d’E/S ont eu lieu sur l’ensemble des neuf disques.
> 
> Outre la forte probabilité que les images géo-répliquées dans ce scénario ne produisent pas une image de base de données cohérente, le stockage géo-redondant peut également avoir un impact très négatif sur les performances. En résumé, n’utilisez pas ce type de redondance de stockage pour les charges de travail du type SGBD.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mappage de disques durs virtuels à des comptes de stockage du service Azure Virtual Machines
Ce chapitre s’applique uniquement aux comptes de stockage Azure. Si vous prévoyez d’utiliser des disques managés, les limites indiquées dans ce chapitre ne vous concernent pas. Pour plus d’informations sur les disques managés, lisez le chapitre [Disques managés][dbms-guide-managed-disks] de ce guide.

Un compte de stockage Azure est non seulement le fait d’un administrateur, mais également l’objet de limitations. Les limitations varient selon qu’il s’agit d’un compte de stockage Azure Standard ou Azure Premium Storage. Les capacités et limitations exactes sont répertoriées [ici][storage-scalability-targets]

Concernant le stockage Azure Standard, il est important de noter qu’il y a une limite d’E/S par seconde par compte de stockage (ligne contenant **Taux de demandes total** dans [l’article][storage-scalability-targets]). De plus, il y a une limite initiale de 100 comptes de stockage par abonnement Azure (depuis juillet 2015). Par conséquent, il est recommandé d’équilibrer les E/S par seconde des machines virtuelles entre plusieurs comptes de stockage lorsque le stockage Azure Standard est utilisé, alors que dans l’idéal, une machine virtuelle unique doit si possible utiliser un seul compte de stockage. Dans le cas de déploiements SGBD où chaque disque dur virtuel hébergé dans le stockage Azure Standard peut atteindre sa limite de quota, vous devez donc déployer seulement 30 à 40 disques durs virtuels par compte de stockage Azure faisant appel au stockage Azure Standard. Par contre, si vous tirez parti du stockage Azure Premium Storage et souhaitez stocker d’importants volumes de base de données, il se peut que le nombre d’E/S par seconde soit approprié. Cependant, un compte de stockage Azure Premium Storage est beaucoup plus restrictif en matière de volume de données qu’un compte de stockage Azure Standard. Vous ne pouvez ainsi déployer qu’un nombre limité de disques durs virtuels au sein d’un compte de stockage Azure Premium Storage avant d’atteindre la limite de volume de données. En définitive, un compte de stockage Azure peut être considéré comme un « SAN virtuel » dont les E/S par seconde et/ou la capacité sont limités. Comme pour les déploiements locaux, il reste à définir la disposition des disques durs virtuels des différents systèmes SAP sur les différents « périphériques SAN imaginaires » ou comptes de stockage Azure.

Pour le stockage Azure Standard, nous vous recommandons, dans la mesure du possible, de ne pas présenter le stockage de différents comptes de stockage à une même machine virtuelle.

Avec la série DS ou GS des machines virtuelles Azure, il est possible de monter des disques durs virtuels issus de comptes de stockage Azure Standard et Premium. Un stockage hétérogène de ce type pourrait être utilisé par exemple pour écrire des sauvegardes dans un stockage Standard soutenu par des disques durs virtuels tout en ayant les fichiers de données et journaux du SGBD dans un stockage Premium. 

Selon les déploiements client et les tests, environ 30 à 40 disques durs virtuels contenant les fichiers de données et les fichiers journaux de base de données peuvent être configurés sur un seul compte de stockage Azure Standard avec des performances acceptables. Comme indiqué précédemment, la limitation d’un compte de stockage Azure Premium Storage a des chances d’être le volume de données qu’il peut contenir et non le nombre d’E/S par seconde.

Comme avec les périphériques SAN en local, le partage nécessite une surveillance afin de détecter les goulots d’étranglement sur un compte de stockage Azure. L’extension de surveillance Azure pour SAP et le portail Azure peuvent être utilisés pour détecter les comptes de stockage Azure actifs dont les performances d’E/S ne sont pas optimales.  Lorsqu’une telle détection a lieu, il est recommandé de déplacer les machines virtuelles actives vers un autre compte de stockage Azure. Pour savoir comment activer les fonctionnalités de surveillance d’hôte SAP, consultez le [Guide de déploiement][deployment-guide].

Un autre article rassemblant les bonnes pratiques relatives au stockage standard Azure et aux comptes de stockage standard Azure est disponible ici : <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Disques managés
Les disques managés sont un nouveau type de ressources d’Azure Resource Manager. Ils peuvent être utilisés à la place des disques durs virtuels qui sont stockés dans les comptes de stockage Azure. Les disques managés s’alignent automatiquement sur le groupe à haute disponibilité de la machine virtuelle à laquelle ils sont attachés. De fait, ils augmentent la disponibilité de votre machine virtuelle et des services exécutés sur celle-ci. Pour plus d’informations, consultez cet [article de présentation](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

SAP prend uniquement en charge les disques managés Premium. Pour plus d’informations, lisez la note SAP [1928533].

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Déplacement de machines virtuelles de SGBD déployées du stockage Azure Standard vers le stockage Azure Premium Storage
Nous rencontrons un nombre assez important de scénarios où des clients souhaitent déplacer une machine virtuelle déployée du stockage Azure Standard vers le stockage Azure Premium Storage. Si vos disques sont stockés dans des comptes de stockage Azure, cela n’est pas possible sans déplacer physiquement les données. Il existe plusieurs façons d’atteindre cet objectif :

* Vous pouvez copier tous les disques durs virtuels, le disque dur virtuel de base, ainsi que les disques durs virtuels de données dans un nouveau compte de stockage Premium Azure. Souvent, vous choisissez le nombre de disques durs virtuels dans le stockage Azure Standard non pas en fonction du volume de données offert, mais parce que vous aviez besoin de ce nombre total d’E/S par seconde. Maintenant que vous effectuez un déplacement vers le stockage Premium Azure, vous pourriez utiliser beaucoup moins de disques durs virtuels pour atteindre le même débit d’E/S. Étant donné qu’avec le stockage standard Azure, vous payiez pour les données utilisées et non pour la taille de disque nominale, le nombre de disques durs virtuels n’importait pas du point de vue des coûts. Cependant, avec le stockage Azure Premium Storage, vous payez pour la taille de disque nominale. Par conséquent, la plupart des clients essaient de limiter le nombre de disques durs virtuels Azure dans le stockage Premium au nombre nécessaire pour atteindre le débit d’E/S nécessaire. Les clients décident donc généralement de ne pas faire une simple copie 1:1.
* S’il n’est pas encore monté, vous montez un disque dur virtuel unique pouvant contenir une sauvegarde de base de données de votre base de données SAP. Après la sauvegarde, vous démontez tous les disques durs virtuels, y compris celui contenant la sauvegarde, et copiez le disque dur virtuel de base et le disque dur virtuel avec la sauvegarde dans un compte de stockage Azure Premium Storage. Ensuite, vous déployez la machine virtuelle s’appuyant sur le disque dur virtuel de base et montez le disque dur virtuel contenant la sauvegarde. Maintenant, vous créez des disques Premium Storage vides supplémentaires pour la machine virtuelle, dans lesquels la base de données sera restaurée. Cela suppose que le SGBD vous permet de modifier les chemins d’accès aux fichiers de données et aux fichiers journaux dans le cadre du processus de restauration.
* Une autre possibilité est une variante du processus précédent, où vous copiez le disque dur virtuel de sauvegarde dans le stockage Premium Azure et l’attachez à une machine virtuelle que vous venez de déployer et d’installer.
* Une quatrième possibilité s’offre à vous si vous avez besoin de modifier le nombre de fichiers de données de votre base de données. Dans ce cas, vous exécutez une copie de système homogène SAP à l’aide de la fonctionnalité d’exportation/importation. Placez ces fichiers d’exportation dans un disque dur virtuel qui est copié dans un compte de stockage Azure Premium Storage et attachez-le à une machine virtuelle que vous utilisez pour exécuter les processus d’importation. Les clients utilisent cette possibilité principalement lorsqu’ils veulent réduire le nombre de fichiers de données.

Si vous utilisez des disques managés, vous pouvez migrer vers le Stockage Premium de la façon suivante :

1. Libérez la machine virtuelle
2. Si nécessaire, redimensionnez la machine virtuelle pour atteindre une taille prenant en charge le stockage Premium (par exemple DS ou GS)
3. Passez à un compte Premium pour le disque managé (SSD)
4. Démarrez la machine virtuelle

### <a name="deployment-of-vms-for-sap-in-azure"></a>Déploiement de machines virtuelles pour SAP dans Azure
Microsoft Azure offre plusieurs modes de déploiement de machines virtuelles et des disques associés. Il est important de comprendre ce qui les distingue dans la mesure où les préparations des machines virtuelles peuvent différer en fonction du déploiement. En règle générale, nous examinons les scénarios décrits dans les chapitres suivants.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Déploiement d’une machine virtuelle provenant d’Azure Marketplace
Vous voulez prendre une image fournie par Microsoft ou par un tiers provenant de la Place de marché Microsoft Azure pour déployer votre machine virtuelle. Une fois que vous avez déployé votre machine virtuelle dans Azure, vous utilisez les mêmes instructions et outils pour installer les logiciels SAP au sein de votre machine virtuelle comme vous le feriez dans un environnement local. Pour installer les logiciels SAP au sein de la machine virtuelle Azure, SAP et Microsoft recommandent de charger et stocker le support d’installation SAP sur des disques, ou de créer une machine virtuelle Azure faisant office de serveur de fichiers qui contient tous les supports d’installation SAP nécessaires.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Déploiement d’une machine virtuelle avec une image généralisée propre au client
En raison des correctifs qui doivent être appliqués à votre version de système d’exploitation ou de logiciel SGBD, les images fournies sur la Place de marché Microsoft Azure peuvent ne pas répondre à vos besoins. Par conséquent, vous devrez peut-être créer une machine virtuelle à l’aide de votre propre image « privée » de machine virtuelle pour système d’exploitation/SGBD, qui pourra être déployée plusieurs fois par la suite. Pour préparer une image « privée » de ce type pour la duplication, le système d’exploitation doit être généralisé sur la machine virtuelle locale. Pour savoir comment généraliser une machine virtuelle, consultez le [Guide de déploiement][deployment-guide].

Si vous avez déjà installé du contenu SAP sur votre machine virtuelle locale (en particulier pour les systèmes à 2 niveaux), vous pouvez adapter les paramètres du système SAP après le déploiement de la machine virtuelle Azure à l’aide de la procédure de modification du nom d’instance prise en charge par le gestionnaire de déploiement de logiciels SAP (Note de SAP [1619720]). Sinon, vous pouvez installer les logiciels SAP après le déploiement de la machine virtuelle Azure.

En ce qui concerne le contenu de base de données utilisé par l’application SAP, vous pouvez soit regénérer le contenu à l’aide d’une installation SAP, soit importer votre contenu dans Azure en utilisant un disque dur virtuel avec une sauvegarde de base de données de SGBD ou en tirant parti des capacités de sauvegarde directe dans Stockage Microsoft Azure du SGBD. Dans ce cas, vous pouvez également préparer des disques durs virtuels avec les fichiers de données et les fichiers journaux du SGBD en local, puis les importer en tant que disques dans Azure. Toutefois, le transfert de données SGBD chargées dans Azure à partir d’un stockage local fonctionne uniquement sur des disques durs virtuels qui ont été préparés en local.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Déplacement d’une machine virtuelle locale vers Azure avec un disque non généralisé
Vous envisagez de déplacer un système SAP spécifique local vers Azure (développement et transfert). Pour ce faire, vous pouvez charger le disque qui contient le système d’exploitation, les fichiers binaires SAP et les éventuels fichiers binaires du SGBD, ainsi que les disques contenant les fichiers de données et les fichiers journaux du SGBD dans Azure. Contrairement au scénario 2 ci-dessus, vous conservez le nom d’hôte, le SID SAP et les comptes d’utilisateur SAP dans la machine virtuelle Azure, tels qu’ils ont été configurés dans l’environnement local. Par conséquent, il n’est pas nécessaire de généraliser l’image. Ce cas s’applique tout particulièrement pour les scénarios de déploiement entre différents locaux dans lesquels une partie du paysage SAP est exécutée en local et une autre dans Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure
Azure offre les fonctionnalités de haute disponibilité et de récupération d’urgence suivantes, qui s’appliquent à différents composants utilisés pour des déploiements SAP et SGBD.

### <a name="vms-deployed-on-azure-nodes"></a>Machines virtuelles déployées sur des nœuds Azure
La plateforme Azure n’offre pas de fonctionnalités telles que la migration en direct pour les machines virtuelles déployées. Cela signifie que si une maintenance est nécessaire sur un cluster de serveurs sur lequel une machine virtuelle est déployée, la machine virtuelle doit être arrêtée et redémarrée. La maintenance dans Azure est effectuée à l’aide de domaines de mise à niveau au sein des clusters de serveurs. Seul un domaine de mise à niveau à la fois fait l’objet de la maintenance. Pendant le redémarrage, le service est interrompu lorsque la machine virtuelle est arrêtée, la maintenance est effectuée et la machine virtuelle redémarrée. Cependant, la plupart des fournisseurs de SGBD fournissent des fonctionnalités de haute disponibilité et de récupération d’urgence qui garantissent un redémarrage rapide des services du SGBD sur un autre nœud en cas d’indisponibilité du nœud principal. La plateforme Azure offre des fonctionnalités permettant de répartir les machines virtuelles, le stockage et les autres services Azure entre les domaines de mise à niveau pour s’assurer que la maintenance planifiée et les défaillances d’infrastructure n’affectent qu’un petit sous-ensemble de machines virtuelles ou de services.  Avec une planification méticuleuse, il est possible d’atteindre des niveaux de disponibilité comparables à ceux d’infrastructures locales.

Les groupes à haute disponibilité Microsoft Azure sont un regroupement logique de machines virtuelles ou de services qui assurent la répartition des machines virtuelles et des autres services dans des domaines d’erreur et de mise à niveau distincts, de telle sorte qu’il ne peut jamais y avoir plus d’un arrêt de nœud (pour plus d’informations, consultez [cet article (pour Linux)][virtual-machines-manage-availability-linux] et [cet article (pour Windows)][virtual-machines-manage-availability-windows]).

Ils doivent être configurés lors du déploiement des machines virtuelles, comme illustré ici :

![Définition du groupe à haute disponibilité pour les configurations haute disponibilité SGBD][dbms-guide-figure-200]

Si nous voulons créer des configurations haute disponibilité de déploiements SGBD (indépendamment de la fonctionnalité de haute disponibilité de SGBD utilisée), les machines virtuelles du SGBD devront :

* Ajoutez les nouvelles machines virtuelles au même réseau virtuel Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>).
* Les machines virtuelles de la configuration haute disponibilité doivent aussi se trouver sur le même sous-réseau. La résolution de noms entre les différents sous-réseaux n’est pas possible dans les déploiements « cloud uniquement ». Seule la résolution IP fonctionne. Si vous utilisez une connectivité de site à site ou ExpressRoute pour des déploiements entre différents locaux, un réseau comportant au moins un sous-réseau est déjà établi. La résolution de noms est effectuée selon les stratégies AD et l’infrastructure réseau locales. 



#### <a name="ip-addresses"></a>Adresses IP
Il est recommandé de configurer les machines virtuelles de manière résiliente pour les configurations haute disponibilité. Dans Azure, s’appuyer sur des adresses IP pour atteindre les partenaires de haute disponibilité au sein de la configuration haute disponibilité n’est pas fiable, à moins d’utiliser des adresses IP fixes. Il existe deux concepts d’« arrêt » dans Azure :

* L’arrêt via le portail Azure ou l’applet de commande Azure PowerShell Stop-AzureRmVM : dans ce cas, la machine virtuelle est arrêtée et désallouée. Votre compte Azure n’est plus facturé pour cette machine virtuelle, de sorte que les seuls frais occasionnés concernent le stockage utilisé. Cependant, si l’adresse IP privée de l’interface réseau n’était pas fixe, l’adresse IP est libérée et il n’y a aucune garantie que l’ancienne adresse IP sera à nouveau affectée à l’interface réseau après un redémarrage de la machine virtuelle. L’exécution de l’arrêt via le portail Azure ou l’appel de Stop-AzureRmVM entraîne automatiquement la désallocation de la machine virtuelle. Si vous ne souhaitez pas la désallouer, utilisez Stop-AzureRmVM -StayProvisioned. 
* Si vous arrêtez la machine virtuelle depuis un système d’exploitation, elle est arrêtée et n’est PAS désallouée. Cependant, votre compte Azure sera toujours facturé pour la machine virtuelle, même si celle-ci est arrêtée. Dans une telle situation, l’adresse IP affectée à une machine virtuelle arrêtée est conservée. L’arrêt de la machine virtuelle depuis celle-ci ne force pas sa désallocation.

Même pour les scénarios de déploiement entre différents locaux, par défaut, un arrêt et une désallocation entraînent la désaffectation des adresses IP de la machine virtuelle, même si les stratégies locales définies dans les paramètres DHCP sont différentes. 

* La seule exception concerne le cas où une adresse IP fixe est affectée à une interface réseau de la manière décrite [ici][virtual-networks-reserved-private-ip].
* L’adresse IP reste alors fixe tant que l’interface réseau n’est pas supprimée.

> [!IMPORTANT]
> Pour que l’ensemble du déploiement reste simple et facile à gérer, la solution évidente consiste à configurer les machines virtuelles associées dans une configuration haute disponibilité ou de récupération d’urgence de SGBD au sein d’Azure de sorte qu’il y ait une résolution de noms qui fonctionne entre les différentes machines virtuelles impliquées.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Déploiement de la surveillance d’hôte
Pour une utilisation productive des applications SAP dans Azure Virtual Machines, SAP doit avoir la possibilité de collecter des données de surveillance d’hôte auprès des hôtes physiques exécutant les machines virtuelles Azure. Un niveau de correctif logiciel SAP HostAgent spécifique est nécessaire pour activer cette fonctionnalité dans SAPOSCOL et SAP HostAgent. Le niveau de correctif logiciel exact est indiqué dans la Note de SAP [1409604].

Pour plus d’informations concernant le déploiement de composants qui fournissent des données d’hôte à SAPOSCOL et SAPHostAgent, et la gestion du cycle de vie de ces composants, consultez le [Guide de déploiement][deployment-guide].

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Caractéristiques de Microsoft SQL Server
### <a name="sql-server-iaas"></a>IaaS SQL Server
À partir de Microsoft Azure, vous pouvez facilement migrer vos applications SQL Server existantes créées sur la plateforme Windows Server vers les machines virtuelles Azure. Dans une machine virtuelle, SQL Server vous permet de réduire le coût total de possession lié au déploiement, à la gestion et à la maintenance des applications d’entreprise en les migrant en toute simplicité vers Microsoft Azure. Avec SQL Server dans une machine virtuelle Azure, les administrateurs et développeurs peuvent utiliser les outils de développement et d’administration disponibles en local. 

> [!IMPORTANT]
> Notez que nous ne traiterons pas ici du service Microsoft Azure SQL Database, qui est une offre Platform as a Service (PaaS) de la plateforme Microsoft Azure. Ce document porte sur l’exécution du produit SQL Server pour les déploiements locaux dans Azure Virtual Machines, en tirant parti des capacités d’Infrastructure as a Service (IaaS) d’Azure. Les capacités et fonctionnalités de base de données de ces deux services sont différentes et ne doivent pas être confondues. Voir aussi : <https://azure.microsoft.com/services/sql-database/>
> 
> 

Avant de continuer, il est recommandé de lire [cette documentation][virtual-machines-sql-server-infrastructure-services].

Dans les sections suivantes, des parties de la documentation ci-dessus seront regroupées et mentionnées. Les particularités concernant SAP sont également indiquées et certains concepts décrits plus en détail. Cependant, nous vous recommandons vivement d’examiner la documentation ci-dessus avant de lire la documentation propre à SQL Server.

Avant de continuer, il y a certaines informations spécifiques sur SQL Server dans IaaS que vous devez connaître :

* **Contrat de niveau de service pour les machines virtuelles** : il existe un contrat de niveau de service pour les machines virtuelles exécutées dans Azure. Il est disponible ici : <https://azure.microsoft.com/support/legal/sla/>  
* **Prise en charge des versions SQL**: pour les clients SAP, nous prenons en charge SQL Server 2008 R2 et versions ultérieures sur Microsoft Azure Virtual Machine. Les éditions antérieures ne sont pas prises en charge. Pour plus d’informations, voir cette [déclaration officielle](https://support.microsoft.com/kb/956893) générale. Notez qu’en règle générale, SQL Server 2008 est également pris en charge par Microsoft. Cependant, en raison de fonctionnalités significatives pour SAP introduites avec SQL Server 2008 R2, SQL Server 2008 R2 est la version minimale requise pour SAP. Gardez à l’esprit que SQL Server 2012 et 2014 ont été étendus avec une meilleure intégration dans le scénario IaaS (possibilité de sauvegarde directe dans Azure Storage, par exemple). Par conséquent, nous limiterons ce document à SQL Server 2012 et 2014 avec le dernier niveau de correctif logiciel pour Azure.
* **Prise en charge des fonctionnalités SQL**: la plupart des fonctionnalités SQL Server sont prises en charge sur Microsoft Azure Virtual Machines, à quelques exceptions près. **Le clustering de basculement SQL Server à l’aide de disques partagés n’est pas pris en charge**.  Les technologies distribuées telles que la mise en miroir de bases de données, les groupes de disponibilité AlwaysOn, la réplication, la copie des journaux de transaction et l’envoi de journaux et Service Broker sont prises en charge au sein d’une même région Azure. L’utilisation de SQL Server AlwaysOn sur plusieurs régions Azure est également prise en charge, comme indiqué ici : <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Pour plus d’informations, voir la [déclaration officielle](https://support.microsoft.com/kb/956893) . Pour un exemple de déploiement de configuration AlwaysOn, consultez [cet][virtual-machines-workload-template-sql-alwayson] article. Consultez également les meilleures pratiques détaillées [ici][virtual-machines-sql-server-infrastructure-services] 
* **Performances de SQL** : nous sommes convaincus que les machines virtuelles hébergées par Microsoft Azure fonctionnent très bien, par rapport aux autres offres de virtualisation du cloud public. Cependant, les résultats peuvent varier d’un cas à l’autre. Pour en savoir plus, consultez [cet article][virtual-machines-sql-server-performance-best-practices].
* **Utilisation d’images de Microsoft Azure Marketplace**: la méthode la plus rapide pour déployer une nouvelle machine virtuelle Microsoft Azure consiste à utiliser une image de Microsoft Azure Marketplace. En effet, cette plateforme propose des images incluant SQL Server. Les images hébergeant déjà SQL Server ne peuvent pas être directement utilisées pour les applications SAP NetWeaver. En effet, le classement par défaut installé au sein de ces images correspond à celui de SQL Server, et non au classement requis par les systèmes SAP NetWeaver. Pour pouvoir utiliser ces images, suivez la procédure décrite dans le chapitre [Utilisation d’images SQL Server issues de la Place de marché Microsoft Azure][dbms-guide-5.6]. 
* Pour en savoir plus, voir la rubrique [Tarification](https://azure.microsoft.com/pricing/) . Les documents [SQL Server 2012 Licensing Guide](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) (Guide de licences relatives à SQL Server 2012) et [SQL Server 2014 Licensing Guide](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) (Guide de licences relatives à SQL Server 2014) peuvent également fournir des informations très utiles.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Recommandations relatives à la configuration de SQL Server pour les installations SAP sur des machines virtuelles Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recommandations portant sur la structure des machines virtuelles/disques VHD pour les déploiements de SQL Server associés à SAP
Conformément à la description générale, les exécutables SQL Server doivent être situés ou installés sur le lecteur système du disque de la machine virtuelle (le lecteur C:\).  Bien souvent, la plupart des bases de données système SQL Server ne sont pas pleinement exploitées par les charges de travail SAP NetWeaver. Par conséquent, les bases de données système SQL Server (« master », « msdb » et « model ») peuvent également rester sur le lecteur C:\. La base de données tempdb peut être une exception. Pour l’ensemble des charges de travail BW et certaines charges de travail SAP ERP, tempdb peut nécessiter un volume de données ou d’opérations d’E/S plus important, que la capacité de la machine virtuelle d’origine ne suffit pas à satisfaire. Pour ces systèmes, la procédure suivante doit être effectuée :

* Déplacez le ou les fichiers de données principaux de tempdb vers le lecteur logique qui inclut déjà le ou les fichiers de données principaux de la base de données SAP.
* Ajoutez les fichiers de données supplémentaires de la base de données tempdb sur chaque lecteur logique contenant un fichier de données de la base de données utilisateur SAP.
* Ajoutez le fichier journal tempdb au lecteur logique qui contient le fichier journal de la base de données utilisateur.
* Les données et fichiers journaux de tempdb peuvent être placés sur le lecteur D:\ **uniquement dans le cas des types de machines virtuelles qui utilisent des disques SSD locaux** sur le nœud de calcul. Toutefois, il peut être préférable d’utiliser plusieurs fichiers de données tempdb. N’oubliez pas que les volumes de lecteur D:\ sont différents selon le type de machine virtuelle.

Ces configurations permettent à la base de données tempdb de consommer davantage d’espace que celui que peut proposer le lecteur système. Pour référence, vous pouvez consulter les tailles d’unités tempdb sur les systèmes existants qui s’exécutent en local. De plus, une telle configuration permet d’atteindre un nombre d’E/S par seconde pour tempdb qui ne peut pas être fourni avec le lecteur système. Là encore, des systèmes en cours d’exécution en local peuvent être utilisés pour surveiller la charge de travail d’E/S sur tempdb, afin de vous permettre d’obtenir le nombre d’E/S par seconde attendu sur votre base de données tempdb.

Voici un exemple de configuration de machine virtuelle qui exécute SQL Server avec une base de données SAP, et dans laquelle les données et le fichier journal de tempdb sont placés sur le lecteur D:\ :

![Configuration de référence de la machine virtuelle IaaS Azure pour SAP][dbms-guide-figure-300]

N’oubliez pas que le lecteur D:\ présente des tailles différentes selon le type de machine virtuelle. En effet, en fonction des exigences en termes de taille de la base de données tempdb, vous pouvez être amené à coupler les données et fichiers journaux de cette dernière avec les fichiers journaux et données de la base de données SAP, si la capacité du lecteur D:\ est insuffisante.

#### <a name="formatting-the-disks"></a>Formatage des disques
Dans le cas de SQL Server, la taille du bloc NTFS des disques contenant des données et des fichiers journaux SQL Server doit être de 64 Ko. Il est inutile de mettre en forme le lecteur D:\. En effet, ce lecteur est déjà mis en forme.

Pour vous assurer que la restauration ou la création de bases de données n’initialise pas les fichiers de données en supprimant le contenu des fichiers, vous devez vous assurer que le contexte utilisateur dans lequel le service SQL Server s’exécute dispose de l’autorisation adéquate. En général, les utilisateurs du groupe Administrateurs Windows disposent des autorisations requises. Si le service SQL Server est exécuté dans le contexte utilisateur d’un administrateur autre que Windows, vous devez affecter à cet utilisateur le droit **Effectuer les tâches de maintenance de volume**.  Pour plus d’informations, consultez cet article de la Base de connaissances Microsoft : <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Impact de la compression de base de données
Dans les configurations pour lesquelles la bande passante d’E/S peut devenir un facteur de limitation, toutes les mesures qui réduisent le nombre d’E/S par seconde peuvent contribuer à étirer la charge de travail exécutable dans un scénario IaaS comme Azure. Par conséquent, si vous ne l’avez pas encore fait, SAP et Microsoft recommandent l’application de la compression de page SQL Server avant le chargement de bases de données SAP existantes dans Azure.

Nous recommandons d’effectuer une compression de base de données avant le chargement sur Azure pour deux raisons :

* La quantité de données à charger est moins importante.
* En supposant que l’on peut utiliser en local un matériel plus performant avec plus d’UC, une bande passante d’E/S supérieure ou une latence d’E/S inférieure, la durée d’exécution de la compression est plus courte.
* Des bases de données plus petites peuvent permettre de diminuer les coûts liés à l’allocation de disque.

La compression de bases de données fonctionne aussi bien dans une machine virtuelle Azure qu’en local. Pour plus d’informations sur la compression d’une base de données SQL Server SAP existante, consultez <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 : Stockage des fichiers de base de données directement dans le Stockage Blob Azure
SQL Server 2014 permet de stocker les fichiers de base de données directement dans le magasin d’objets blob Azure, sans qu’il soit nécessaire d’utiliser un disque VHD pour « envelopper » ces fichiers. Lorsque vous utilisez le stockage Standard Azure ou des types de machines virtuelles plus petits, vous pouvez dépasser les limites d’E/S par seconde imposées par le nombre limité de disques pouvant être montés sur certains types de machines virtuelles de taille plus réduite. Toutefois, ce genre de scénario fonctionne dans le cas des bases de données utilisateur, mais non pour les bases de données système SQL Server. Il peut également être appliqué aux fichiers journaux et données de SQL Server. Si vous souhaitez déployer une base de données SQL Server SAP de cette manière plutôt que de recourir à des disques VHD, tenez compte des remarques suivantes :

* Le compte de stockage utilisé doit se trouver dans la même région Azure que celui qui permet de déployer la machine virtuelle sur laquelle SQL Server s’exécute.
* Les considérations relatives à la répartition de disques VHD sur différents comptes de stockage Azure qui ont été abordées précédemment portent également sur cette méthode de déploiement. Cela signifie que les opérations d’E/S sont concernées par les limites du compte Azure Storage.

[comment]: <> (MSSedusch TODO Cette opération utilise la bande passante du réseau et non celle du stockage, n’est-ce pas ?)

Pour plus d’informations sur ce type de déploiement, consultez <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Pour stocker des fichiers de données SQL Server directement dans le stockage Premium Azure, vous devez vous procurer la version minimale du correctif SQL Server 2014 documentée ici : <https://support.microsoft.com/kb/3063054> La fonctionnalité permettant de stocker des fichiers de données SQL Server sur le stockage Azure standard fonctionne avec la version finale de SQL Server 2014. Toutefois, ces mêmes correctifs contiennent une autre série de correctifs, qui renforcent la fiabilité du Stockage Blob Azure lorsque celui-ci est directement utilisé pour les sauvegardes et les fichiers de données SQL Server. Pour cette raison, nous recommandons généralement l’utilisation de ces correctifs.

### <a name="sql-server-2014-buffer-pool-extension"></a>Fonctionnalité d’extension du pool de mémoires tampons de SQL Server 2014
SQL Server 2014 propose une nouvelle fonctionnalité appelée « extension du pool de mémoires tampons ». Cette fonctionnalité permet d’étendre le pool de mémoires tampons de SQL Server conservé en mémoire grâce à un cache de deuxième niveau, sauvegardé par les disques SSD locaux d’un serveur ou d’une machine virtuelle. Cela permet de conserver une plage de travail plus étendue pour les données « en mémoire ». Par rapport à l’accès au stockage Azure standard, l’accès à l’extension du pool de mémoires tampons stocké sur les disques SSD locaux d’une machine virtuelle est beaucoup plus rapide.  Par conséquent, il peut s’avérer très pertinent de tirer parti du lecteur D:\ local des types de machine virtuelle présentant un excellent nombre d’E/S par seconde et un très bon débit, afin de réduire la charge d’E/S par seconde affectant Azure Storage et d’améliorer de façon très nette le temps de réponse des requêtes. Cela se révèle d’autant plus vrai lorsque vous n’utilisez pas Premium Storage. En effet, si vous utilisez le Stockage Premium et le cache de lecture Azure Premium sur le nœud de calcul, comme recommandé pour les fichiers de données, il ne doit y avoir aucune différence majeure. En effet, les deux caches (le cache de lecture Premium Storage et la fonction d’extension du pool de mémoires tampons SQL Server) utilisent les disques locaux des nœuds de traitement.
Pour plus d’informations sur cette fonctionnalité, consultez la documentation suivante : <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considérations relatives à la sauvegarde/restauration pour SQL Server
Lors du déploiement de SQL Server dans Azure, votre méthodologie de sauvegarde doit être passée en revue. Même si le système n’est pas un système productif, la base de données SAP hébergée par SQL Server doit être sauvegardée régulièrement. Comme Azure Storage conserve trois images, la sauvegarde joue désormais un rôle moins important en matière de compensation des pannes du stockage. La raison principale du maintien d’un plan de sauvegarde et de récupération approprié réside davantage dans le fait que vous pouvez compenser les erreurs logiques/manuelles en fournissant des fonctionnalités de récupération jusqu’à une date et heure. Par conséquent, l’objectif est soit d’utiliser les sauvegardes pour restaurer la base de données à un moment donné, soit d’utiliser les sauvegardes dans Azure pour amorcer un autre système en copiant la base de données existante. Par exemple, vous avez la possibilité de transférer des données depuis une configuration SAP de niveau 2 vers une configuration système de niveau 3 du même système en restaurant une sauvegarde.

Vous pouvez sauvegarder des données SQL Server dans le Stockage Azure de trois manières :

1. SQL Server 2012 CU4 et les versions ultérieures peuvent sauvegarder les bases de données sur une URL, en mode natif. Cette opération est détaillée dans le billet de blog [New functionality in SQL Server 2014 – Part 5 – Backup/Restore Enhancements](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)(Nouvelles fonctionnalités de SQL Server 2014 - Partie 5 - Améliorations apportées à la sauvegarde et à la restauration). Voir le chapitre [SQL Server 2012 SP1 CU4 et versions ultérieures][dbms-guide-5.5.1].
2. Les versions de SQL Server antérieures à SQL 2012 CU4 peuvent exploiter une fonctionnalité de redirection pour sauvegarder leurs données sur un disque VHD et déplacer le flux d’écriture vers un emplacement Azure Storage qui a été configuré. Voir le chapitre [SQL Server 2012 SP1 CU3 et versions antérieures][dbms-guide-5.5.2].
3. La dernière méthode consiste à exécuter une commande de sauvegarde des données SQL Server sur une unité de disque. Cette procédure, identique à celle du modèle de déploiement local, n’est pas détaillée dans ce document.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 et versions ultérieures
Cette fonctionnalité vous permet de sauvegarder directement les données sur le stockage d’objets blob Azure. Sans cette méthode, vous devez sauvegarder les données sur d’autres disques, ce qui monopolise la capacité des disques, ainsi que les E/S par seconde. L’idée de base est la suivante :

 ![Utilisation de la sauvegarde SQL Server 2012 sur l’objet blob Stockage Microsoft Azure][dbms-guide-figure-400]

Dans ce cas, il n’est pas nécessaire d’utiliser des disques pour stocker les données de sauvegarde SQL Server, ce qui est un avantage. Ainsi, un nombre moins important de disques est alloué et la totalité de la bande passante associée aux E/S par seconde des disques peut être utilisée pour les fichiers journaux et les données. Notez que la taille maximale d’une sauvegarde est limitée à un 1 To, comme décrit dans la section **Limitations** de cet article : <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Si, malgré l’utilisation de la compression de sauvegarde SQL Server, la taille de la sauvegarde dépasse 1 To, la fonctionnalité décrite dans le chapitre [SQL Server 2012 SP1 CU3 et versions antérieures][dbms-guide-5.5.2] du présent document doit être utilisée.

La [documentation associée](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) décrivant la restauration des bases de données à partir de sauvegardes sur le magasin d’objets blob Azure vous recommande de ne pas restaurer directement les données depuis ce magasin si la taille des sauvegardes est supérieure à 25 Go. La recommandation indiquée dans cet article repose sur des considérations relatives aux performances, et non sur des restrictions fonctionnelles. Par conséquent, différentes conditions peuvent s’appliquer au cas par cas.

Pour des informations sur la configuration et l’exploitation de ce type de sauvegarde, voir [ce](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) didacticiel

Un exemple de séquence d’étapes est détaillé [ici](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Lors de l’automatisation des sauvegardes, il est primordial de s’assurer que les objets blob de chaque sauvegarde portent des noms différents. Dans le cas contraire, ils sont remplacés et la chaîne de restauration est interrompue.

Afin de ne pas mélanger les éléments des trois types de sauvegarde, nous vous invitons à créer des conteneurs distincts sous le compte de stockage utilisé pour les sauvegardes. Les conteneurs peuvent être créés par machine virtuelle uniquement, ou par type de sauvegarde et de machine virtuelle. Le schéma peut ressembler à ce qui suit :

 ![Utilisation de la sauvegarde SQL Server 2012 sur l’objet blob Stockage Microsoft Azure : différents conteneurs sous des comptes de stockage distincts][dbms-guide-figure-500]

Dans l’exemple ci-dessus, les sauvegardes ne peuvent pas être effectuées sur le compte de stockage dans lequel les machines virtuelles sont déployées. Un nouveau compte de stockage est prévu spécialement pour les sauvegardes. Au sein des comptes de stockage, des conteneurs différents sont créés à l’aide de la combinaison du nom de la machine virtuelle et du type de la sauvegarde. Cette segmentation facilite l’administration des sauvegardes des différentes machines virtuelles.

Les objets blob sur lesquels les sauvegardes sont directement écrites ne sont pas inclus dans le nombre de disques de données d’une machine virtuelle. Par conséquent, il est possible d’augmenter le nombre maximal de disques de données montés qui sont associés à la SKU de machine virtuelle pour le fichier journal de transactions et les données, tout en étant à même d’exécuter la sauvegarde d’un conteneur de stockage. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 et versions antérieures
La première étape à effectuer pour exécuter une sauvegarde directement dans le Stockage Azure consiste à télécharger le fichier msi lié à [cet article](https://www.microsoft.com/download/details.aspx?id=40740) de la Base de connaissances.

Téléchargez le fichier d’installation x64 et la documentation associée. Ce fichier installe un programme appelé **Microsoft SQL Server Backup to Microsoft Azure Tool**. Lisez attentivement la documentation du produit.  De manière générale, cet outil fonctionne de la façon suivante :

* Du côté SQL Server, un emplacement de disque est défini pour la sauvegarde de SQL Server (n’utilisez pas le lecteur D:\ pour cet emplacement).
* L’outil vous permet de définir des règles qui peuvent être utilisées pour diriger différents types de sauvegardes vers différents conteneurs du Stockage Azure.
* Une fois les règles en place, l’outil redirige le flux d’écriture de la sauvegarde vers l’un des disques VHD/disques, à l’emplacement Stockage Azure qui a été défini précédemment.
* L’outil laisse un fichier stub de quelques kilo-octets sur le disque ou le disque dur virtuel qui a été défini pour la sauvegarde SQL Server. **Ce fichier doit être conservé à l’emplacement de stockage, car il est requis pour effectuer à nouveau une restauration depuis Azure Storage.**
  * Si vous avez perdu le fichier stub (par exemple, à cause de la défaillance du support de stockage qui l’héberge) et que vous avez choisi l’option de sauvegarde vers un compte Stockage Microsoft Azure, vous pouvez récupérer ce fichier stub via le Stockage Microsoft Azure en le téléchargeant à partir du conteneur de stockage dans lequel il a été placé. Placez ce fichier stub dans un dossier figurant sur l’ordinateur local où l’outil est configuré pour détecter et charger les données vers le même conteneur, avec le même mot de passe de chiffrement (si le chiffrement est utilisé avec la règle d’origine). 

Cela signifie que le schéma décrit ci-dessus pour les versions plus récentes de SQL Server peut également être mis en place pour les versions de SQL Server qui n’autorisent pas d’adresse directe pour un emplacement Stockage Azure.

Cette méthode ne doit pas être utilisée avec les versions plus récentes de SQL Server qui prennent en charge la sauvegarde en mode natif dans le Stockage Azure. Les exceptions concernent les cas où des limitations affectant la sauvegarde en mode natif dans Azure empêchent l’exécution de sauvegardes en mode natif dans Azure.

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>Autres méthodes de sauvegarde de bases de données SQL Server
Il existe d’autres méthodes de sauvegarde des bases de données, telles que l’association de disques de données supplémentaires à la machine virtuelle que vous utilisez pour stocker les sauvegardes. Dans ce cas, vous devez vérifier que les disques ne sont pas saturés lorsqu’ils sont exécutés. Si tel est le cas, vous devez démonter le disque concerné, « l’archiver », puis le remplacer par un disque vide. Si vous optez pour cette méthode, vous devez vous assurer que ces disques VHD se trouvent sur des comptes Azure Storage différents de ceux des disques VHD présentant les fichiers de base de données.

Une deuxième méthode consiste à utiliser une machine virtuelle volumineuse, qui peut être associée à plusieurs disques (par exemple, un système D14 avec 32 disques VHD). Utilisez les espaces de stockage pour créer un environnement flexible, dans lequel vous pouvez créer des partages qui seront ensuite utilisés en tant que cibles de sauvegarde pour les différents serveurs SGBD (système de gestion de base de données).

Certaines meilleures pratiques sont également décrites [ici](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) . 

#### <a name="performance-considerations-for-backupsrestores"></a>Considérations sur les performances des sauvegardes/restaurations
À l’instar des déploiements complets, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, vous pouvez partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les fichiers de données, plus le débit global de lecture est réduit.
* Moins il y a de threads d’UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est important.
* Moins il y a de cibles d’écriture de la sauvegarde (objets blob, disques, disques VHD), plus le débit est réduit.
* Plus la taille des machines virtuelles est réduite, plus le quota du débit de stockage sera faible lors de l’écriture et de la lecture depuis Azure Storage, que les sauvegardes soient directement stockées sur un objet blob Azure ou sur des disques VHD, eux-mêmes stockés sur des objets blob.

Lorsque vous utilisez un objet blob Microsoft Azure Storage en tant que cible de sauvegarde dans les versions les plus récentes, vous ne pouvez indiquer qu’une seule URL cible pour chaque sauvegarde spécifique.

Cependant, quand vous utilisez l’outil « Microsoft SQL Server Backup to Microsoft Azure Tool » dans des versions antérieures, vous pouvez définir plusieurs cibles de fichier. Puisqu’il existe plusieurs cibles, la sauvegarde peut évoluer ; le débit associé augmente. Cela donne également lieu à la création de plusieurs fichiers sur le compte Azure Storage. Lors des tests, vous avez constaté que l’utilisation de plusieurs destinations de fichiers permettait réellement d’atteindre le débit obtenu avec les extensions de sauvegarde implémentées dans SQL Server 2012 SP1 CU4 et versions ultérieures. Par ailleurs, vous n’êtes pas contraint de respecter la limite d’1 To imposée par la fonction de sauvegarde en mode natif dans Azure.

Toutefois, n’oubliez pas que le débit dépend également de l’emplacement du compte Azure Storage que vous utilisez pour la sauvegarde. Il peut être pertinent de placer le compte de stockage dans une région différente de la région au sein de laquelle les machines virtuelles s’exécutent. Vous pouvez exécuter la configuration de la machine virtuelle dans la région Europe de l’Ouest tout en plaçant le compte de stockage que vous utilisez pour la sauvegarde dans la région Europe du Nord. Cela affecte certainement le débit des sauvegardes. Il est peu probable que le débit généré atteigne 150 Mbits/s, comme dans certains cas où le stockage cible et les machines virtuelles s’exécutent au sein du même centre de données régional.

#### <a name="managing-backup-blobs"></a>Gestion des objets blob de sauvegarde
Si vous voulez gérer les sauvegardes vous-même, il y a une exigence à respecter. Étant donné que l’exécution de sauvegardes fréquentes de fichiers journaux de transactions est censée créer de nombreux objets blob, la gestion de ces objets peut rapidement surcharger le portail Azure. Pour cette raison, il est recommandé d’utiliser un explorateur de stockage Azure. Il existe plusieurs explorateurs susceptibles de vous aider à gérer efficacement un compte de stockage Azure.

* Microsoft Visual Studio sur lequel est installé le SDK Azure (<https://azure.microsoft.com/downloads/>)
* Explorateur Stockage Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* Outils tiers

Pour obtenir une description plus complète de la sauvegarde et de SAP sur Azure, consultez le [Guide de la sauvegarde SAP](sap-hana-backup-guide.md).

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Utilisation d’une image SQL Server issue de la Place de marché Microsoft Azure
Dans la Place de marché Azure, Microsoft propose des machines virtuelles qui contiennent déjà des versions de SQL Server. Pour les clients SAP qui requièrent des licences pour SQL Server et Windows, cela peut être l’occasion de répondre aux besoins de base en termes de licences, en configurant des machines virtuelles déjà dotées de SQL Server. Pour pouvoir utiliser ces images pour SAP, vous devez tenir compte des considérations suivantes :

* Les versions de SQL Server autres que les versions d’évaluation nécessitent des frais d’acquisition plus élevés que les machines virtuelles « Windows uniquement » qui sont déployées à partir de la Place de marché Azure. Pour comparer les prix, consultez les articles <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> et <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Vous pouvez uniquement utiliser les versions de SQL Server qui sont prises en charge par SAP, telles que SQL Server 2012.
* Le classement de l’instance SQL Server qui est installée dans les machines virtuelles proposées par la Place de marché ne correspond pas à celui que nécessite SAP NetWeaver pour l’instance SQL Server. Toutefois, vous pouvez modifier ce classement, en suivant les instructions de la section suivante.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Modification du classement SQL Server d’une machine virtuelle Microsoft Windows/SQL Server
Étant donné que les images SQL Server figurant dans la Place de marché Microsoft Azure ne sont pas configurées pour utiliser le classement exigé par les applications SAP NetWeaver, elles doivent être modifiées immédiatement après le déploiement. Pour SQL Server 2012, vous pouvez exécuter cette opération en suivant la procédure ci-après dès que la machine virtuelle est déployée et qu’un administrateur peut se connecter à cette dernière :

* Ouvrez une fenêtre de commande Windows en tant qu’administrateur.
* Remplacez le répertoire par celui-ci : C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Exécutez la commande suivante : Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`&gt; correspond au compte qui a été défini en tant que compte Administrateur lors du déploiement de la machine virtuelle pour la première fois, via la galerie.

Le processus doit prendre quelques minutes seulement. Pour vérifier que le résultat attendu a été obtenu, effectuez les étapes suivantes :

* Ouvrez SQL Server Management Studio.
* Ouvrez une fenêtre Requête.
* Exécutez la commande sp_helpsort dans la base de données MASTER de SQL Server.

Le résultat doit être similaire à ce qui suit :

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Si vous n’obtenez pas ce résultat, interrompez immédiatement le déploiement de SAP et cherchez à savoir pourquoi la commande n’a pas fonctionné comme prévu. Le système ne prend **PAS** en charge le déploiement d’applications SAP NetWeaver sur une instance SQL Server avec des pages de codes SQL autres que celle indiquée ci-dessus.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Solutions SQL Server haute disponibilité pour SAP dans Azure
Comme indiqué plus haut, il n’est pas possible de créer le stockage partagé qui est nécessaire pour l’utilisation des anciennes fonctionnalités de haute disponibilité SQL Server. Cette dernière permet d’installer deux instances SQL Server minimum au sein d’un cluster WSFC (Windows Server Failover Cluster) à l’aide d’un disque partagé pour les bases de données utilisateur (et de tempdb, en fin de compte). Il s’agit depuis longtemps de la méthode standard pour assurer la haute disponibilité. Elle est également prise en charge par SAP. Comme Azure ne prend pas en charge le stockage partagé, les configurations de haute disponibilité de SQL Server reposant sur un cluster de disque partagé sont impossibles. Toutefois, de nombreuses autres méthodes sont toujours possibles. Elles sont décrites dans les sections suivantes.

#### <a name="sql-server-log-shipping"></a>Copie des journaux de transaction SQL Server
L’une des méthodes permettant d’assurer la haute disponibilité est la copie des journaux de transaction SQL Server. Si les machines virtuelles prenant part à la configuration haute disponibilité disposent de la fonctionnalité de résolution de noms, il n’y a aucune difficulté. L’installation dans Azure ne diffère pas d’une installation effectuée en local. Il est déconseillé de se fier uniquement à la résolution IP. Pour en savoir plus sur la configuration de la copie des journaux de transaction et connaître les principes qui sous-tendent cette technologie, consultez la documentation suivante :

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

Pour assurer leur haute disponibilité, vous devez déployer les machines virtuelles se trouvant au sein d’une configuration de copie des journaux de transaction de ce type, afin qu’elles soient placées dans le même groupe à haute disponibilité Azure.

#### <a name="database-mirroring"></a>Mise en miroir de bases de données
La mise en miroir de bases de données prise en charge par SAP (voir Note de SAP [965908]) s’appuie sur la définition d’un partenaire de basculement dans la chaîne de connexion SAP. Dans le cas de déploiements entre différents locaux, nous partons du principe que les deux machines virtuelles se trouvent au sein du même domaine, et que, au sein du contexte utilisateur dans lequel les deux instances SQL Server s’exécutent, ces dernières sont également des utilisateurs du domaine et disposent de privilèges adéquats dans les deux instances SQL Server concernées. Par conséquent, la procédure d’installation de la fonctionnalité de mise en miroir de bases de données dans Azure ne diffère pas de celle d’une configuration ou installation locale classique.

Pour les déploiements sur cloud uniquement, la méthode la plus simple consiste à configurer un autre domaine dans Azure, afin que les machines virtuelles SGBD (système de gestion de base de données), ainsi que les machines virtuelles SAP dédiées, dans l’idéal, se trouvent au sein d’un même domaine.

Si cela n’est pas possible, vous pouvez recourir aux certificats associés aux points de terminaison de la mise en miroir de bases de données, comme indiqué ici : <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Un tutoriel permettant de configurer la mise en miroir de bases de données dans Azure est disponible ici : <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server AlwaysOn
Étant donné que la fonction AlwaysOn est prise en charge pour les systèmes SAP locaux (voir la note SAP [1772688]), il est possible de l’utiliser avec SAP sur Azure. L’incapacité à créer des disques partagés dans Azure ne signifie pas qu’il est impossible de créer une configuration de cluster WSFC (Windows Server Failover Cluster) AlwaysOn entre différentes machines virtuelles. Cela signifie simplement que vous n’avez pas la possibilité d’utiliser un disque partagé en tant que quorum dans la configuration de cluster. Par conséquent, vous pouvez créer une configuration WSFC AlwaysOn dans Azure et ne pas sélectionner le type de quorum qui utilise un disque partagé. L’environnement Azure dans lequel ces machines virtuelles sont déployées doit résoudre les machines virtuelles par nom ; quant aux machines virtuelles, elles doivent être dans le même domaine. Cela se révèle vrai pour Azure uniquement, dans le cas de déploiement entre différents locaux. Certains éléments doivent être pris en compte pour le déploiement de l’écouteur de groupe de disponibilité SQL Server (à ne pas confondre avec le groupe à haute disponibilité Azure), car Azure ne permet pas de créer un objet AD/DNS (il le permet toutefois localement). Par conséquent, certaines étapes d’installation différentes sont nécessaires pour surmonter le comportement spécifique d’Azure.

Lors de l’utilisation de l’écouteur de groupe de disponibilité, tenez compte des considérations suivantes :

* L’utilisation de l’écouteur de groupe de disponibilité n’est possible que sur un système Windows Server 2012 ou version ultérieure, utilisé en tant que SE invité de la machine virtuelle. Pour Windows Server 2012, veillez à appliquer ce correctif : <https://support.microsoft.com/kb/2854082> 
* Ce correctif n’existe pas pour Windows Server 2008 R2. La fonction AlwaysOn doit être utilisée de la même manière que la fonctionnalité de mise en miroir de bases de données, via la spécification d’un partenaire de basculement dans la chaîne de connexion (grâce au paramètre SAP default.pfl dbs/mss/server : voir la note SAP [965908]).
* Lorsque vous utilisez un écouteur de groupe de disponibilité, les machines virtuelles de base de données doivent être connectées à un équilibreur de charge dédié. La résolution de noms dans un déploiement « cloud uniquement » nécessite la présence de l’ensemble des machines virtuelles d’un système SAP (serveurs d’applications, serveur SGBD et serveur (A)SCS) au sein du même réseau virtuel, ou exige la maintenance du fichier etc\host depuis la couche Application SAP, afin de faire en sorte que les noms des machines virtuelles SQL Server soient résolus. Pour éviter qu’Azure n’affecte de nouvelles adresses IP lorsque les deux machines virtuelles sont arrêtées en même temps, l’utilisateur doit affecter des adresses IP statiques aux interfaces réseau de ces machines au sein de la configuration AlwaysOn (la procédure de définition d’une adresse IP statique est décrite dans [cet article][virtual-networks-reserved-private-ip])

[comment]: <> (Anciens blogs)
[comment]: <> (&lt;https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx&gt;, &lt;https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx&gt;) 
* La création d’une configuration de cluster WSFC requiert certaines étapes spécifiques lorsque ce cluster doit se voir affecter une adresse IP spécifique, car la fonctionnalité actuelle d’Azure affecte au nom du cluster la même adresse IP que celle du nœud sur lequel le cluster est créé. Cela signifie que l’attribution d’une adresse IP différente au cluster doit faire l’objet d’une étape manuelle.
* L’écouteur de groupe de disponibilité va être créé dans Azure avec les points de terminaison TCP/IP qui sont affectés aux machines virtuelles exécutant les réplicas principaux et secondaires du groupe de disponibilité.
* Il peut être nécessaire de sécuriser ces points de terminaison avec des ACL.

[comment]: <> (Ancien blog TODO)
[comment]: <> (The detailed steps and necessities of installing an AlwaysOn configuration on Azure are best experienced when walking through the tutorial available [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Preconfigured AlwaysOn setup via the Azure gallery &lt;https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx&gt;)
[comment]: <> (Creating an Availability Group Listener is best described in [this][virtual-machines-windows-classic-ps-sql-int-listener] tutorial)
[comment]: <> (Securing network endpoints with ACLs are explained best here:)
[comment]: <> (*    &lt;https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/&gt;)
[comment]: <> (*    &lt;https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx&gt; )
[comment]: <> (*    &lt;https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx&gt;)  
[comment]: <> (*    &lt;https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx&gt;) 

Il est également possible de déployer un groupe de disponibilité AlwaysOn SQL Server sur différentes régions Azure. Cette fonctionnalité utilise la connectivité de réseau virtuel à réseau virtuel Azure ([plus d’informations ici][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Ancien blog TODO)
[comment]: <> (The setup of SQL Server AlwaysOn Availability Groups in such a scenario is described here: &lt;https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx&gt;.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Haute disponibilité SQL Server dans Azure - Résumé
Azure Storage protégeant le contenu, vous avez une raison de moins d’insister sur la création d’une image de secours. Cela signifie que votre scénario de haute disponibilité doit uniquement protéger vos systèmes contre les problèmes suivants :

* indisponibilité de la machine virtuelle dans son ensemble en raison d’une opération de maintenance sur le cluster de serveurs dans Azure, ou pour d’autres raisons ;
* problèmes logiciels au sein de l’instance SQL Server ;
* protection contre les erreurs manuelles, lorsque les données sont supprimées et qu’une récupération jusqu’à une date et heure est requise.

Si l’on étudie les technologies de mise en correspondance, on peut prétendre que les fonctionnalités de mise en miroir de bases de données et AlwaysOn sont susceptibles de gérer les deux premières situations, alors que la troisième situation ne peut être gérée que par la copie des journaux de transaction.

Vous devez trouver l’équilibre entre l’installation d’AlwaysOn et la mise en miroir de bases de données. La première est plus complexe, mais offre des avantages spécifiques. Parmi ces avantages, on peut citer :

* des réplicas secondaires accessibles en lecture ;
* des sauvegardes à partir des réplicas secondaires ;
* meilleure extensibilité ;
* possibilités de disposer de plusieurs réplicas secondaires.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Résumé – SQL Server général pour SAP sur Azure
Ce guide offre de nombreuses recommandations. Nous vous invitons à les parcourir plusieurs fois avant de planifier votre déploiement Azure. Cependant, de manière générale, vous devez suivre les dix points principaux spécifiques à la fonction SGBD (système de gestion de base de données) sur Azure :

[comment]: <> (Débit multiplié par 2,3 par rapport à quoi ? Than one VHD?)
1. Utilisez la dernière version du système SGBD (système de gestion de base de données), comme SQL Server 2014, qui présente les avantages les plus intéressants dans Azure. Pour SQL Server, il s’agit de SQL Server 2012 SP1 CU4, qui inclut la fonctionnalité de sauvegarde dans le Stockage Azure. Toutefois, en association avec SAP, nous recommandons au moins la version SQL Server 2014 SP1 CU1 ou SQL Server 2012 SP2, et la dernière unité de capacité.
2. Planifiez avec soin votre paysage de système SAP dans Azure, afin de trouver l’équilibre entre la disposition des fichiers de données et les restrictions d’Azure :
   * Évitez d’utiliser un trop grand nombre de disques. Cependant, vous devez en configurer suffisamment pour atteindre le nombre d’E/S par seconde nécessaire.
   * Si vous n’utilisez pas de disques managés, n’oubliez pas que les E/S par seconde sont limitées pour chaque compte Stockage Azure, et que les comptes de stockage sont limités au sein de chaque abonnement Azure ([plus d’informations ici][azure-subscription-service-limits]). 
   * N’effectuez une agrégation par bandes que si vous devez obtenir un débit supérieur.
3. N’installez pas vos logiciels et ne placez pas les fichiers nécessitant une persistance sur le lecteur D:\, car il n’est pas permanent. Les données placées sur ce lecteur ne sont pas conservées après le redémarrage de Windows.
4. N’utilisez pas la mise en cache de disque Azure pour le stockage Azure standard.
5. N’utilisez pas un compte de stockage Azure géorépliqué.  Utilisez des systèmes localement redondants pour les charges de travail SGBD (système de gestion de base de données).
6. Utilisez la solution de haute disponibilité/récupération d’urgence de votre fournisseur SGBD (système de gestion de base de données) pour répliquer les données des bases de données.
7. Utilisez toujours la fonction de résolution de noms ; ne vous fiez pas aux adresses IP.
8. Utilisez la fonctionnalité de compression de base de données la plus élevée possible. Il s’agit de la compression de page pour SQL Server.
9. Veillez à recourir à des images SQL Server de Microsoft Azure Marketplace. Si vous utilisez le serveur SQL numéro un, vous devez modifier le classement de l’instance avant d’installer un système SAP NetWeaver sur ce serveur.
10. Installez et configurez la surveillance d’hôte SAP pour Azure comme le décrit le [Guide de déploiement][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Caractéristiques de SAP ASE sur Windows
À partir de Microsoft Azure, vous pouvez facilement migrer vos applications SAP ASE existantes vers les machines virtuelles Azure. Dans une machine virtuelle, SAP ASE vous permet de réduire le coût total de possession lié au déploiement, à la gestion et à la maintenance des applications d’entreprise en les migrant facilement vers Microsoft Azure. Lorsque le logiciel SAP ASE est installé dans une machine virtuelle Azure, les administrateurs et développeurs peuvent continuer à utiliser les outils de développement et d’administration disponibles en local.

Il existe un contrat de niveau de service pour les machines virtuelles Azure, qui est disponible ici : <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Nous sommes convaincus que les machines virtuelles hébergées par Microsoft Azure fonctionnent bien, par rapport aux autres offres de virtualisation du cloud public. Cependant, les résultats peuvent varier au cas par cas. Dans le cas du dimensionnement SAP, les numéros SAP associés aux SKU des différentes machines virtuelles certifiées pour SAP sont fournis dans une autre note SAP : [1928533].

Les instructions et recommandations concernant l’utilisation d’Azure Storage, le déploiement de machines virtuelles SAP ou la surveillance SAP s’appliquent aux déploiements de SAP ASE conjointement avec des applications SAP, comme énoncé dans les quatre premiers chapitres de ce document.

### <a name="sap-ase-version-support"></a>Prise en charge des versions SAP ASE
Actuellement, SAP prend en charge SAP ASE version 16.0 pour une utilisation avec les produits SAP Business Suite. Toutes les mises à jour pour le serveur SAP ASE ou les pilotes JDBC et ODBC à utiliser avec les produits SAP Business Suite sont exclusivement fournies par le biais du SAP Service Marketplace à l’adresse suivante : <https://support.sap.com/swdc>

Dans le cas d’une installation locale, ne téléchargez pas les mises à jour pour le serveur SAP ASE ou les pilotes JDBC et ODBC directement depuis les sites web Sybase. Pour obtenir des informations détaillées sur les correctifs pris en charge pour une utilisation avec les produits SAP Business Suite en local et dans les machines virtuelles Azure, consultez les notes SAP suivantes :

* [1590719]
* [1973241]

Pour des informations générales sur l’exécution de SAP Business Suite sur SAP ASE, voir [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Instructions de configuration de SAP ASE pour les installations SAP ASE sur des machines virtuelles Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Structure du déploiement de SAP ASE
Conformément à la description générale, les exécutables SAP ASE doivent être situés ou installés sur le lecteur système du disque de la machine virtuelle (le lecteur C:\). En règle générale, la plupart des bases de données d’outils et système SAP ASE ne sont pas pleinement exploitées par les charges de travail SAP NetWeaver. Par conséquent, les bases de données d’outils et système (« master », « model », « saptools », « sybmgmtdb » et « sybsystemdb ») peuvent également rester sur le lecteur C:\. 

La base de données temporaire contenant toutes les tables de travail et les tables temporaires créées par SAP ASE peut constituer une exception qui, dans le cas de certains ERP SAP et pour toutes les charges de travail BW, peut nécessiter des volumes de données supérieurs ou des volumes d’E/S qui ne rentrent pas sur le disque du système d’exploitation de la machine virtuelle d’origine (le lecteur C:\).

Selon la version de SAPInst/SWPM utilisée pour installer le système, la base de données peut contenir les éléments suivants :

* Une seule base de données tempdb SAP ASE, créée lors de l’installation de SAP ASE
* une base de données tempdb SAP ASE créée via l’installation de SAP ASE ainsi qu’une base de données saptempdb supplémentaire, créée par le programme d’installation de SAP ;
* Une base de données tempdb SAP ASE créée par l’installation de SAP ASE, et une tempdb supplémentaire créée manuellement (voir note SAP [1752266]) pour répondre aux exigences ERP/BW de tempdb

En ce qui concerne les performances de charges de travail ERP spécifiques et de toutes les charges de travail BW, il peut être pertinent de placer les unités tempdb de la base de données tempdb créée en plus (via SWPM ou manuellement) sur un lecteur autre que C:\. Si aucune tempdb supplémentaire n’existe, il est recommandé d’en créer une (voir note SAP [1752266]).

Pour des systèmes de ce type, la procédure suivante doit être effectuée pour la base de données tempdb supplémentaire créée :

* Déplacez la première unité tempdb vers la première unité de la base de données SAP.
* Ajoutez des unités tempdb à chacun des disques VHD contenant une unité de base de données SAP.

Cette configuration permet à la base de données tempdb de consommer davantage d’espace que celui que peut proposer le lecteur système. Pour référence, vous pouvez consulter les tailles d’unités tempdb sur les systèmes existants qui s’exécutent en local. Une telle configuration permet d’atteindre un nombre d’E/S par seconde pour tempdb qui ne peut pas être fourni avec le lecteur système. Rappelons que les systèmes qui s’exécutent en local peuvent être utilisés pour surveiller la charge de travail d’E/S sur la base de données tempdb.

Ne placez jamais des unités SAP ASE sur le lecteur D:\ de la machine virtuelle. Cela s’applique également à la base de données tempdb, même si les objets conservés dans cette dernière sont temporaires.

#### <a name="impact-of-database-compression"></a>Impact de la compression de base de données
Dans les configurations pour lesquelles la bande passante d’E/S peut devenir un facteur de limitation, toutes les mesures qui réduisent le nombre d’E/S par seconde peuvent contribuer à étirer la charge de travail exécutable dans un scénario IaaS comme Azure. Par conséquent, il est recommandé de vérifier que la compression SAP ASE est utilisée avant le chargement d’une base de données SAP existante dans Azure.

Nous recommandons d’effectuer une compression avant le chargement sur Azure (si elle n’est pas déjà implémentée) pour plusieurs raisons :

* La quantité de données à charger dans Azure est moins importante.
* En supposant que l’on peut utiliser en local un matériel plus performant, avec plus d’UC, une bande passante d’E/S supérieure ou une latence d’E/S inférieure, la durée d’exécution de la compression est plus courte.
* Des bases de données plus petites peuvent permettre de diminuer les coûts liés à l’allocation de disque.

La compression des données et des éléments LOB fonctionne au sein d’une machine virtuelle hébergée dans Azure Virtual Machines comme en local. Pour savoir comment vérifier si la compression est déjà utilisée dans une base de données SAP ASE existante, consultez la note SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilisation de DBACockpit pour surveiller les instances de base de données
Pour les systèmes SAP utilisant SAP ASE en tant que plateforme de base de données, la fonction DBACockpit est accessible sous forme de fenêtres de navigateur intégrées dans Transaction DBACockpit ou via Webdynpro. Toutefois, l’ensemble des fonctionnalités de surveillance et d’administration de la base de données sont uniquement disponibles dans l’implémentation Webdynpro de DBACockpit.

À l’instar des systèmes locaux, plusieurs étapes sont nécessaires pour activer toutes les fonctionnalités de SAP NetWeaver utilisées par l’implémentation Webdynpro de DBACockpit. Pour activer l’utilisation de Webdynpro et générer les éléments nécessaires, consultez la note SAP [1245200]. Quand vous suivez les instructions fournies dans les notes ci-dessus, vous configurez également Internet Communication Manager (ICM), ainsi que les ports à utiliser pour les connexions HTTP et HTTPS. Pour http, la configuration par défaut ressemble à ceci :

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

Quant aux liens générés dans Transaction DBACockpit, ils ressemblent à ceci :

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

En fonction de la connexion éventuelle de la machine virtuelle hébergeant le système SAP via une méthode site à site, multisite ou ExpressRoute (déploiement entre différents locaux), vous devez vous assurer qu’ICM utilise un nom d’hôte complet qui peut être résolu sur la machine à partir de laquelle vous essayez d’ouvrir DBACockpit. Consultez la note SAP [773830] pour comprendre comment ICM détermine le nom d’hôte complet en fonction des paramètres de profil, puis définissez le paramètre icm/host_name_full explicitement si nécessaire.

Si vous avez déployé la machine virtuelle dans un scénario de cloud uniquement sans connectivité intersite entre le site local et Azure, vous devez définir une adresse IP publique et une étiquette de domaine. Le format du nom DNS public de la machine virtuelle ressemblera à ceci :

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Pour plus de détails sur le nom DNS, voir [ici][virtual-machines-azurerm-versus-azuresm].

Lorsque vous définissez le paramètre de profil SAP icm/host_name_full sur le nom DNS de la machine virtuelle Azure, le lien peut ressembler à ceci :

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dans ce cas, vous devez veiller à :

* Ajouter des règles de trafic entrant au groupe de sécurité réseau dans le portail Azure pour les ports TCP/IP utilisés afin de communiquer avec ICM
* ajouter des règles de trafic entrant à la configuration du pare-feu Windows pour les ports TCP/IP utilisés afin de communiquer avec ICM.

Pour effectuer une importation automatisée de toutes les corrections disponibles, il est recommandé d’appliquer périodiquement la note SAP de collection de correction applicable à votre version SAP :

* [1558958]
* [1619967]
* [1882376]

Vous trouverez plus d’informations sur DBA Cockpit pour SAP ASE dans les notes SAP suivantes :

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considérations relatives à la sauvegarde/restauration pour SAP ASE
Lorsque vous déployez SAP ASE dans Azure, votre méthodologie de sauvegarde doit être revue. Même si le système n’est pas un système productif, la base de données SAP hébergée par SAP ASE doit être sauvegardée régulièrement. Comme Azure Storage conserve trois images, la sauvegarde joue désormais un rôle moins important en matière de compensation des pannes du stockage. La raison principale du maintien d’un plan de sauvegarde et de restauration approprié réside davantage dans le fait que vous pouvez compenser les erreurs logiques/manuelles en fournissant des fonctionnalités de récupération jusqu’à une date et heure. Par conséquent, l’objectif est soit d’utiliser les sauvegardes pour restaurer la base de données à un moment donné, soit d’utiliser les sauvegardes dans Azure pour amorcer un autre système en copiant la base de données existante. Par exemple, vous avez la possibilité de transférer des données depuis une configuration SAP de niveau 2 vers une configuration système de niveau 3 du même système en restaurant une sauvegarde.

La sauvegarde et la restauration d’une base de données dans Azure fonctionnent de la même façon qu’en local. Consultez les notes SAP suivantes :

* [1588316]
* [1585981]

pour plus d’informations sur la création de configurations d’images mémoire et la planification des sauvegardes. Selon votre stratégie et vos besoins, vous pouvez configurer les images mémoire de bases de données et de journaux sur disque sur l’un des disques existants ou ajouter un disque supplémentaire pour la sauvegarde. Pour réduire le risque de perte de données en cas d’erreur, il est recommandé d’utiliser un disque ne contenant aucune unité de base de données.

Outre les données et la compression des éléments LOB, SAP ASE propose la compression de sauvegarde. Pour occuper moins d’espace avec les images mémoire de bases de données et de journaux, il est recommandé d’utiliser la compression de sauvegarde. Pour plus d’informations, consultez la note SAP [1588316]. La compression de sauvegarde est également cruciale pour réduire la quantité de données à transférer si vous prévoyez de télécharger en local des sauvegardes ou des disques durs virtuels contenant des images mémoire de sauvegarde à partir de la machine virtuelle Azure.

N’utilisez pas le lecteur D:\ comme destination des images mémoire de bases de données ou de journaux.

#### <a name="performance-considerations-for-backupsrestores"></a>Considérations sur les performances des sauvegardes/restaurations
À l’instar des déploiements complets, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit global de lecture est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (répertoires d’agrégation, disques) d’écriture de la sauvegarde, plus le débit est réduit

Pour augmenter le nombre de cibles d’écriture, deux options peuvent être utilisées ou combinées selon vos besoins :

* Agréger le volume cible de sauvegarde sur plusieurs disques montés, afin d’améliorer le débit d’E/S par seconde sur ce volume agrégé par bandes
* Créer une configuration d’image mémoire dans SAP ASE qui utilise plusieurs répertoires cibles pour l’écriture de l’image mémoire

L’agrégation d’un volume par bandes sur plusieurs disques montés a été évoquée précédemment dans ce guide. Pour plus d’informations sur l’utilisation de plusieurs répertoires pour la configuration d’images mémoire dans SAP ASE, consultez la documentation sur la procédure stockée sp_config_dump, utilisée pour créer la configuration d’images mémoire sur [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Récupération d’urgence avec les machines virtuelles Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Réplication de données avec le serveur de réplication Sybase SAP
Avec le serveur de réplication Sybase (SRS) SAP, SAP ASE fournit une solution de secours actif pour transférer des transactions de base de données vers un emplacement distant en mode asynchrone. 

L’installation et l’utilisation de SRS fonctionnent aussi bien dans une machine virtuelle hébergée dans Azure Virtual Machine Services qu’en local.

Le HADR SAP ASE ne nécessite pas d’équilibreur de charge interne Azure. En outre, il n’a pas de dépendances au clustering du système d’exploitation et fonctionne sur les machines virtuelles Azure Windows et Linux. Pour plus d’informations sur le HADR SAP ASE, lisez le [Guide de l’utilisateur du HADR SAP ASE](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Caractéristiques de SAP ASE sur Linux
À partir de Microsoft Azure, vous pouvez facilement migrer vos applications SAP ASE existantes vers les machines virtuelles Azure. Dans une machine virtuelle, SAP ASE vous permet de réduire le coût total de possession lié au déploiement, à la gestion et à la maintenance des applications d’entreprise en les migrant facilement vers Microsoft Azure. Lorsque le logiciel SAP ASE est installé dans une machine virtuelle Azure, les administrateurs et développeurs peuvent continuer à utiliser les outils de développement et d’administration disponibles en local.

Pour déployer des machines virtuelles Azure, il est important de connaître les contrats de niveau de service (SLA) officiels qui se trouvent à l’adresse suivante : <https://azure.microsoft.com/support/legal/sla>

Les informations de dimensionnement SAP et la liste des références SKU de machines virtuelles certifiées SAP sont fournies dans la note SAP [1928533]. D’autres documents SAP sur le redimensionnement des machines virtuelles Azure sont disponibles ici : <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> et ici : <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>.

Les instructions et recommandations concernant l’utilisation d’Azure Storage, le déploiement de machines virtuelles SAP ou la surveillance SAP s’appliquent aux déploiements de SAP ASE conjointement avec des applications SAP, comme énoncé dans les quatre premiers chapitres de ce document.

Les deux notes SAP suivantes incluent des informations générales relatives à ASE sur Linux et ASE dans le cloud :

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Prise en charge des versions SAP ASE
Actuellement, SAP prend en charge SAP ASE version 16.0 pour une utilisation avec les produits SAP Business Suite. Toutes les mises à jour pour le serveur SAP ASE ou les pilotes JDBC et ODBC à utiliser avec les produits SAP Business Suite sont exclusivement fournies par le biais du SAP Service Marketplace à l’adresse suivante : <https://support.sap.com/swdc>

Dans le cas d’une installation locale, ne téléchargez pas les mises à jour pour le serveur SAP ASE ou les pilotes JDBC et ODBC directement depuis les sites web Sybase. Pour obtenir des informations détaillées sur les correctifs pris en charge pour une utilisation avec les produits SAP Business Suite en local et dans les machines virtuelles Azure, consultez les notes SAP suivantes :

* [1590719]
* [1973241]

Pour des informations générales sur l’exécution de SAP Business Suite sur SAP ASE, voir [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Instructions de configuration de SAP ASE pour les installations SAP ASE sur des machines virtuelles Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Structure du déploiement de SAP ASE
Conformément à la description générale, les fichiers exécutables SAP ASE doivent être situés ou installés dans le système de fichiers racine de la machine virtuelle ( /sybase). En règle générale, la plupart des bases de données d’outils et système SAP ASE ne sont pas pleinement exploitées par les charges de travail SAP NetWeaver. Par conséquent, les bases de données de système et d’outils (master, model, saptools, sybmgmtdb, sybsystemdb) peuvent également demeurer sur le système de fichiers racine. 

La base de données temporaire contenant toutes les tables de travail et les tables temporaires créées par SAP ASE peut constituer une exception qui, dans le cas de certains ERP SAP et pour toutes les charges de travail BW, peut nécessiter des volumes de données supérieurs ou des volumes d’E/S qui ne rentrent pas sur le disque du système d’exploitation de la machine virtuelle d’origine.

Selon la version de SAPInst/SWPM utilisée pour installer le système, la base de données peut contenir les éléments suivants :

* Une seule base de données tempdb SAP ASE, créée lors de l’installation de SAP ASE
* une base de données tempdb SAP ASE créée via l’installation de SAP ASE ainsi qu’une base de données saptempdb supplémentaire, créée par le programme d’installation de SAP ;
* Une base de données tempdb SAP ASE créée par l’installation de SAP ASE, et une tempdb supplémentaire créée manuellement (voir note SAP [1752266]) pour répondre aux exigences ERP/BW de tempdb

Dans le cas de charges de travail ERP spécifiques ou pour toutes les charges BW, il est judicieux, en ce qui concerne les performances, de conserver les unités tempdb de la tempdb supplémentaire créée (par SWPM ou manuellement) sur un système de fichiers distinct qui peut être représenté par un disque de données Azure unique ou un volume RAID Linux s’étendant sur plusieurs disques de données Azure. Si aucune tempdb supplémentaire n’existe, il est recommandé d’en créer une (voir note SAP [1752266]).

Pour des systèmes de ce type, la procédure suivante doit être effectuée pour la base de données tempdb supplémentaire créée :

* Déplacer le premier répertoire tempdb vers le premier système de fichiers de la base de données SAP
* Ajouter des répertoires tempdb à chacun des disques contenant un système de fichiers de la base de données SAP

Cette configuration permet à la base de données tempdb de consommer davantage d’espace que celui que peut proposer le lecteur système. Pour référence, vous pouvez consulter les tailles de répertoire tempdb sur les systèmes existants qui s’exécutent en local. Une telle configuration permet d’atteindre un nombre d’E/S par seconde pour tempdb qui ne peut pas être fourni avec le lecteur système. Rappelons que les systèmes qui s’exécutent en local peuvent être utilisés pour surveiller la charge de travail d’E/S sur la base de données tempdb.

Ne placez jamais de répertoire SAP ASE aux emplacements /mnt ou /mnt/resource de la machine virtuelle. Cela s’applique également à la tempdb, même si les objets conservés dans la tempdb sont seulement temporaires, car /mnt ou/mnt/resource sont des espaces temporaires par défaut non persistants de machine virtuelle Azure. Vous trouverez plus d’informations sur l’espace temporaire de machine virtuelle Azure dans [cet article][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impact de la compression de base de données
Dans les configurations pour lesquelles la bande passante d’E/S peut devenir un facteur de limitation, toutes les mesures qui réduisent le nombre d’E/S par seconde peuvent contribuer à étirer la charge de travail exécutable dans un scénario IaaS comme Azure. Par conséquent, il est recommandé de vérifier que la compression SAP ASE est utilisée avant le chargement d’une base de données SAP existante dans Azure.

Nous recommandons d’effectuer une compression avant le chargement sur Azure (si elle n’est pas déjà implémentée) pour plusieurs raisons :

* La quantité de données à charger dans Azure est moins importante.
* En supposant que l’on peut utiliser en local un matériel plus performant, avec plus d’UC, une bande passante d’E/S supérieure ou une latence d’E/S inférieure, la durée d’exécution de la compression est plus courte.
* Des bases de données plus petites peuvent permettre de diminuer les coûts liés à l’allocation de disque.

La compression des données et des éléments LOB fonctionne au sein d’une machine virtuelle hébergée dans Azure Virtual Machines comme en local. Pour savoir comment vérifier si la compression est déjà utilisée dans une base de données SAP ASE existante, consultez la note SAP [1750510]. Pour plus d’informations sur la compression des bases de données, consultez la note SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilisation de DBACockpit pour surveiller les instances de base de données
Pour les systèmes SAP utilisant SAP ASE en tant que plateforme de base de données, la fonction DBACockpit est accessible sous forme de fenêtres de navigateur intégrées dans Transaction DBACockpit ou via Webdynpro. Toutefois, l’ensemble des fonctionnalités de surveillance et d’administration de la base de données sont uniquement disponibles dans l’implémentation Webdynpro de DBACockpit.

À l’instar des systèmes locaux, plusieurs étapes sont nécessaires pour activer toutes les fonctionnalités de SAP NetWeaver utilisées par l’implémentation Webdynpro de DBACockpit. Pour activer l’utilisation de Webdynpro et générer les éléments nécessaires, consultez la note SAP [1245200]. Quand vous suivez les instructions fournies dans les notes ci-dessus, vous configurez également Internet Communication Manager (ICM), ainsi que les ports à utiliser pour les connexions HTTP et HTTPS. Pour http, la configuration par défaut ressemble à ceci :

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

Quant aux liens générés dans Transaction DBACockpit, ils ressemblent à ceci :

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

En fonction de la connexion éventuelle de la machine virtuelle hébergeant le système SAP via une méthode site à site, multisite ou ExpressRoute (déploiement entre différents locaux), vous devez vous assurer qu’ICM utilise un nom d’hôte complet qui peut être résolu sur la machine à partir de laquelle vous essayez d’ouvrir DBACockpit. Consultez la note SAP [773830] pour comprendre comment ICM détermine le nom d’hôte complet en fonction des paramètres de profil, puis définissez le paramètre icm/host_name_full explicitement si nécessaire.

Si vous avez déployé la machine virtuelle dans un scénario de cloud uniquement sans connectivité intersite entre le site local et Azure, vous devez définir une adresse IP publique et une étiquette de domaine. Le format du nom DNS public de la machine virtuelle ressemblera à ceci :

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Pour plus de détails sur le nom DNS, voir [ici][virtual-machines-azurerm-versus-azuresm].

Lorsque vous définissez le paramètre de profil SAP icm/host_name_full sur le nom DNS de la machine virtuelle Azure, le lien peut ressembler à ceci :

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dans ce cas, vous devez veiller à :

* Ajouter des règles de trafic entrant au groupe de sécurité réseau dans le portail Azure pour les ports TCP/IP utilisés afin de communiquer avec ICM
* ajouter des règles de trafic entrant à la configuration du pare-feu Windows pour les ports TCP/IP utilisés afin de communiquer avec ICM.

Pour effectuer une importation automatisée de toutes les corrections disponibles, il est recommandé d’appliquer périodiquement la note SAP de collection de correction applicable à votre version SAP :

* [1558958]
* [1619967]
* [1882376]

Vous trouverez plus d’informations sur DBA Cockpit pour SAP ASE dans les notes SAP suivantes :

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considérations relatives à la sauvegarde/restauration pour SAP ASE
Lors du déploiement de SAP ASE dans Azure, votre méthodologie de sauvegarde doit être passée en revue. Même si le système n’est pas un système productif, la base de données SAP hébergée par SAP ASE doit être sauvegardée régulièrement. Comme Azure Storage conserve trois images, la sauvegarde joue désormais un rôle moins important en matière de compensation des pannes du stockage. La raison principale du maintien d’un plan de sauvegarde et de restauration approprié réside davantage dans le fait que vous pouvez compenser les erreurs logiques/manuelles en fournissant des fonctionnalités de récupération jusqu’à une date et heure. Par conséquent, l’objectif est soit d’utiliser les sauvegardes pour restaurer la base de données à un moment donné, soit d’utiliser les sauvegardes dans Azure pour amorcer un autre système en copiant la base de données existante. Par exemple, vous avez la possibilité de transférer des données depuis une configuration SAP de niveau 2 vers une configuration système de niveau 3 du même système en restaurant une sauvegarde.

La sauvegarde et la restauration d’une base de données dans Azure fonctionnent de la même façon qu’en local. Consultez les notes SAP suivantes :

* [1588316]
* [1585981]

pour plus d’informations sur la création de configurations d’images mémoire et la planification des sauvegardes. Selon votre stratégie et vos besoins, vous pouvez configurer les images mémoire de bases de données et de journaux sur disque sur l’un des disques existants ou ajouter un disque supplémentaire pour la sauvegarde. Pour réduire le risque de perte de données en cas d’erreur, il est recommandé d’utiliser un disque ne contenant aucun répertoire/fichier de base de données.

Outre les données et la compression des éléments LOB, SAP ASE propose la compression de sauvegarde. Pour occuper moins d’espace avec les images mémoire de bases de données et de journaux, il est recommandé d’utiliser la compression de sauvegarde. Pour plus d’informations, consultez la note SAP [1588316]. La compression de sauvegarde est également cruciale pour réduire la quantité de données à transférer si vous prévoyez de télécharger en local des sauvegardes ou des disques durs virtuels contenant des images mémoire de sauvegarde à partir de la machine virtuelle Azure.

N’utilisez pas l’espace temporaire de la machine virtuelle Azure /mnt ou/mnt/resource comme destination d’images mémoire de bases de données ou de journaux.

#### <a name="performance-considerations-for-backupsrestores"></a>Considérations sur les performances des sauvegardes/restaurations
À l’instar des déploiements complets, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit global de lecture est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (volume RAID logiciel Linux, disques) d’écriture de la sauvegarde, plus le débit est réduit

Pour augmenter le nombre de cibles d’écriture, deux options peuvent être utilisées ou combinées selon vos besoins :

* Agréger le volume cible de sauvegarde sur plusieurs disques montés, afin d’améliorer le débit d’E/S par seconde sur ce volume agrégé par bandes
* Créer une configuration d’image mémoire dans SAP ASE qui utilise plusieurs répertoires cibles pour l’écriture de l’image mémoire

L’agrégation d’un volume par bandes sur plusieurs disques montés a été évoquée précédemment dans ce guide. Pour plus d’informations sur l’utilisation de plusieurs répertoires pour la configuration d’images mémoire dans SAP ASE, consultez la documentation sur la procédure stockée sp_config_dump, utilisée pour créer la configuration d’images mémoire sur [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Récupération d’urgence avec les machines virtuelles Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Réplication de données avec le serveur de réplication Sybase SAP
Avec le serveur de réplication Sybase (SRS) SAP, SAP ASE fournit une solution de secours actif pour transférer des transactions de base de données vers un emplacement distant en mode asynchrone. 

L’installation et l’utilisation de SRS fonctionnent aussi bien dans une machine virtuelle hébergée dans Azure Virtual Machine Services qu’en local.

La fonction ASE HADR (haute disponibilité et récupération d’urgence) via le serveur de réplication SAP n’est PAS prise en charge pour le moment. Elle sera peut-être testée et publiée pour les plateformes Microsoft Azure à l’avenir.

## <a name="specifics-to-oracle-database-on-windows"></a>Caractéristiques d’Oracle Database sur Windows
Les logiciels Oracle sont pris en charge par Oracle pour s’exécuter dans Microsoft Windows Hyper-V et Azure. Pour plus d’informations sur la prise en charge générale de Windows Hyper-V et d’Azure, consultez : <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Outre la prise en charge générale, le scénario spécifique des applications SAP exploitant les bases de données Oracle est également pris en charge. Les détails sont évoqués dans cette partie du document.

### <a name="oracle-version-support"></a>Prise en charge des versions Oracle
Les versions Oracle et les versions de systèmes d’exploitation correspondantes prises en charge pour l’exécution de SAP sur Oracle dans des machines virtuelles Azure sont répertoriées dans la note SAP [2039619].

Pour obtenir des informations d’ordre général sur l’exécution de SAP Business Suite sur Oracle, consultez <https://www.sap.com/community/topic/oracle.html>.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration Oracle pour les installations SAP sur des machines virtuelles Azure
#### <a name="storage-configuration"></a>Configuration du stockage
Une seule instance Oracle utilisant les disques au format NTFS est prise en charge. Tous les fichiers de base de données doivent être stockés sur le système de fichiers NTFS basé sur les disques durs virtuels ou les disques managés. Ces disques sont montés sur la machine virtuelle Azure, et sont basés sur le stockage d’objets blob de pages Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou sur des disques managés (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Tous les types de lecteurs réseau ou de partages distants tels que les services de fichiers Azure :

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

ne sont **PAS** pris en charge pour les fichiers de base de données Oracle !

Si vous utilisez des disques basés sur le stockage d’objets blob de pages Azure ou des disques managés, les instructions figurant dans les chapitres [Mise en cache pour les machines virtuelles et les disques de données][dbms-guide-2.1] et [Stockage Microsoft Azure][dbms-guide-2.3] de ce document s’appliquent également aux déploiements avec Oracle Database.

Comme expliqué précédemment dans la partie générale du document, des quotas existent en ce qui concerne le débit d’E/S par seconde pour les disques Azure. Les quotas exacts dépendent du type de machine virtuelle utilisé. La liste des types de machines virtuelles et de leurs quotas est disponible [ici (pour Linux)][virtual-machines-sizes-linux] et [ici (pour Windows)][virtual-machines-sizes-windows].

Pour connaître les types de machines virtuelles Azure pris en charge, consultez la note SAP [1928533].

Tant que le quota actuel d’E/S par seconde par disque satisfait aux exigences, il est possible de stocker tous les fichiers de base de données sur un seul disque Azure monté. 

Si des E/S par seconde supplémentaires sont nécessaires, il est recommandé d’utiliser des pools de stockage Windows (fonctionnalité uniquement disponible dans Windows Server 2012 et versions ultérieures) ou l’agrégation Windows pour Windows 2008 R2, afin de créer une unité logique volumineuse sur plusieurs disques montés (voir également le chapitre [RAID logiciel][dbms-guide-2.2] de ce document). Cette approche simplifie les tâches administratives pour gérer l’espace disque et évite les tâches de distribution manuelles des fichiers sur plusieurs disques montés.

#### <a name="backup--restore"></a>Sauvegarde / restauration
Pour la fonctionnalité de sauvegarde / restauration, les outils SAP BR*Tools for Oracle sont pris en charge de la même manière que sur les systèmes d’exploitation Windows Server et Hyper-V standard. Oracle Recovery Manager (RMAN) est également pris en charge pour les sauvegardes sur disque et les restaurations à partir du disque.

#### <a name="high-availability"></a>Haute disponibilité
Oracle Data Guard est pris en charge à des fins de haute disponibilité et de récupération d’urgence. Pour plus d’informations, consultez [cette][virtual-machines-windows-classic-configure-oracle-data-guard] documentation.

#### <a name="other"></a>Autres
Tous les autres sujets généraux, notamment les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent à Oracle Database, comme décrit dans les trois premiers chapitres de ce document pour le déploiement de machines virtuelles.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Caractéristiques d’Oracle Database sur Oracle Linux
Les logiciels Oracle sont pris en charge par Oracle pour s’exécuter dans Microsoft Windows Hyper-V et Azure. Pour plus d’informations sur la prise en charge générale de Windows Hyper-V et d’Azure, consultez : <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Outre la prise en charge générale, le scénario spécifique des applications SAP exploitant les bases de données Oracle est également pris en charge. Les détails sont évoqués dans cette partie du document.

### <a name="oracle-version-support"></a>Prise en charge des versions Oracle
Les versions Oracle et les versions de systèmes d’exploitation correspondantes prises en charge pour l’exécution de SAP sur Oracle dans des machines virtuelles Azure sont répertoriées dans la note SAP [2039619].

Pour obtenir des informations d’ordre général sur l’exécution de SAP Business Suite sur Oracle, consultez <https://www.sap.com/community/topic/oracle.html>.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration Oracle pour les installations SAP sur des machines virtuelles Azure
#### <a name="storage-configuration"></a>Configuration du stockage
Une seule instance Oracle utilisant les disques formatés ext3, ext4 et xfs est prise en charge. Tous les fichiers de base de données doivent être stockés sur ces systèmes de fichiers basés sur les disques durs virtuels ou les disques managés. Ces disques sont montés sur la machine virtuelle Azure, et sont basés sur le stockage d’objets blob de pages Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou sur des disques managés (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Tous les types de lecteurs réseau ou de partages distants tels que les services de fichiers Azure :

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

ne sont **PAS** pris en charge pour les fichiers de base de données Oracle !

Si vous utilisez des disques basés sur le stockage d’objets blob de pages Azure ou des disques managés, les instructions figurant dans les chapitres [Mise en cache pour les machines virtuelles et les disques de données][dbms-guide-2.1] et [Stockage Microsoft Azure][dbms-guide-2.3] de ce document s’appliquent également aux déploiements avec Oracle Database.

Comme expliqué précédemment dans la partie générale du document, des quotas existent en ce qui concerne le débit d’E/S par seconde pour les disques Azure. Les quotas exacts dépendent du type de machine virtuelle utilisé. La liste des types de machines virtuelles et de leurs quotas est disponible [ici (pour Linux)][virtual-machines-sizes-linux] et [ici (pour Windows)][virtual-machines-sizes-windows].

Pour connaître les types de machines virtuelles Azure pris en charge, consultez la note SAP [1928533].

Tant que le quota actuel d’E/S par seconde par disque satisfait aux exigences, il est possible de stocker tous les fichiers de base de données sur un seul disque Azure monté. 

Si vous avez besoin d’un taux d’E/S par seconde plus important, il est recommandé d’utiliser MDADM ou LVM (Logical Volume Manager) pour créer un seul grand volume logique sur plusieurs disques montés. Voir également le chapitre [RAID logiciel][dbms-guide-2.2] de ce document. Cette approche simplifie les tâches administratives pour gérer l’espace disque et évite les tâches de distribution manuelles des fichiers sur plusieurs disques montés.

#### <a name="backup--restore"></a>Sauvegarde / restauration
Pour la fonctionnalité de sauvegarde/restauration, les outils SAP BR*Tools for Oracle sont pris en charge de la même manière que sur les systèmes nus et les systèmes Hyper-V. Oracle Recovery Manager (RMAN) est également pris en charge pour les sauvegardes sur disque et les restaurations à partir du disque.

#### <a name="high-availability"></a>Haute disponibilité
Oracle Data Guard est pris en charge à des fins de haute disponibilité et de récupération d’urgence. Pour plus d’informations, consultez [cette][virtual-machines-windows-classic-configure-oracle-data-guard] documentation.

#### <a name="other"></a>Autres
Tous les autres sujets généraux, notamment les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent à Oracle Database, comme décrit dans les trois premiers chapitres de ce document pour le déploiement de machines virtuelles.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Caractéristiques de la base de données SAP MaxDB sur Windows
### <a name="sap-maxdb-version-support"></a>Prise en charge des versions SAP MaxDB
Actuellement, SAP prend en charge SAP MaxDB version 7.9 pour une utilisation avec les produits reposant sur SAP NetWeaver dans Azure. Toutes les mises à jour pour le serveur SAP MaxDB ou les pilotes JDBC et ODBC à utiliser avec les produits SAP NetWeaver sont exclusivement fournies par le biais du SAP Service Marketplace à l’adresse suivante : <https://support.sap.com/swdc>.
Vous trouverez des informations générales sur l’exécution de SAP NetWeaver sur SAP MaxDB à l’adresse suivante : <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Versions de Microsoft Windows et types de machines virtuelles Azure pris en charge pour SGBD SAP MaxDB
Pour connaître la version de Microsoft Windows prise en charge pour SGBD SAP MaxDB sur Azure, consultez :

* [Tableau de disponibilité des produits SAP][sap-pam]
* Note SAP [1928533]

Il est vivement recommandé d’utiliser la version la plus récente du système d’exploitation Microsoft Windows : Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentation SAP MaxDB disponible
Vous trouverez la liste mise à jour de la documentation SAP MaxDB dans la Note de SAP suivante [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration SAP MaxDB pour les installations SAP sur des machines virtuelles Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuration du stockage
Les bonnes pratiques de stockage Azure pour SAP MaxDB suivent les recommandations générales mentionnées dans le chapitre [Structure d’un déploiement SGBDR][dbms-guide-2].

> [!IMPORTANT]
> Comme d’autres bases de données, SAP MaxDB possède des données et des fichiers journaux. Toutefois, dans la terminologie SAP MaxDB, le terme correct est « volume » (et non « fichier »). Par exemple, il existe des volumes de données et des volumes de journaux SAP MaxDB. Ne les confondez pas avec les volumes de disque du système d’exploitation. 
> 
> 

En bref, voici que vous avez à faire :

* Si vous utilisez des comptes de stockage Azure, définissez le compte de stockage Azure qui contient les volumes de données et de journaux SAP MaxDB (c’est-à-dire les fichiers) sur **Stockage localement redondant (LRS)**, comme indiqué dans le chapitre [Stockage Microsoft Azure][dbms-guide-2.3].
* Séparez le chemin d’accès d’E/S pour les volumes de données SAP MaxDB (c’est-à-dire les fichiers) du chemin d’accès d’E/S pour les volumes de journaux (c’est-à-dire les fichiers). Cela signifie que les volumes de données SAP MaxDB (c’est-à-dire les fichiers) doivent être installés sur un lecteur logique et que les volumes de journaux SAP MaxDB (c’est-à-dire les fichiers) doivent être installés sur un autre lecteur logique.
* Définissez le type de mise en cache approprié pour chaque disque, selon que vous l’utilisez pour des volumes de données ou de journaux SAP MaxDB (c’est-à-dire les fichiers), et que vous utilisez le Stockage Standard Azure ou le Stockage Premium Azure, comme le décrit le chapitre [Mise en cache pour les machines virtuelles et les disques de données][dbms-guide-2.1].
* Tant que le quota actuel d’E/S par seconde par disque satisfait aux exigences, il est possible de stocker tous les volumes de données sur un seul disque monté, et également de stocker tous les volumes de journaux de bases de données sur un autre disque unique monté.
* Si des E/S par seconde supplémentaires et/ou plus d’espace sont nécessaires, il est recommandé d’utiliser des pools de stockage Microsoft Windows (fonctionnalité uniquement disponible dans Microsoft Windows Server 2012 et versions ultérieures) ou l’agrégation de Microsoft Windows pour Microsoft Windows 2008 R2, afin de créer une unité logique volumineuse sur plusieurs disques montés. Voir également le chapitre [RAID logiciel][dbms-guide-2.2] de ce document. Cette approche simplifie les tâches administratives pour gérer l’espace disque et évite les tâches de distribution manuelles des fichiers sur plusieurs disques montés.
* Pour les exigences d’E/S par seconde les plus élevées, vous pouvez utiliser Azure Premium Storage, qui est disponible sur les machines virtuelles séries DS et GS.

![Configuration de référence de la machine virtuelle IaaS Azure pour SGBD SAP MaxDB][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Sauvegarde et restauration
Lors du déploiement de SAP MaxDB dans Azure, vous devez passer en revue votre méthodologie de sauvegarde. Même si le système n’est pas un système productif, la base de données SAP hébergée par SAP MaxDB doit être sauvegardée régulièrement. Étant donné qu’Azure Storage conserve trois images, une sauvegarde est désormais moins importante en termes de protection de votre système contre les défaillances de stockage, ou les défaillances opérationnelles ou administratives plus sérieuses. La raison principale du maintien d’un plan de sauvegarde et de restauration approprié est que vous pouvez compenser les erreurs logiques ou manuelles en fournissant des fonctionnalités de récupération jusqu’à une date et heure. Par conséquent, l’objectif est soit d’utiliser les sauvegardes pour restaurer la base de données à un certain point dans le temps, soit d’utiliser les sauvegardes dans Azure pour amorcer un autre système en copiant la base de données existante. Par exemple, vous avez la possibilité de transférer des données depuis une configuration SAP de niveau 2 vers une configuration système de niveau 3 du même système en restaurant une sauvegarde.

La sauvegarde et la restauration d’une base de données dans Azure fonctionnent de la même manière que pour les systèmes locaux. Vous pouvez donc utiliser les outils de sauvegarde/restauration SAP MaxDB standard décrits dans l’un des documents de la documentation SAP MaxDB répertoriée dans la Note de SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considérations sur les performances de sauvegarde et de restauration
À l’instar des déploiements sur système nu, les performances de sauvegarde et de restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit de lecture global est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (répertoires d’agrégation, disques) d’écriture de la sauvegarde, plus le débit est faible

Pour augmenter le nombre de cibles d’écriture, vous pouvez utiliser deux options, éventuellement en association, selon vos besoins :

* Dédier des volumes distincts pour la sauvegarde
* Agréger le volume cible de sauvegarde sur plusieurs disques montés, afin d’améliorer le débit d’E/S par seconde sur ce volume de disque agrégé par bandes
* Disposer d’unités de disque logiques dédiées distinctes pour les éléments suivants :
  * Volumes de sauvegarde SAP MaxDB (c’est-à-dire les fichiers)
  * Volumes de données SAP MaxDB (c’est-à-dire les fichiers)
  * Volumes de journaux SAP MaxDB (c’est-à-dire les fichiers)

L’agrégation d’un volume par bandes sur plusieurs disques montés a été évoquée précédemment dans le chapitre [RAID logiciel][dbms-guide-2.2] de ce document. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Autres
Tous les autres sujets généraux, tels que les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent également à la base de données SAP MaxDB, comme décrit dans les trois premiers chapitres de ce document pour le déploiement de machines virtuelles.
D’autres paramètres spécifiques à SAP MaxDB sont transparents pour les machines virtuelles Azure et sont décrits dans différents documents figurant dans la Note de SAP [767598] et dans les Notes de SAP suivantes :

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Caractéristiques de SAP liveCache sur Windows
### <a name="sap-livecache-version-support"></a>Prise en charge des versions SAP liveCache
La version minimale de SAP liveCache prise en charge dans les machines virtuelles Azure est **SAP LC/LCAPPS 10.0 SP 25** incluant **liveCache 7.9.08.31** et **LCA-Build 25** pour **EhP 2 for SAP SCM 7.0** et les versions ultérieures.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Versions de Microsoft Windows et types de machines virtuelles Azure pris en charge pour SGBD SAP liveCache
Pour connaître la version de Microsoft Windows prise en charge pour SAP liveCache sur Azure, consultez :

* [Tableau de disponibilité des produits SAP][sap-pam]
* Note SAP [1928533]

Il est vivement recommandé d’utiliser la version la plus récente du système d’exploitation Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration SAP liveCache pour les installations SAP sur des machines virtuelles Azure
#### <a name="recommended-azure-vm-types"></a>Types de machines virtuelles Azure recommandés
SAP liveCache étant une application qui effectue des calculs énormes, la quantité et la vitesse de la mémoire RAM et de l’UC ont des répercussions importantes sur les performances de SAP liveCache. 

Pour les types de machines virtuelles Azure pris en charge par SAP (Note de SAP [1928533]), toutes les ressources de l’UC virtuel allouées à la machine virtuelle sont soutenues par des ressources UC physiques dédiées de l’hyperviseur. Aucun surprovisionnement (et par conséquent aucune concurrence pour les ressources UC) n’a lieu.

De même, pour tous les types d’instances de machine virtuelle Azure pris en charge par SAP, la mémoire de la machine virtuelle est mappée à la mémoire physique à 100 %. Ainsi, le surprovisionnement (surengagement) n’est pas utilisé.

De ce point de vue, il est vivement recommandé d’utiliser le nouveau type de machines virtuelles Azure des séries D ou DS (en association avec le Stockage Premium Azure), car elles sont dotées de processeurs 60 % plus rapides que ceux de la série A. Pour la charge de mémoire RAM et de processeur la plus élevée, vous pouvez utiliser des machines virtuelles de série G et de série GS (en association avec le Stockage Azure Premium) avec la famille de processeurs Intel ?? Xeon?? E5 v3 la plus récente, qui ont deux fois plus de mémoire et quatre fois plus de capacité de stockage SSD (Solid State Drive) que la série D/DS.

#### <a name="storage-configuration"></a>Configuration du stockage
SAP liveCache étant basé sur la technologie SAP MaxDB, toutes les recommandations en matière de meilleures pratiques de stockage Azure mentionnées pour SAP MaxDB dans le chapitre [Configuration du stockage][dbms-guide-8.4.1] sont également valides pour SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Machine virtuelle Azure dédiée pour liveCache
Comme SAP liveCache utilise intensivement la puissance de calcul, pour une utilisation productive, il est fortement recommandé de déployer sur une machine virtuelle Azure dédiée. 

![Machine virtuelle Azure dédiée à liveCache pour les cas d’utilisation productive][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Sauvegarde et restauration
La sauvegarde et la restauration, ainsi que les considérations sur les performances, sont déjà décrites dans les chapitres SAP MaxDB correspondants [Sauvegarde et restauration][dbms-guide-8.4.2] et [Considérations sur les performances de sauvegarde et de restauration][dbms-guide-8.4.3]. 

#### <a name="other"></a>Autres
Tous les autres sujets généraux sont déjà décrits dans [ce chapitre][dbms-guide-8.4.4] concernant SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Caractéristiques de SAP Content Server sur Windows
SAP Content Server est un composant distinct, basé sur le serveur pour stocker du contenu, tels que les documents électroniques dans différents formats. SAP Content Server est fourni par le développement de la technologie et doit être utilisé entre les applications SAP. Il est installé sur un système distinct. Le contenu standard consiste en des supports et documents de formation issus de Knowledge Warehouse ou en des dessins techniques provenant du système de gestion des documents mySAP PLM. 

### <a name="sap-content-server-version-support"></a>Prise en charge des versions SAP Content Server
Actuellement, SAP prend en charge :

* **SAP Content Server** version **6.50 (et versions supérieures)**
* **SAP MaxDB version 7.9**
* **Microsoft IIS (Internet Information Server) version 8.0 (et versions supérieures)**

Il est vivement recommandé d’utiliser la version la plus récente de SAP Content Server qui, au moment de la rédaction de ce document, est la version **6.50 SP4**, ainsi que la version la plus récente de **Microsoft IIS 8.5**. 

Vérifiez les dernières versions prises en charge de SAP Content Server et de Microsoft IIS dans le [Tableau de disponibilité des produits SAP][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Versions de Microsoft Windows et types de machines virtuelles Azure pris en charge pour SAP Content Server
Pour découvrir la version de Windows prise en charge pour SAP Content Server sur Azure, consultez :

* [Tableau de disponibilité des produits SAP][sap-pam]
* Note SAP [1928533]

Il est vivement recommandé d’utiliser la version la plus récente de Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration SAP Content Server pour les installations SAP sur des machines virtuelles Azure
#### <a name="storage-configuration"></a>Configuration du stockage
Si vous configurez SAP Content Server pour stocker des fichiers dans la base de données SAP MaxDB, l’ensemble des recommandations en matière de meilleures pratiques de stockage Azure mentionnées pour SAP MaxDB dans le chapitre [Configuration du stockage][dbms-guide-8.4.1] sont également valides pour le scénario SAP Content Server. 

Si vous configurez SAP Content Server pour stocker les fichiers dans le système de fichiers, il est recommandé d’utiliser un lecteur logique dédié. L’utilisation des espaces de stockage Windows vous permet d’augmenter également la taille du disque logique et le débit d’E/S par seconde, comme le décrit le chapitre [RAID logiciel][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Emplacement de SAP Content Server
SAP Content Server doit être déployé dans la même région Azure et le même réseau virtuel Azure de déploiement du système SAP. Vous êtes libre de décider si vous souhaitez déployer les composants SAP Content Server sur une machine virtuelle Azure dédiée ou sur la même machine virtuelle d’exécution du système SAP. 

![Machine virtuelle Azure dédiée pour SAP Content Server][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Emplacement de SAP Cache Server
SAP Cache Server est un composant supplémentaire basé sur serveur pour fournir l’accès aux documents (mis en cache) en local. SAP Cache Server met en cache les documents d’un SAP Content Server. Cela permet d’optimiser le trafic réseau si les documents doivent être récupérés plusieurs fois à partir de différents emplacements. La règle générale est que SAP Cache Server doit être physiquement proche du client qui y accède. 

Ici, deux options s’offrent à vous :

1. **Le client est un système SAP principal** Si un système SAP principal est configuré pour accéder à SAP Content Server, ce système SAP est un client. Comme le système SAP et SAP Content Server sont déployés dans la même région Azure (dans le même centre de données Azure), ils sont physiquement proches l’un de l’autre. Par conséquent, il n’est pas nécessaire de disposer d’un SAP Cache Server dédié. Les clients de l’interface utilisateur SAP (GUI ou navigateur web SAP) accèdent directement au système SAP, et celui-ci récupère les documents à partir du SAP Content Server.
2. **Le client est un navigateur web local** SAP Content Server peut être configuré pour être accessible directement par le navigateur web. Dans ce cas, un navigateur web exécuté localement est un client de SAP Content Server. Le centre de données local et le centre de données Azure se situent à des emplacements physiques différents (idéalement, proches l’un de l’autre). Votre centre de données local est connecté à Azure via le VPN de site à site Azure ou ExpressRoute. Bien que les deux options offrent une connexion réseau VPN sécurisée à Azure, la connexion réseau de site à site n’offre pas de contrat de niveau de service (SLA) pour la bande passante réseau et la latence entre le centre de données local et le centre de données Azure. Pour accélérer l’accès aux documents, vous pouvez effectuer l’une des opérations suivantes :
   1. Installer SAP Cache Server en local, à proximité du navigateur web local (option sur [cette][dbms-guide-900-sap-cache-server-on-premises] figure)
   2. Configurer Azure ExpressRoute, qui offre une connexion réseau dédiée à haut débit et à faible latence entre le centre de données local et le centre de données Azure.

![Possibilité d’installer SAP Cache Server en local][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Sauvegarde / restauration
Si vous configurez SAP Content Server pour stocker des fichiers dans la base de données SAP MaxDB, la procédure de sauvegarde/restauration et les considérations sur les performances sont déjà décrites dans les chapitres SAP MaxDB [Sauvegarde et restauration][dbms-guide-8.4.2] et [Considérations sur les performances de sauvegarde et de restauration][dbms-guide-8.4.3]. 

Si vous configurez SAP Content Server pour stocker les fichiers dans le système de fichiers, l’une des options consiste à exécuter manuellement la sauvegarde/restauration de l’ensemble de la structure de fichiers dans laquelle se trouvent les documents. Comme pour la sauvegarde/restauration SAP MaxDB, il est recommandé de disposer d’un volume de disque dédié aux fins de sauvegarde. 

#### <a name="other"></a>Autres
Les autres paramètres propres à SAP Content Server sont transparents pour les machines virtuelles Azure et sont décrits dans différents documents et Notes SAP :

* <https://service.sap.com/contentserver> 
* Note de SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Caractéristiques d’IBM DB2 pour LUW sous Windows
Avec Microsoft Azure, vous pouvez facilement migrer votre application SAP existante exécutée sur IBM DB2 pour Linux, UNIX et Windows (LUW) vers les machines virtuelles Azure. Avec SAP sur IBM DB2 pour LUW, les administrateurs et les développeurs peuvent utiliser les outils de développement et d’administration disponibles en local.
Des informations générales sur l’exécution de SAP Business Suite sur IBM DB2 pour LUW sont disponibles dans SAP Community Network (SCN) à l’adresse suivante : <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Pour des informations et des mises à jour supplémentaires de SAP sur DB2 pour LUW sur Azure, voir la Note de SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Prise en charge des versions IBM DB2 pour Linux, UNIX et Windows
SAP sur IBM DB2 pour LUW est pris en charge sur les services de machines virtuelles Microsoft Azure à compter de la version DB2 10.5.

Pour plus d’informations sur les produits SAP et les types de machines virtuelles Azure pris en charge, consultez la note SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration IBM DB2 pour Linux, UNIX et Windows pour les installations SAP sur des machines virtuelles Azure
#### <a name="storage-configuration"></a>Configuration du stockage
Tous les fichiers de base de données doivent être stockés sur le système de fichiers NTFS basé sur les disques directement attachés. Ces disques sont montés sur la machine virtuelle Azure, et sont basés sur le stockage d’objets blob de pages Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou sur des disques managés (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Tous les types de lecteurs réseau ou de partages distants tels que les services de fichiers Azure suivants ne sont **PAS** pris en charge pour les fichiers de base de données : 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Si vous utilisez des disques basés sur le stockage d’objets blob de pages Azure ou sur des disques managés, les instructions figurant dans le chapitre [Structure d’un déploiement SGBDR][dbms-guide-2] de ce document s’appliquent également aux déploiements avec la base de données IBM DB2 pour LUW. 

Comme expliqué précédemment dans la partie générale du document, des quotas existent en ce qui concerne le débit d’E/S par seconde pour les disques. Les quotas exacts dépendent du type de machine virtuelle utilisé. La liste des types de machines virtuelles et de leurs quotas est disponible [ici (pour Linux)][virtual-machines-sizes-linux] et [ici (pour Windows)][virtual-machines-sizes-windows].

Tant que le quota actuel d’E/S par seconde par disque est suffisant, il est possible de stocker tous les fichiers de base de données sur un seul disque monté. 

Pour en savoir plus sur les considérations relatives aux performances, consultez également, dans les guides d’installation SAP, le chapitre portant sur la sécurité des données et les considérations sur les performances pour les répertoires de base de données.

Vous pouvez également utiliser des pools de stockage Windows (fonctionnalité uniquement disponible dans Windows Server 2012 et versions ultérieures) ou l’agrégation Windows pour Windows 2008 R2, comme le décrit le chapitre [RAID logiciel][dbms-guide-2.2] de ce document, afin de créer une unité logique volumineuse sur plusieurs disques montés.
Pour les disques contenant les chemins d’accès de stockage DB2 pour vos données SAP et répertoires SAPTMP, vous devez spécifier une taille de secteur de disque physique de 512 Ko. Quand vous utilisez des pools de stockage Windows, vous devez créer les pools de stockage manuellement par le biais de l’interface de ligne de commande en utilisant le paramètre `-LogicalSectorSizeDefault`. Pour plus d’informations, consultez <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Sauvegarde/restauration
La fonctionnalité de sauvegarde/restauration pour IBM DB2 pour LUW est prise en charge de la même façon que sur les systèmes d’exploitation Windows Server et Hyper-V standard.

Vous devez vous assurer que vous disposez d’une stratégie de sauvegarde de base de données valide en place. 

À l’instar des déploiements sur système nu, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit global de lecture est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (répertoires d’agrégation, disques) d’écriture de la sauvegarde, plus le débit est faible

Pour augmenter le nombre de cibles d’écriture, il est possible d’utiliser/de combiner deux options selon vos besoins :

* Agréger le volume cible de sauvegarde sur plusieurs disques, afin d’améliorer le débit d’E/S par seconde sur ce volume agrégé par bandes
* Utiliser plusieurs répertoires cible d’écriture de la sauvegarde

#### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence
Microsoft Cluster Server (MSCS) n’est pas pris en charge.

La fonction HADR (haute disponibilité et récupération d’urgence) DB2 est prise en charge. Si les machines virtuelles de la configuration haute disponibilité disposent de la résolution de noms de travail, l’installation dans Azure ne diffère pas d’une installation effectuée en local. Il est déconseillé de se fier uniquement à la résolution IP.

N’utilisez pas la géoréplication pour les comptes de stockage qui stockent les disques de base de données. Pour plus d’informations, consultez les chapitres [Stockage Microsoft Azure][dbms-guide-2.3] et [Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure][dbms-guide-3].

#### <a name="other"></a>Autres
Tous les autres sujets généraux, notamment les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent à IBM DB2 pour LUW, comme décrit dans les trois premiers chapitres de ce document pour le déploiement de machines virtuelles. 

Voir également le chapitre [Résumé – SQL Server général pour SAP sur Azure][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Caractéristiques d’IBM DB2 pour LUW sous Linux
Avec Microsoft Azure, vous pouvez facilement migrer votre application SAP existante exécutée sur IBM DB2 pour Linux, UNIX et Windows (LUW) vers les machines virtuelles Azure. Avec SAP sur IBM DB2 pour LUW, les administrateurs et les développeurs peuvent utiliser les outils de développement et d’administration disponibles en local. Des informations générales sur l’exécution de SAP Business Suite sur IBM DB2 pour LUW sont disponibles dans SAP Community Network (SCN) à l’adresse suivante : <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Pour des informations et des mises à jour supplémentaires de SAP sur DB2 pour LUW sur Azure, voir la Note de SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Prise en charge des versions IBM DB2 pour Linux, UNIX et Windows
SAP sur IBM DB2 pour LUW est pris en charge sur les services de machines virtuelles Microsoft Azure à compter de la version DB2 10.5.

Pour plus d’informations sur les produits SAP et les types de machines virtuelles Azure pris en charge, consultez la note SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Instructions de configuration IBM DB2 pour Linux, UNIX et Windows pour les installations SAP sur des machines virtuelles Azure
#### <a name="storage-configuration"></a>Configuration du stockage
Tous les fichiers de base de données doivent être stockés sur un système de fichiers basé sur les disques directement attachés. Ces disques sont montés sur la machine virtuelle Azure, et sont basés sur le stockage d’objets blob de pages Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou sur des disques managés (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Tous les types de lecteurs réseau ou de partages distants tels que les services de fichiers Azure suivants ne sont **PAS** pris en charge pour les fichiers de base de données :

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Si vous utilisez des disques basés sur le stockage d’objets blob de pages Azure, les instructions figurant dans le chapitre [Structure d’un déploiement SGBDR][dbms-guide-2] de ce document s’appliquent également aux déploiements avec la base de données IBM DB2 pour LUW.

Comme expliqué précédemment dans la partie générale du document, des quotas existent en ce qui concerne le débit d’E/S par seconde pour les disques. Les quotas exacts dépendent du type de machine virtuelle utilisé. La liste des types de machines virtuelles et de leurs quotas est disponible [ici (pour Linux)][virtual-machines-sizes-linux] et [ici (pour Windows)][virtual-machines-sizes-windows].

Tant que le quota actuel d’E/S par seconde par disque est suffisant, il est possible de stocker tous les fichiers de base de données sur un seul disque.

Pour en savoir plus sur les considérations relatives aux performances, consultez également, dans les guides d’installation SAP, le chapitre portant sur la sécurité des données et les considérations sur les performances pour les répertoires de base de données.

Vous pouvez également utiliser LVM (Logical Volume Manager, gestionnaire de volumes logiques) ou MDADM comme le décrit le chapitre [RAID logiciel][dbms-guide-2.2] de ce document pour créer une seule unité logique volumineuse sur plusieurs disques.
Pour les disques contenant les chemins d’accès de stockage DB2 pour vos données SAP et répertoires SAPTMP, vous devez spécifier une taille de secteur de disque physique de 512 Ko.

#### <a name="backuprestore"></a>Sauvegarde/restauration
La fonctionnalité de sauvegarde/restauration pour IBM DB2 pour LUW est prise en charge de la même façon que sur l’installation Linux locale standard.

Vous devez vous assurer que vous disposez d’une stratégie de sauvegarde de base de données valide en place.

À l’instar des déploiements sur système nu, les performances de sauvegarde/restauration dépendent du nombre de volumes pouvant être lus en parallèle et du débit éventuel de ces volumes. En outre, la consommation d’UC par la compression de sauvegarde peut jouer un rôle significatif sur les machines virtuelles ayant jusqu’à huit threads d’UC. Par conséquent, on peut partir des hypothèses suivantes :

* Moins il y a de disques utilisés pour stocker les unités de base de données, plus le débit global de lecture est réduit
* Moins il y a de threads UC dans la machine virtuelle, plus l’impact de la compression de sauvegarde est grave
* Moins il y a de cibles (répertoires d’agrégation, disques) d’écriture de la sauvegarde, plus le débit est faible

Pour augmenter le nombre de cibles d’écriture, il est possible d’utiliser/de combiner deux options selon vos besoins :

* Agréger le volume cible de sauvegarde sur plusieurs disques, afin d’améliorer le débit d’E/S par seconde sur ce volume agrégé par bandes
* Utiliser plusieurs répertoires cible d’écriture de la sauvegarde

#### <a name="high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence
La fonction HADR (haute disponibilité et récupération d’urgence) DB2 est prise en charge. Si les machines virtuelles de la configuration haute disponibilité disposent de la résolution de noms de travail, l’installation dans Azure ne diffère pas d’une installation effectuée en local. Il est déconseillé de se fier uniquement à la résolution IP.

N’utilisez pas la géoréplication pour les comptes de stockage qui stockent les disques de base de données. Pour plus d’informations, consultez les chapitres [Stockage Microsoft Azure][dbms-guide-2.3] et [Haute disponibilité et récupération d’urgence avec les machines virtuelles Azure][dbms-guide-3].

#### <a name="other"></a>Autres
Tous les autres sujets généraux, notamment les groupes à haute disponibilité Azure ou la surveillance SAP, s’appliquent avec IBM DB2 pour LUW comme décrit dans les trois premiers chapitres dans ce document pour les déploiements de machines virtuelles.

Voir également le chapitre [Résumé – SQL Server général pour SAP sur Azure][dbms-guide-5.8].

