---
title: "Résoudre les problèmes relatifs aux groupes de sécurité réseau - PowerShell | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes liés aux groupes de sécurité réseau dans le modèle de déploiement Azure Resource Manager à l’aide d’Azure PowerShell."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 4c732bb7-5cb1-40af-9e6d-a2a307c2a9c4
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: d556f2d6d37956c3b3bca2a2905b2c947e6be0df
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Résoudre les groupes de sécurité réseau à l’aide d’Azure PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure](virtual-network-nsg-troubleshoot-portal.md)
> * [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
> 
> 

Si vous avez configuré des groupes de sécurité réseau sur votre machine virtuelle et rencontrez des problèmes de connectivité de machine virtuelle, cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour les groupes de sécurité réseau afin de vous aider à dépanner.

Les groupes de sécurité réseau vous permettent de contrôler les types de trafic transitant sur vos machines virtuelles. Des groupes de sécurité réseau peuvent être appliqués à des sous-réseaux d’un réseau virtuel Azure (VNet), à des cartes réseau ou aux deux. Les règles effectives appliquées à une carte réseau sont une agrégation de règles existant dans les groupes de sécurité réseau appliqués à une carte réseau et au sous-réseau auquel elle est connectée. Les règles appliquées à ces groupes de sécurité réseau sont parfois en conflit entre elles et peuvent avoir une incidence sur la connectivité réseau d’une machine virtuelle.  

Vous pouvez afficher toutes les règles de sécurité effectives de vos groupes de sécurité réseau, telles qu’appliquées aux cartes réseau de votre machine virtuelle. Cet article explique comment résoudre les problèmes de connectivité de machine virtuelle à l’aide de ces règles dans le modèle de déploiement Azure Resource Manager. Si vous n’êtes pas familiarisé avec les concepts de réseau virtuel et de groupe de sécurité réseau, lisez les articles de présentation [Réseau virtuel](virtual-networks-overview.md) et [Groupes de sécurité réseau](virtual-networks-nsg.md).

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Utilisation de règles de sécurité effectives pour résoudre des problèmes de flux de trafic de machine virtuelle
Le scénario qui suit illustre un problème de connexion courant :

Une machine virtuelle nommée *VM1* fait partie d’un sous-réseau nommé *Subnet1* au sein d’un réseau virtuel nommé *WestUS-VNet1*. Une tentative de connexion à la machine virtuelle à l’aide du protocole RDP sur le port TCP 3389 a échoué. Des groupes de sécurité réseau sont appliqués tant à la carte réseau *VM1-NIC1* qu’au sous-réseau *Subnet1*. Le trafic vers le port TCP 3389 est autorisé dans le groupe de sécurité réseau associé à l’interface réseau *VM1-NIC1*. Toutefois, le test Ping du port TCP 3389 de VM1 échoue.

Bien que cet exemple utilise le port TCP 3389, les étapes suivantes permettent de déterminer les échecs de connexion entrante et sortante sur n’importe quel port.

## <a name="detailed-troubleshooting-steps"></a>Étapes de dépannage détaillées
Pour dépanner des groupes de sécurité réseau pour une machine virtuelle, procédez comme suit :

1. Démarrez une session Azure PowerShell et connectez-vous à Azure. Si vous n’êtes pas familiarisé avec l’utilisation d’Azure PowerShell, lisez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) . L’opération *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* doit être affectée à votre compte pour l’interface réseau. Pour savoir comment affecter des opérations à des comptes, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Entrez la commande suivante pour renvoyer toutes les règles du groupe de sécurité réseau appliquées à une carte d’interface réseau nommée *VM1-NIC1* dans le groupe de ressources *RG1* :
   
        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Si vous ne connaissez pas le nom d’une carte d’interface réseau, entrez la commande suivante pour récupérer les noms de toutes les cartes d’interface réseau d’un groupe de ressources : 
   > 
   > `Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`
   > 
   > 
   
    Le texte suivant est un exemple de sortie de règles effectives retournée pour la carte d’interface réseau *VM1-NIC1* :
   
        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
   
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]
   
    Notez les informations suivantes dans la sortie :
   
   * Il existe deux sections **NetworkSecurityGroup** : l’une est associée à un sous-réseau (*Subnet1*), et l’autre à une carte d’interface réseau (*VM1-NIC1*). Dans cet exemple, un groupe de sécurité réseau a été appliquée à chacune d’elles.
   * **Association** montre les ressources (carte d’interface réseau ou sous-réseau) auxquelles un groupe de sécurité réseau donné est associé. Si la ressource de groupe de sécurité réseau est déplacée/dissociée immédiatement avant l’exécution de cette commande, il se peut que vous deviez attendre quelques secondes pour que la modification apparaisse dans la sortie de la commande. 
   * Les noms de règle sont précédés de *defaultSecurityRules*: lors de la création d’un groupe de sécurité réseau, plusieurs règles de sécurité par défaut sont créées à l’intérieur de celui-ci. Vous ne pouvez pas supprimer des règles par défaut, mais vous pouvez les remplacer par des règles de priorité plus élevée.
     Pour en savoir plus sur les règles de sécurité par défaut de groupe de sécurité réseau , voir l’article [Vue d’ensemble de groupe de sécurité réseau](virtual-networks-nsg.md#default-rules) .
   * **ExpandedAddressPrefix** développe les préfixes d’adresse pour les balises par défaut de groupe de sécurité réseau. Les balises représentent plusieurs préfixes d’adresse. L’expansion des balises peut être utile lors de la résolution de problèmes de connectivité de machine virtuelle avec des préfixes d’adresse spécifiques. Par exemple, avec VNET Peering, la balise VIRTUAL_NETWORK se développe pour afficher les préfixes de réseau virtuel homologués dans la sortie précédente.
     
     > [!NOTE]
     > La commande affiche des règles effectives uniquement si un groupe de sécurité réseau est associé à un sous-réseau, à une carte d’interface réseau ou aux deux. Une machine virtuelle peut avoir plusieurs cartes d’interface réseau à laquelle différents groupes de sécurité réseau sont appliqués. Lors de la résolution du problème, exécutez la commande pour chaque carte d’interface réseau.
     > 
     > 
3. Pour faciliter le filtrage d’un nombre plus important de règles de groupe de sécurité réseau, entrez les commandes suivantes pour résoudre le problème : 
   
        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView
   
    Un filtre pour le trafic RDP (port TCP 3389) est appliqué à l’affichage grille, comme illustré dans l’image suivante :
   
    ![Liste des règles](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
4. Comme vous pouvez le voir dans l’affichage grille, il existe des règles d’autorisation et de refus pour RDP. La sortie de l’étape 2 montre que la règle *DenyRDP* figure dans le groupe de sécurité réseau appliqué au sous-réseau. Pour les règles de trafic entrant, les groupes de sécurité réseau appliqués au sous-réseau sont traités en premier. Si une correspondance est trouvée, le groupe de sécurité réseau appliqué à l’interface réseau n’est pas traité. Dans ce cas, la règle *DenyRDP* du sous-réseau bloque le RDP à la machine virtuelle (**VM1**).
   
   > [!NOTE]
   > Plusieurs cartes d’interface réseau peuvent être attachées à une machine virtuelle. Chacune peut être connectée à un sous-réseau différent. Étant donné que les commandes des étapes précédentes sont exécutées sur une carte d’interface réseau, il est important de spécifier la carte d’interface réseau avec laquelle l’échec de connectivité se produit. Si vous n’êtes pas sûr, vous pouvez toujours exécuter les commandes sur chaque carte d’interface réseau attachée à la machine virtuelle.
   > 
   > 
5. Pour le protocole RDP dans la machine virtuelle VM1, modifiez la règle *Refuser RDP (3389)* en *Autoriser RDP(3389)* dans le groupe de sécurité réseau **Subnet1-NSG**. Vérifiez que le port TCP 3389 est ouvert en ouvrant une connexion RDP à la machine virtuelle ou en utilisant l’outil PsPing. Pour en savoir plus sur PsPing, lire la [page de téléchargement de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)
   
    Vous pouvez supprimer des règles d’un groupe de sécurité réseau en utilisant les informations de la sortie de la commande suivante :
   
        Get-Help *-AzureRmNetworkSecurityRuleConfig

## <a name="considerations"></a>Considérations
Lors de la résolution de problèmes de connectivité, considérez les points suivants :

* Des règles de groupe de sécurité réseau par défaut bloquent l’accès entrant à partir d’internet et n’autorisent que le trafic entrant dans le réseau virtuel. Les règles doivent être ajoutées de façon explicite afin d’autoriser l’accès entrant à partir d’Internet, en fonction des besoins.
* Si aucune règle de sécurité du groupe de sécurité réseau n’est à l’origine d’un échec de connectivité réseau d’une machine virtuelle, les causes du problème peuvent être les suivantes :
  * Logiciel de pare-feu s’exécutant à l’intérieur du système d’exploitation de la machine virtuelle
  * Itinéraires configurés pour des appliances virtuelles ou un trafic local. Le trafic Internet peut être redirigé localement au moyen d’un tunneling forcé. Il se peut qu’une connexion RDP/SSH d’Internet à votre machine virtuelle ne fonctionne pas avec ce paramètre, selon la façon dont le matériel de réseau local gère ce trafic. Pour savoir comment diagnostiquer des problèmes d’itinéraire susceptibles d’entraver le flux de trafic échangé avec la machine virtuelle, consultez [Résolution des problèmes d’itinéraire](virtual-network-routes-troubleshoot-powershell.md) . 
* Si vous avez des réseaux virtuels homologues, par défaut, la balise VIRTUAL_NETWORK s’étend automatiquement pour inclure les préfixes des réseaux virtuels homologues. Pour résoudre des problèmes liés à la connectivité d’homologation de réseau virtuel, vous pouvez consulter ces préfixes dans la liste **ExpandedAddressPrefix** . 
* Les règles de sécurité effectives sont affichées uniquement s’il existe un groupe de sécurité réseau associé à la carte réseau et au sous-réseau de la machine virtuelle. 
* Si aucun groupe de sécurité réseau n’est associé à la carte réseau ou au sous-réseau, et si une adresse IP publique est affectée à votre machine virtuelle, tous les ports sont ouverts pour les accès entrants et sortants. Si la machine virtuelle a une adresse IP publique, l’application de groupes de sécurité réseau à la carte réseau ou au sous-réseau est vivement recommandée.  

