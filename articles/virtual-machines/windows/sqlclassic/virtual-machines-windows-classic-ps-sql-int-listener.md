---
title: Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure | Microsoft Docs
description: Ce didacticiel utilise des ressources créées avec le modèle de déploiement Classic, et crée un écouteur de groupe de disponibilité Always On dans Azure utilisant un équilibreur de charge interne.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0466265ad5a24e8ea6dc5079e2b4006d74e7dde0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité Always On dans Azure
> [!div class="op_single_selector"]
> * [Écouteur interne](../classic/ps-sql-int-listener.md)
> * [Écouteur externe](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Vue d'ensemble

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager et classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, nous recommandons d’utiliser le modèle Resource Manager.

Afin de configurer un écouteur pour un groupe de disponibilité Always On dans le modèle Resource Manager, voir [Configurer un équilibreur de charge pour un groupe de disponibilité Always On dans Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

Votre groupe de disponibilité peut contenir des réplicas locaux uniquement, Azure uniquement, ou locaux et Azure pour les configurations hybrides. Les réplicas Azure peuvent se trouver dans une même région ou dans plusieurs régions qui utilisent plusieurs réseaux virtuels. Les procédures suivantes de cet article supposent que vous avez déjà [configuré un groupe de disponibilité](../classic/portal-sql-alwayson-availability-groups.md), mais pas un écouteur.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Instructions et limitations pour les écouteurs internes
L’utilisation d’un équilibreur de charge interne (ILB) avec un écouteur de groupe de disponibilité dans Azure doit respecter les conditions suivantes :

* L'écouteur du groupe de disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.
* Un seul écouteur du groupe de disponibilité interne est pris en charge pour chaque service cloud, car l'écouteur est configuré sur l'équilibreur de charge interne, et il n’y a qu’un seul équilibreur de charge interne pour chaque service cloud. Toutefois, il est possible de créer plusieurs écouteurs externes. Pour plus d’informations, voir [Configurer un écouteur externe pour les groupes de disponibilité Always On dans Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Déterminer l'accessibilité de l'écouteur
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un écouteur qui utilise un équilibreur de charge interne. Si vous avez besoin d’un écouteur public ou externe, consultez la version de cet article qui explique comment configurer un [écouteur externe](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Créer des points de terminaison de machine virtuelle à charge équilibrée avec retour direct du serveur
Vous créez tout d’abord un équilibreur de charge interne en exécutant le script ultérieurement dans cette section.

Créez un point de terminaison avec équilibrage de charge pour chaque machine virtuelle qui héberge un réplica Azure. Si vous avez des réplicas dans plusieurs régions, chaque réplica de cette région doit se trouver dans le même service cloud sur le même réseau virtuel Azure. La création de réplicas de groupe de disponibilité couvrant plusieurs régions Azure nécessite de configurer plusieurs réseaux virtuels. Pour plus d’informations sur la configuration d’une connexion entre des réseaux virtuels, voir [Configurer une connexion de réseau virtuel à réseau virtuel](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Dans le portail Azure, accédez à chaque machine virtuelle qui héberge un réplica pour afficher les détails.

2. Cliquez sur l'onglet **Points de terminaison** de chacune machine virtuelle.

3. Vérifiez que le **Nom** et le **Port public** du point de terminaison de l’écouteur ne sont pas déjà utilisés. Dans l'exemple de cette section, le nom est *MyEndpoint* et le port est *1433*.

4. Sur votre client local, téléchargez et installez [le dernier module PowerShell](https://azure.microsoft.com/downloads/).

5. Lancez Azure PowerShell.  
    Une nouvelle session PowerShell s'ouvre avec les modules d'administration Azure chargés.

6. Exécutez `Get-AzurePublishSettingsFile`. Cette applet de commande vous dirige vers un navigateur de façon à télécharger un fichier de paramètres de publication dans un répertoire local. Vous serez peut-être invité à entrer les informations d'identification de connexion de votre abonnement Azure.

7. Exécutez la commande `Import-AzurePublishSettingsFile` suivante avec le chemin d'accès du fichier de paramètres de publication que vous avez téléchargé :

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    Une fois le fichier de paramètres de publication importé, vous pouvez gérer votre abonnement Azure dans la session PowerShell.

8. Affectez une adresse IP statique pour l’*équilibrage de charge interne*. Examinez la configuration actuelle du réseau virtuel en exécutant la commande suivante :

        (Get-AzureVNetConfig).XMLConfiguration
9. Notez le nom *Subnet* du sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Ce nom sera utilisé dans le paramètre $SubnetName du script.

10. Notez le nom *VirtualNetworkSite* et la valeur *AddressPrefix* de début pour le sous-réseau qui contient les machines virtuelles qui hébergent les réplicas. Recherchez ensuite une adresse IP disponible en transférant les deux valeurs à la commande `Test-AzureStaticVNetIP` et en examinant la valeur *AvailableAddresses*. Par exemple, si le réseau virtuel est nommé *MyVNet* et a une plage d’adresses de sous-réseau démarrant avec *172.16.0.128*, la commande suivante répertorie les adresses disponibles :

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Sélectionnez l’une des adresses disponibles et utilisez-la dans le paramètre $ILBStaticIP du script à l’étape suivante.

12. Copiez le script PowerShell suivant dans un éditeur de texte puis définissez les valeurs des variables en fonction de votre environnement. Les valeurs par défaut ont été fournies pour certains paramètres.  

    Les déploiements existants qui utilisent des groupes d'affinités ne peuvent pas ajouter un équilibrage de charge interne. Pour plus d’informations sur les exigences liées à l’équilibreur de charge interne, consultez la rubrique [Présentation de l’équilibrage de charge interne](../../../load-balancer/load-balancer-internal-overview.md).

    Notez que, si votre groupe de disponibilité s'étend sur plusieurs régions Azure, vous devez exécuter le script une fois dans chaque centre de données pour le service cloud et les nœuds qui se trouvent dans ce centre de données.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Après avoir défini les variables, copiez le script de l'éditeur de texte dans votre session PowerShell pour l'exécuter. Si l'invite affiche **>>**, appuyez de nouveau sur Entrée pour vous assurer que le script s'exécute.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Vérifiez que KB2854082 est installé le cas échéant
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Ouvrez les ports de pare-feu dans des nœuds de groupe de disponibilité
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Créer l'écouteur de groupe de disponibilité

Créez l’écouteur de groupe de disponibilité en deux étapes. Tout d’abord, créez la ressource de cluster de point d’accès client et configurez les dépendances. Configurez ensuite les ressources de cluster dans PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Créez le point d’accès client et configurez les dépendances de cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurez les ressources de cluster dans PowerShell
1. Pour l'équilibreur de charge interne, vous devez utiliser son adresse IP créée précédemment. Pour obtenir cette adresse IP dans PowerShell, utilisez le script suivant :

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Sur l’une des machines virtuelles, copiez le script PowerShell pour votre système d’exploitation dans un éditeur de texte, puis définissez les variables sur les valeurs notées précédemment.

    Pour Windows Server 2012 ou version ultérieure, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Pour Windows Server 2008 R2, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Une fois les variables définies, ouvrez une fenêtre Windows PowerShell avec élévation de privilèges, puis collez le script de l'éditeur de texte dans votre session Azure PowerShell pour l'exécuter. Si l'invite affiche **>>**, appuyez de nouveau sur Entrée pour vous assurer que le script s'exécute.

4. Répétez les étapes précédentes pour chaque machine virtuelle.  
    Ce script configure la ressource Adresse IP avec l'adresse IP du service cloud et définit d'autres paramètres, tels que le port de la sonde. Lorsque la ressource d'adresse IP est mise en ligne, elle peut ensuite répondre à l'interrogation sur le port de la sonde du point de terminaison à équilibrage de charge créé précédemment.

## <a name="bring-the-listener-online"></a>Mettre l'écouteur en ligne
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Éléments de suivi
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Tester l'écouteur du groupe de disponibilité (au sein du même réseau virtuel)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
