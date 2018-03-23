---
title: Résoudre les problèmes relatifs aux groupes de sécurité réseau - Portail | Microsoft Docs
description: Découvrez comment résoudre les problèmes liés aux groupes de sécurité réseau dans le modèle de déploiement Azure Resource Manager à l’aide du portail Azure.
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: 5998aa0e0b281ead859c24d792c3e175e3cca899
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Résoudre les problèmes relatifs aux groupes de sécurité réseau à l’aide du portail Azure
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

### <a name="vm"></a>Afficher les règles de sécurité effectives pour une machine virtuelle
Pour dépanner des groupes de sécurité réseau pour une machine virtuelle, procédez comme suit :

Vous pouvez afficher la liste complète des règles de sécurité effectives sur une carte réseau à partir de la machine virtuelle elle-même. Vous pouvez également ajouter, modifier et supprimer les règles du groupe de sécurité réseau de carte réseau et de sous-réseau à partir du panneau des règles effectives si vous êtes autorisé à effectuer ces opérations.

1. Connectez-vous au portail Azure sur https://portal.azure.com avec un compte Azure. L’opération *Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action* doit être affectée à votre compte pour l’interface réseau. Pour savoir comment affecter des opérations à des comptes, consultez [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Cliquez sur **Tous les services**, puis sur **Machines virtuelles** dans la liste qui s’affiche.
3. Sélectionnez une machine virtuelle à dépanner dans la liste qui s’affiche. Un panneau de machine virtuelle avec des options s’affiche.
4. Cliquez sur **Diagnostiquer et résoudre les problèmes**, puis sélectionnez un problème courant. Pour cet exemple, le problème **Je ne peux pas me connecter à ma machine virtuelle Windows** est sélectionné. 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)
5. Des étapes s’affichent sous le problème, comme illustré dans l’image suivante : 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)
   
    Dans la liste des étapes recommandées, cliquez sur *Règles de groupe de sécurité effectives* .
6. Le panneau **Obtenir les règles de sécurité effectives** s’affiche, comme illustré dans l’image suivante :
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)
   
    Observez les sections suivantes de l’image :
   
   * **Étendue :** définie sur *VM1*, la machine virtuelle sélectionnée à l’étape 3.
   * **Interface réseau :***VM1-NIC1* est sélectionné. Une machine virtuelle peut avoir plusieurs interfaces réseau. Chaque carte réseau peut avoir des règles de sécurité effectives uniques. En cours de dépannage, vous devrez peut-être afficher les règles de sécurité effectives pour chaque carte réseau.
   * **Groupes de sécurité réseau associés :** des groupes de sécurité réseau peuvent être appliqués à la carte réseau et au sous-réseau auquel elle est connectée. Dans l’image, un groupe de sécurité réseau a été appliqué à la carte réseau et au sous-réseau auquel elle est connectée. Vous pouvez cliquer sur les noms des groupes de sécurité réseau pour en modifier directement les règles.
   * **Onglet VM1-nsg :** la liste de règles affichée dans l’image a trait au groupe de sécurité réseau appliqué à la carte réseau. Azure crée plusieurs règles par défaut à chaque création d’un groupe de sécurité réseau. Vous ne pouvez pas supprimer les règles par défaut, mais vous pouvez les remplacer par des règles de priorité plus élevée. Pour en savoir plus sur les règles par défaut, consultez [Vue d’ensemble d’un groupe de sécurité réseau](virtual-networks-nsg.md#default-rules) .
   * **Colonne DESTINATION :** certaines règles contiennent du texte dans la colonne, tandis que d’autres contiennent des préfixes d’adresse. Le texte est le nom des balises par défaut appliquées à la règle de sécurité lors de sa création. Les balises sont des identificateurs fournis par le système, qui représentent plusieurs préfixes. La sélection d’une règle avec une balise, telle que *AllowInternetOutBound*, répertorie les préfixes dans le panneau **Préfixes d’adresse** .
   * **Télécharger :** la liste de règles peut être longue. Vous pouvez télécharger un fichier .csv des règles pour l’analyse hors connexion en cliquant sur **Télécharger** , puis en enregistrant le fichier.
   * **AllowRDP** : cette règle autorise les connexions RDP à la machine virtuelle.
7. Cliquez sur l’onglet **Subnet1-NSG** pour afficher les règles effectives du groupe de sécurité réseau appliquées au sous-réseau, comme illustré dans l’image suivante : 
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)
   
    Notez la règle *denyRDP* **Inbound** . Les règles de trafic entrant appliquées au sous-réseau sont évaluées avant les règles appliquées à l’interface réseau. Étant donné que la règle de refus est appliquée au niveau du sous-réseau, la demande de connexion à TCP 3389 échoue, car la règle d’autorisation au niveau de la carte réseau n’est jamais évaluée. 
   
    La règle *denyRDP* est la cause d’échec de la connexion RDP. Sa suppression devrait résoudre le problème.
   
   > [!NOTE]
   > Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution, ou si les groupes de sécurité réseau n’ont pas été appliqués à la carte réseau ou au sous-réseau, aucune règle ne s’affiche.
   > 
   > 
