---
title: "Groupes de disponibilité SQL Server - Machines virtuelles Azure - Récupération d’urgence | Microsoft Docs"
description: "Cet article explique comment configurer un groupe de disponibilité SQL Server sur des machines virtuelles avec un réplica dans une autre région."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 84fa2e051c46e178e3e72709886babc8c3db7b9d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Configurer un groupe de disponibilité AlwaysOn sur des machines virtuelles Azure dans des emplacements différents

Cet article explique comment configurer un réplica de groupe de disponibilité SQL Server AlwaysOn sur des machines virtuelles Azure dans un emplacement Azure distant. Utilisez cette configuration pour prendre en charge la récupération d’urgence.

Cet article s’applique aux machines virtuelles Azure s’exécutant en mode Resource Manager.

L’illustration suivante montre un déploiement classique d’un groupe de disponibilité sur des machines virtuelles Azure :

   ![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

Dans ce déploiement, toutes les machines virtuelles sont dans une région Azure. Les réplicas de groupe de disponibilité peuvent avoir une validation synchrone avec basculement automatique sur SQL-1 et SQL-2. Pour générer cette architecture, consultez [Modèle de groupe de disponibilité ou didacticiel](virtual-machines-windows-portal-sql-availability-group-overview.md).

Cette architecture est vulnérable aux interruptions de service si la région Azure devient inaccessible. Pour résoudre cette vulnérabilité, ajoutez un réplica dans une autre région Azure. Le schéma suivant illustre l’aspect de la nouvelle architecture :

   ![Récupération d’urgence du groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Le diagramme précédent illustre une nouvelle machine virtuelle appelée SQL-3. SQL-3 se trouve dans une autre région Azure. SQL-3 est ajoutée au cluster de basculement Windows Server. SQL-3 peut héberger un réplica de groupe de disponibilité. Notez enfin que la région Azure de SQL-3 a un nouvel équilibrage de charge Azure.

>[!NOTE]
> Un groupe à haute disponibilité Azure est requis lorsque plusieurs machines virtuelles se trouvent dans la même région. Si une seule machine virtuelle se trouve dans la région, le groupe à haute disponibilité n’est pas nécessaire. Vous ne pouvez placer une machine virtuelle dans un groupe à haute disponibilité que lors de la création. Si la machine virtuelle se trouve déjà dans un groupe à haute disponibilité, vous pouvez ajouter une machine virtuelle à un réplica supplémentaire ultérieurement.

Dans cette architecture, le réplica dans la région distante est normalement configuré avec le mode de disponibilité à validation asynchrone et le mode de basculement manuel.

Lorsque les réplicas du groupe de disponibilité se trouvent sur des machines virtuelles Azure dans différentes régions Azure, chaque région requiert :

* Une passerelle de réseau virtuel
* Une connexion à la passerelle de réseau virtuel

Le schéma suivant montre comment les réseaux communiquent entre les centres de données.

   ![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Cette architecture facture les données sortantes des données répliquées entre des régions Azure. Consultez [Détails de la tarification de la bande passante](http://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Créer un réplica distant

Pour créer un réplica dans un centre de données distant, procédez comme suit :

1. [Créez un réseau virtuel dans la nouvelle région](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Configurez une connexion de réseau virtuel à réseau virtuel à l’aide du portail Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >Parfois, vous serez amené à utiliser PowerShell pour créer la connexion de réseau virtuel à réseau virtuel. Par exemple, si vous utilisez différents comptes Azure, vous ne pouvez pas configurer la connexion dans le portail. Dans ce cas, [configurez une connexion de réseau virtuel à réseau virtuel à l’aide du portail Azure](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Créez un contrôleur de domaine dans la nouvelle région](../../../active-directory/active-directory-new-forest-virtual-machine.md).

   Ce contrôleur de domaine assure l’authentification si le contrôleur de domaine dans le site principal n’est pas disponible.

1. [Créez une machine virtuelle SQL Server dans la nouvelle région](virtual-machines-windows-portal-sql-server-provision.md).

1. [Créez un équilibrage de charge Azure dans le réseau sur la nouvelle région](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

   Cet équilibrage de charge doit :

   - Être dans les mêmes réseau et sous-réseau que la nouvelle machine virtuelle.
   - Avoir une adresse IP statique pour l’écouteur du groupe de disponibilité.
   - Inclure un pool principal comprenant uniquement des machines virtuelles situées dans la même région que l’équilibrage de charge.
   - Utiliser une sonde de port TCP propre à l’adresse IP.
   - Avoir une règle d’équilibrage de charge propre à SQL Server dans la même région.  

1. [Ajoutez la fonction de Clustering avec basculement au nouveau serveur SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Joignez le nouveau SQL Server au domaine](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

1. [Configurez le nouveau compte de service SQL Server pour qu’il utilise un compte de domaine](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount).

1. [Ajoutez le nouveau SQL Server au cluster de basculement Windows Server](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode).

1. Créez une ressource à adresse IP sur le cluster.

   Vous pouvez créer la ressource à adresse IP dans le Gestionnaire du cluster de basculement. Cliquez avec le bouton droit sur le rôle du groupe de disponibilité, cliquez sur **Ajouter une ressource**, **More Resources (Plus de ressources)** puis **Adresse IP**.

   ![Création d’une adresse IP](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Configurez cette adresse IP comme suit :

   - Utilisez le réseau à partir du centre de données distant.
   - Attribuez l’adresse IP à partir du nouvel équilibrage de charge Azure. 

1. Sur le nouveau SQL Server dans le Gestionnaire de configuration SQL Server, [activez les groupes de disponibilité AlwaysOn](http://msdn.microsoft.com/library/ff878259.aspx).

1. [Ouvrez les ports de pare-feu sur le nouveau SQL Server](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

   Les numéros de port que vous devez ouvrir varient selon votre environnement. Ouvrez les ports du point de terminaison de la mise en miroir et de la sonde d’intégrité d’Azure Load Balancer.

1. [Ajoutez un réplica au groupe de disponibilité sur le nouveau SQL Server](http://msdn.microsoft.com/library/hh213239.aspx).

   Pour un réplica dans une région Azure distante, configurez-le pour une réplication asynchrone avec basculement manuel.  

1. Ajoutez la ressource à adresse IP comme une dépendance du cluster (nom de réseau) du point d’accès au client écouteur.

   La capture d’écran suivante illustre une ressource de cluster à adresse IP configurée correctement :

   ![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Le groupe de ressources de cluster inclut les deux adresses IP. Ces deux adresses IP sont des dépendances du point d’accès au client écouteur. Utilisez l’opérateur **OR** dans la configuration de dépendance de cluster.

1. [Définissez les paramètres de cluster dans PowerShell](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Exécutez le script PowerShell avec le nom réseau du cluster, une adresse IP et un port de sonde que vous avez configurés sur l’équilibrage de charge dans la nouvelle région.

   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Configurer la connexion à plusieurs sous-réseaux

Le réplica dans le centre de données distant fait partie du groupe de disponibilité, mais il se trouve dans un autre sous-réseau. Si ce réplica devient le réplica principal, la connexion d’application peut être perturbée. Ce comportement est identique à un groupe de disponibilité local dans un déploiement de plusieurs sous-réseaux. Pour autoriser les connexions des applications clientes, mettez à jour la connexion cliente ou configurez la mise en cache de la résolution du nom sur la ressource de nom de réseau de cluster.

Idéalement, mettez à jour les chaînes de connexion au client pour qu’elles indiquent `MultiSubnetFailover=Yes`. Consultez [Connexion à MultiSubnetFailover](http://msdn.microsoft.com/library/gg471494#Anchor_0).

Si vous ne pouvez pas modifier les chaînes de connexion, vous pouvez configurer la mise en cache de la résolution des noms. Consultez [Connection Timeouts in Multi-subnet Availability Group (Délais d’expiration de la connexion dans le groupe de disponibilité de plusieurs sous-réseaux)](http://blogs.msdn.microsoft.com/alwaysonpro/2014/06/03/connection-timeouts-in-multi-subnet-availability-group/).

## <a name="fail-over-to-remote-region"></a>Basculer vers la région distante

Pour tester la connectivité de l’écouteur à la région distante, vous pouvez basculer le réplica vers la région distante. Si le réplica est asynchrone, le basculement est vulnérable à la perte potentielle de données. Pour effectuer un basculement sans perte de données, modifiez le mode de disponibilité en synchrone et définissez le mode de basculement automatique. Procédez comme suit :

1. Dans l’**Explorateur d’objets**, connectez-vous à l’instance de SQL Server qui héberge le réplica principal.
1. Sous **Groupes de disponibilité AlwaysOn**, **Groupes de disponibilité**, cliquez avec le bouton droit sur votre groupe de disponibilité, puis cliquez sur **Propriétés**.
1. Dans la page **Général**, sous **Réplicas de disponibilité**, configurez le réplica secondaire dans le site de récupération d’urgence pour qu’il utilise le mode de disponibilité **Validation synchrone** et le mode de basculement **Automatique**.
1. Si vous avez un réplica secondaire dans le même site que le réplica principal pour la haute disponibilité, configurez ce réplica en **Validation asynchrone** et **Manuel**.
1. Cliquez sur OK.
1. Dans l’**Explorateur d’objets**, cliquez sur le groupe de disponibilité puis sur **Afficher le tableau de bord**.
1. Dans le tableau de bord, vérifiez que le réplica sur le site de récupération d’urgence est synchronisé.
1. Dans l’**Explorateur d’objets**, cliquez sur le groupe de disponibilité puis sur **Basculer...**. SQL Server Management Studio ouvre un assistant pour effectuer le basculement vers SQL Server.  
1. Cliquez sur **Suivant** et sélectionnez l’instance de SQL Server sur le site de récupération d’urgence. Cliquez à nouveau sur **Suivant** .
1. Connectez-vous à l’instance de SQL Server sur le site de récupération d’urgence et cliquez sur **Suivant**.
1. Dans la page **Synthèse**, vérifiez les paramètres et cliquez sur **Terminer**.

Après avoir testé la connectivité, replacez le réplica principal dans votre centre de données principal et rétablissez les paramètres de fonctionnement normaux du mode de disponibilité. Le tableau suivant présente les paramètres de fonctionnement normaux de l’architecture décrite dans ce document :

| Lieu | Instance de serveur | Rôle | Mode de disponibilité | Mode de basculement
| ----- | ----- | ----- | ----- | -----
| Centre de données principal | SQL-1 | Primaire | Synchrone | Automatique
| Centre de données principal | SQL-2 | Secondaire | Synchrone | Automatique
| Centre de données secondaire ou distant | SQL-3 | Secondaire | Asynchrone | Manuel


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Plus d’informations sur le basculement manuel forcé et planifié

Pour plus d’informations, consultez les rubriques suivantes :

- [Effectuer un basculement manuel planifié d'un groupe de disponibilité (SQL Server)](http://msdn.microsoft.com/library/hh231018.aspx)
- [Effectuer un basculement manuel forcé d'un groupe de disponibilité (SQL Server)](http://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Liens supplémentaires

* [Groupes de disponibilité AlwaysOn](http://msdn.microsoft.com/library/hh510230.aspx)
* [Azure Virtual Machines](http://docs.microsoft.com/azure/virtual-machines/windows/)
* [Équilibrages de charge Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Groupes à haute disponibilité Azure](../manage-availability.md)
