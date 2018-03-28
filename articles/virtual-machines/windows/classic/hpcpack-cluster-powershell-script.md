---
title: Script PowerShell pour déployer des clusters Windows HPC | Microsoft Docs
description: Exécuter un script PowerShell pour déployer un cluster Windows HPC Pack 2012 R2 sur les machines virtuelles Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 82aa2942a6a4fe6a3ac1c3ec2c0710e39f4282b1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Créer un cluster de calcul haute performance (HPC) Windows avec le script de déploiement du HPC Pack IaaS
Exécutez le script PowerShell de déploiement du HPC Pack IaaS pour déployer un cluster HPC Pack 2012 R2 complet pour les charges de travail Windows sur les machines virtuelles Azure. Le cluster se compose d’un nœud principal joint à Active Directory, exécutant Windows Server et Microsoft HPC Pack, et de ressources de calcul Windows supplémentaires que vous spécifiez. Si vous souhaitez déployer un cluster HPC Pack dans Azure pour les charges de travail Linux, consultez [Créer un cluster HPC Linux avec le script de déploiement du HPC Pack IaaS](../../linux/classic/hpcpack-cluster-powershell-script.md). 

> [!IMPORTANT] 
> Le script PowerShell décrit dans cet article crée un cluster Microsoft HPC Pack 2012 R2 dans Azure à l’aide du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> En outre, le script décrit dans cet article ne prend pas en charge HPC Pack 2016. Pour plus d’informations sur les modèles Resource Manager pour HPC Pack 2012 R2 et HPC Pack 2016, consultez les [options de déploiement de cluster HPC Pack dans Azure](../hpcpack-cluster-options.md).

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Exemples de fichiers de configuration
Dans les exemples suivants, utilisez vos propres valeurs pour votre ID ou nom d’abonnement et les noms de compte et de service.

### <a name="example-1"></a>Exemple 1
Le fichier de configuration suivant déploie un cluster HPC Pack qui possède un nœud principal avec des bases de données locales et cinq nœuds de calcul exécutant le système d’exploitation Windows Server 2012 R2. Tous les services cloud sont créés directement dans l’emplacement « États-Unis de l’Ouest ». Le nœud principal agit en tant que contrôleur de domaine de la forêt de domaines.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemple 2
Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster possède 1 nœud principal avec des bases de données locales et 12 nœuds de calcul avec l’extension de machine virtuelle BGInfo appliquée.
L’installation automatique des mises à jour Windows est désactivée pour toutes les machines virtuelles dans la forêt de domaines. Tous les services cloud sont créés directement dans l’emplacement East Asia. Les nœuds de calcul sont créés dans trois services cloud et trois comptes de stockage (c’est-à-dire, *MyHPCCN-0001* à *MyHPCCN-0005* dans *MyHPCCNService01* et *mycnstorage01* ; *MyHPCCN-0006* à *MyHPCCN0010* dans *MyHPCCNService02* et *mycnstorage02* ; et *MyHPCCN-0011* à *MyHPCCN-0012* dans *MyHPCCNService03* et *mycnstorage03*). Les nœuds de calcul sont créés à partir d’une image privée existante capturée depuis un nœud de calcul. Le service d’agrandissement et de réduction automatiques est activé avec des intervalles d’agrandissement et de réduction par défaut.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemple 3
Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster contient un nœud principal, un serveur de base de données avec un disque de données de 500 Go, deux nœuds de répartiteur exécutant le système d’exploitation Windows Server 2012 R2 et cinq nœuds de calcul exécutant le système d’exploitation Windows Server 2012 R2. Le service cloud MyHPCCNService est créé dans le groupe d’affinités *MyIBAffinityGroup*. Les autres services cloud sont créés dans le groupe d’affinités *MyAffinityGroup*. L’API REST du planificateur de travaux HPC et le portail web HPC sont activés sur le nœud principal.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemple 4
Le fichier de configuration suivant déploie un cluster HPC Pack dans une forêt de domaines existante. Le cluster présente 2 nœuds principaux avec des bases de données locales, 2 modèles de nœud Azure sont créés et 3 nœuds Azure de taille moyenne sont créés pour le modèle de nœud Azure *AzureTemplate1*. Un fichier de script s’exécute sur le nœud principal après la configuration de ce dernier.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Résolution de problèmes
* **Erreur « Le réseau virtuel n’existe pas »** : si vous exécutez le script pour déployer plusieurs clusters dans Azure simultanément sous un même abonnement, un ou plusieurs déploiements peuvent échouer avec l’erreur « Le réseau virtuel *nom\_VNet* n’existe pas ».
  Si cette erreur se produit, réexécutez le script de déploiement qui a échoué.
* **Problème d’accès à Internet à partir du réseau virtuel Azure** : si vous créez un cluster avec un nouveau contrôleur de domaine en utilisant le script de déploiement, ou si vous promouvez manuellement une machine virtuelle de nœud principal en contrôleur de domaine, vous pouvez rencontrer des problèmes de connexion des machines virtuelles à Internet. Ce problème peut se produire si un serveur DNS redirecteur est automatiquement configuré sur le contrôleur de domaine et si ce serveur DNS redirecteur ne se résout pas correctement.
  
    Pour contourner ce problème, ouvrez une session sur le contrôleur de domaine et supprimez le paramètre de configuration du redirecteur ou configurez un serveur DNS redirecteur valide. Pour configurer ce paramètre, dans le Gestionnaire de serveur, cliquez sur **Outils** >
    **DNS** pour ouvrir le Gestionnaire DNS, puis double-cliquez sur **Redirecteurs**.
* **Problèmes d’accès au réseau RDMA à partir de machines virtuelles nécessitant beaucoup de ressources système** : si vous ajoutez des machines virtuelles de calcul ou à nœud de répartiteur Windows Server utilisant une taille compatible RDMA, A8 ou A9 par exemple, vous pouvez rencontrer des problèmes pour la connexion de ces machines virtuelles au réseau d’application RDMA. Cela peut se produire si l’extension HpcVmDrivers n’est pas installée correctement lorsque les machines virtuelles sont ajoutées au cluster. Par exemple, l’extension peut être bloquée à l’état d’installation.
  
    Pour contourner ce problème, vérifiez tout d’abord l’état de l’extension dans les machines virtuelles. Si l’extension n’est pas installée correctement, essayez de supprimer les nœuds de cluster HPC, puis ajoutez de nouveau les nœuds. Par exemple, vous pouvez ajouter des machines virtuelles à nœud de calcul en exécutant le script Add-HpcIaaSNode.ps1 sur le nœud principal.

## <a name="next-steps"></a>Étapes suivantes
* Essayez d’exécuter une charge de travail test sur le cluster. Pour obtenir un exemple, consultez le [guide de mise en route](https://technet.microsoft.com/library/jj884144)du HPC Pack.
* Pour accéder à un didacticiel qui utilise un script de déploiement de cluster et exécute une charge de travail HPC, consultez [Prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Essayez les outils de HPC Pack pour démarrer, arrêter, ajouter et supprimer des nœuds de calcul à partir d’un cluster que vous créez. Consultez [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](hpcpack-cluster-node-manage.md).
* Pour se préparer à soumettre des travaux au cluster à partir d’un ordinateur local, consultez [Envoyer des travaux HPC à partir d’un ordinateur local vers un cluster HPC Pack dans Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

