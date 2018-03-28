---
title: Script PowerShell pour déployer des clusters HPC Linux | Microsoft Docs
description: Exécuter un script PowerShell pour déployer un cluster Linux HPC Pack 2012 R2 sur les machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 477859db8c884714d9b5140483d300ebfe6c116a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Créer un cluster de calcul haute performance (HPC) Linux avec le script de déploiement du HPC Pack IaaS
Exécutez le script PowerShell de déploiement du HPC Pack IaaS pour déployer un cluster HPC Pack 2012 R2 complet pour les charges de travail Linux sur les machines virtuelles Azure. Le cluster se compose d’un nœud principal joint à Active Directory, exécutant Windows Server et Microsoft HPC Pack, et de nœuds de calcul qui exécutent l’une des distributions Linux prises en charge par HPC Pack. Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Windows, consultez [Créer un cluster HPC Windows avec le script de déploiement du HPC Pack IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
> [!IMPORTANT] 
> Le script PowerShell décrit dans cet article crée un cluster Microsoft HPC Pack 2012 R2 dans Azure à l’aide du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> En outre, le script décrit dans cet article ne prend pas en charge HPC Pack 2016. Pour plus d’informations sur les modèles Resource Manager pour HPC Pack 2012 R2 et HPC Pack 2016, consultez les [options de déploiement de cluster HPC Pack dans Azure](../hpcpack-cluster-options.md).


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Exemple de fichier de configuration
Le fichier de configuration suivant crée un contrôleur de domaine et une forêt de domaines, et déploie un cluster HPC Pack composé d’un nœud principal avec des bases de données locales et de 10 nœuds de calcul Linux. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul Linux sont créés dans deux services cloud et deux comptes de stockage (c’est-à-dire *MyLnxCN-0001* à *MyLnxCN-0005* dans *MyLnxCNService01* et *mylnxstorage01*, et *MyLnxCN-0006* à *MyLnxCN-0010* dans *MyLnxCNService02* et *mylnxstorage02*). Les nœuds de calcul sont créés à partir d’une image Linux OpenLogic CentOS version 7.0. 

Utilisez vos propres valeurs pour votre nom d’abonnement et les noms de compte et de service.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Résolution de problèmes
* **Erreur « Le réseau virtuel n’existe pas »**. Si vous exécutez le script de déploiement HPC Pack IaaS pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ».
  Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.
* **Problème d’accès à Internet à partir du réseau virtuel Azure**. Si vous créez un cluster HPC Pack avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement une machine virtuelle de nœud principal en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles à Internet sur le réseau virtuel Azure. Cela peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.
  
    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour ce faire, dans le Gestionnaire de serveur, cliquez sur **Outils** > **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.

## <a name="next-steps"></a>Étapes suivantes
* Consultez la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](hpcpack-cluster.md) pour plus d’informations sur les distributions Linux prises en charge, le déplacement des données et la soumission de tâches à un cluster HPC Pack avec des nœuds de calcul Linux.
* Pour accéder à des didacticiels qui utilisent le script pour créer un cluster et exécuter une charge de travail HPC Linux, consultez :
  * [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](hpcpack-cluster-namd.md)
  * [Exécution d’OpenFOAM avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](hpcpack-cluster-openfoam.md)
  * [Exécution de STAR-CCM+ avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](hpcpack-cluster-starccm.md)