8. Pour modifier les règles du groupe de sécurité réseau, cliquez sur *Subnet1-NSG* dans la section **Groupes de sécurité réseau associés** .
   Cette opération ouvre le panneau **Subnet1-NSG** . Vous pouvez modifier directement les règles en cliquant sur **Règles de sécurité de trafic entrant**.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)
9. Après la suppression de la règle de trafic entrant *denyRDP* dans **Subnet1-NSG** et l’ajout d’une règle *allowRDP*, la liste des règles effectives ressemble à l’image suivante :
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)
   
    Vérifiez que le port TCP 3389 est ouvert en ouvrant une connexion RDP à la machine virtuelle ou en utilisant l’outil PsPing. Pour en savoir plus sur PsPing, lisez la [page de téléchargement de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="nic"></a>Afficher les règles de sécurité effectives pour une interface réseau
Si le flux de trafic de votre machine virtuelle est affecté pour une carte réseau spécifique, vous pouvez afficher la liste complète des règles effectives pour la carte réseau à partir du contexte des interfaces réseau en procédant comme suit :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur **Tous les services**, puis sur **Interfaces réseau** dans la liste qui s’affiche.
3. Sélectionnez une interface réseau. Dans l’image suivante, une carte réseau nommée *VM1-NIC1* est sélectionnée.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)
   
    Notez que l’ **Étendue** est définie sur l’interface réseau sélectionnée. Pour en savoir plus sur les informations complémentaires affichées, lisez l’étape 6 de la section **Dépanner les groupes de sécurité réseau pour une machine virtuelle** dans cet article.
   
   > [!NOTE]
   > En cas de suppression d’un groupe de sécurité réseau d’une interface réseau, le groupe de sécurité réseau du sous-réseau continue à s’appliquer à la carte réseau donnée. Dans ce cas, la sortie affiche uniquement les règles du groupe de sécurité réseau du sous-réseau. Les règles s’affichent uniquement si la carte réseau est connectée à une machine virtuelle.
   > 
   > 
4. Vous pouvez modifier directement les règles des groupes de sécurité réseau associés à une carte réseau et à un sous-réseau. Pour en savoir plus, consultez l’étape 8 de la section **Afficher les règles de sécurité effectives pour une machine virtuelle** dans cet article.

## <a name="nsg"></a>Afficher les règles de sécurité effectives pour un groupe de sécurité réseau
Lorsque vous modifiez les règles d’un groupe de sécurité réseau, vous pouvez examiner l’impact des règles ajoutées à une machine virtuelle particulière. Vous pouvez afficher la liste complète des règles de sécurité effectives pour toutes les cartes réseau auxquelles s’applique un groupe de sécurité réseau donné, sans devoir changer de contexte à partir du panneau du groupe de sécurité réseau. Pour résoudre des problèmes de règles effectives au sein d’un groupe de sécurité réseau, procédez comme suit :

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. Cliquez sur **Tous les services**, puis sur **Groupes de sécurité réseau** dans la liste qui s’affiche.
3. Sélectionnez un groupe de sécurité réseau. Dans l’image suivante, un groupe de sécurité réseau nommé VM1-nsg a été sélectionné.
   
    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)
   
    Observez les sections suivantes de l’image précédente :
   
   * **Étendue :** définissez cette option sur le groupe de sécurité réseau sélectionné.
   * **Machine virtuelle :** quand un groupe de sécurité réseau est appliqué à un sous-réseau, il est appliqué à toutes les interfaces réseau attachées à toutes les machines virtuelles connectées au sous-réseau. Cette liste affiche toutes les machines virtuelles auxquelles ce groupe de sécurité réseau s’applique. Vous pouvez sélectionner n’importe quelle machine virtuelle dans la liste.
     
     > [!NOTE]
     > Si un groupe de sécurité réseau est appliqué uniquement à un sous-réseau vide, les machines virtuelles ne sont pas répertoriées. Si un groupe de sécurité réseau est appliqué à une carte réseau qui n’est pas associée à une machine virtuelle, cette carte réseau n’est pas répertoriée. 
     > 
     > 
   * **Interface réseau** : une machine virtuelle peut avoir plusieurs interfaces réseau. Vous pouvez sélectionner une interface réseau attachée à la machine virtuelle sélectionnée.
   * **AssociatedNSGs** : à tout moment, une carte réseau peut avoir jusqu’à deux groupes de sécurité réseau effectifs, l’un appliqué à la carte réseau et l’autre au sous-réseau. Bien que l’étendue sélectionnée soit VM1-nsg, si la carte réseau a un groupe de sécurité réseau de sous-réseau effectif, la sortie affiche les deux groupes de sécurité réseau.
4. Vous pouvez modifier directement les règles des groupes de sécurité réseau associés à une carte réseau ou à un sous-réseau. Pour en savoir plus, consultez l’étape 8 de la section **Afficher les règles de sécurité effectives pour une machine virtuelle** dans cet article.

Pour en savoir plus sur les informations supplémentaires affichées, consultez l’étape 6 de la section **Afficher les règles de sécurité effectives pour une machine virtuelle** dans cet article.

> [!NOTE]
> S’il ne peut y avoir un seul groupe de sécurité réseau appliqué à un sous-réseau et à une carte réseau, un groupe de sécurité réseau peut être associé à plusieurs cartes réseau et sous-réseaux.
> 
> 

## <a name="considerations"></a>Considérations
Lors de la résolution de problèmes de connectivité, considérez les points suivants :

* Des règles de groupe de sécurité réseau par défaut bloquent l’accès entrant à partir d’internet et n’autorisent que le trafic entrant dans le réseau virtuel. Les règles doivent être ajoutées de façon explicite afin d’autoriser l’accès entrant à partir d’Internet, en fonction des besoins.
* Si aucune règle de sécurité du groupe de sécurité réseau n’est à l’origine d’un échec de connectivité réseau d’une machine virtuelle, les causes du problème peuvent être les suivantes :
  * Logiciel de pare-feu s’exécutant à l’intérieur du système d’exploitation de la machine virtuelle
  * Itinéraires configurés pour des appliances virtuelles ou un trafic local. Le trafic Internet peut être redirigé localement au moyen d’un tunneling forcé. Il se peut qu’une connexion RDP/SSH d’Internet à votre machine virtuelle ne fonctionne pas avec ce paramètre, selon la façon dont le matériel de réseau local gère ce trafic. Pour savoir comment diagnostiquer des problèmes d’itinéraire susceptibles d’entraver le flux de trafic échangé avec la machine virtuelle, consultez [Résolution des problèmes d’itinéraire](virtual-network-routes-troubleshoot-powershell.md) . 
* Si vous avez des réseaux virtuels homologues, par défaut, la balise VIRTUAL_NETWORK s’étend automatiquement pour inclure les préfixes des réseaux virtuels homologues. Pour résoudre des problèmes liés à la connectivité d’homologation de réseau virtuel, vous pouvez consulter ces préfixes dans la liste **ExpandedAddressPrefix** . 
* Les règles de sécurité effectives sont affichées uniquement s’il existe un groupe de sécurité réseau associé à la carte réseau et au sous-réseau de la machine virtuelle. 
* Si aucun groupe de sécurité réseau n’est associé à la carte réseau ou au sous-réseau, et si une adresse IP publique est affectée à votre machine virtuelle, tous les ports sont ouverts pour les accès entrants et sortants. Si la machine virtuelle a une adresse IP publique, l’application de groupes de sécurité réseau à la carte réseau ou au sous-réseau est vivement recommandée.

