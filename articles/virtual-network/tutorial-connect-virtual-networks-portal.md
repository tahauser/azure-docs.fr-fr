---
title: Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels - Portail Azure | Microsoft Docs
description: Apprenez comment connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac0c1033546758a77b43298a5fa8cba5f5204650
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Connecter des réseaux virtuels à l’aide de l’appairage de réseaux virtuels en utilisant le portail Azure

Vous pouvez connecter des réseaux virtuels entre eux à l’aide de l’appairage de réseaux virtuels. Une fois que les réseaux virtuels sont appairés, les ressources des deux réseaux virtuels peuvent communiquer entre elles avec les mêmes bande passante et latence, comme si les ressources se trouvaient sur le même réseau virtuel. Cet article décrit la création et l’appairage de deux réseaux virtuels. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer deux réseaux virtuels
> * Créer un appairage entre des réseaux virtuels
> * Tester l’appairage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-virtual-networks"></a>Créer des réseaux virtuels

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Comme indiqué dans l’image suivante, entrez *myVirtualNetwork1* comme **Nom**, *10.0.0.0/16* pour **Espace d’adressage**, **myResourceGroup** comme **Groupe de ressources**, *Subnet1* comme **Nom du sous-réseau**, 10.0.0.0/24 comme **Plage d’adresses de sous-réseau**, sélectionnez un **Emplacement** et votre **Abonnement**, acceptez les valeurs par défaut restantes, puis sélectionnez **Créer** :

    ![Créez un réseau virtuel](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Effectuez à nouveau les étapes 1 à 3, avec les modifications suivantes :
    - **Nom** : *myVirtualNetwork2*
    - **Groupe de ressources** : sélectionnez **Utiliser existant**, puis **myResourceGroup**.
    - **Espace d’adressage** : *10.1.0.0/16*
    - **Plage d’adresses de sous-réseau** : *10.1.0.0/24*

    Le préfixe d’adresse pour le réseau virtuel *myVirtualNetwork2* ne chevauche pas l’espace d’adressage du réseau virtuel *myVirtualNetwork1*. Il est impossible d’appairer des réseaux virtuels dont les espaces d’adressage se chevauchent.

## <a name="peer-virtual-networks"></a>Appairer des réseaux virtuels

1. Dans la zone de recherche en haut du portail Azure, commencez à taper *MyVirtualNetwork1*. Quand la mention **myVirtualNetwork1** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez **Peerings** (Appairages) sous **PARAMÈTRES**, puis **+ Ajouter**, comme indiqué dans l’image suivante :

    ![Créer un appairage](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Entrez ou sélectionnez les informations affichées dans l’image suivante, puis sélectionnez **OK**. Pour sélectionner le réseau virtuel *myVirtualNetwork2*, sélectionnez **Réseau virtuel**, puis *myVirtualNetwork2*.

    ![Paramètres d’appairage](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **L’ÉTAT D’APPAIRAGE** est *Initié*, comme indiqué dans l’image suivante :

    ![État d’appairage](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Si vous ne voyez pas l’état, actualisez votre navigateur.

4. Recherchez le réseau virtuel *myVirtualNetwork2*. Quand il apparaît dans les résultats de la recherche, sélectionnez-le.
5. Effectuez à nouveau les étapes 1 à 3, avec les modifications suivantes, puis sélectionnez **OK** :
    - **Nom** : *myVirtualNetwork2-myVirtualNetwork1*
    - **Réseau virtuel** : *myVirtualNetwork1*

    **L’ÉTAT D’APPAIRAGE** est *Connecté*. Azure a également changé l’état *Initié* de l’appairage *myVirtualNetwork2-myVirtualNetwork1* en *Connecté*. L’appairage de réseaux virtuels n’est pas entièrement établi tant que l’état d’appairage pour les deux réseaux virtuels n’est pas *Connecté*. 

Les appairages sont effectués entre deux réseaux virtuels, mais ne sont pas transitifs. Ainsi, par exemple, si vous souhaitez également appairer *myVirtualNetwork2* avec *myVirtualNetwork3*, vous devez créer un appairage supplémentaire entre les réseaux virtuels *myVirtualNetwork2* et *myVirtualNetwork3*. Bien que *myVirtualNetwork1* soit appairé avec *myVirtualNetwork2*, les ressources au sein de *myVirtualNetwork1* ne peuvent accéder aux ressources dans *myVirtualNetwork3* que si *myVirtualNetwork1* a été également appairé avec *myVirtualNetwork3*. 

Avant d’appairer des réseaux virtuels en production, il est recommandé de bien vous familiariser avec la [vue d’ensemble de l’appairage](virtual-network-peering-overview.md), la [gestion de l’appairage](virtual-network-manage-peering.md) et les [limites de réseau virtuel](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Bien que cet article décrive un appairage entre deux réseaux virtuels appartenant aux mêmes abonnement et emplacement, vous pouvez également appairer des réseaux virtuels dans des [régions différentes](#register) et des [abonnements Azure différents](create-peering-different-subscriptions.md#portal). Vous pouvez également créer des [conceptions de réseaux Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) à l’aide de l’appairage.

## <a name="test-peering"></a>Tester l’appairage

Pour tester la communication réseau entre les machines virtuelles sur différents réseaux virtuels via un appairage, déployez une machine virtuelle sur chaque sous-réseau, puis établissez la communication entre les machines virtuelles. 

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle dans chaque réseau virtuel pour pouvoir valider ultérieurement la communication entre elles.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. Vous pouvez sélectionner différents systèmes d’exploitation, mais les étapes restantes partent du principe que vous avez sélectionné **Windows Server 2016 Datacenter**. 
3. Sélectionnez ou saisissez les informations suivantes pour **Informations de base**, puis sélectionnez **OK** :
    - **Nom** : *myVm1*
    - **Groupe de ressources** : sélectionnez **Utiliser existant**, puis *myResourceGroup*.
    - **Emplacement** : sélectionnez *États-Unis de l’Est*.

    Le **nom d’utilisateur** et le **mot de passe** que vous entrez vous serviront lors d’une étape ultérieure. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). L’**Emplacement** et l’**Abonnement** choisis doivent être identiques à l’emplacement et l’abonnement du réseau virtuel. Il n’est pas nécessaire de sélectionner le même groupe de ressources que celui dans lequel a été créé le réseau virtuel,mais il est tout de même sélectionné dans cet article.
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Sélectionnez ou saisissez les informations suivantes pour **Paramètres**, puis sélectionnez **OK** :
    - **Réseau virtuel** : vérifiez que **myVirtualNetwork1** est sélectionné. Sinon, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork1** sous **Choisir un réseau virtuel**.
    - **Sous-réseau** : vérifiez que **Subnet1** est sélectionné. Sinon, sélectionnez **Sous-réseau**, puis **Subnet1** sous **Choisir un sous-réseau**, comme indiqué dans l’image suivante :
    
        ![Paramètres de la machine virtuelle](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Sous **Créer** dans le **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.
7. Effectuez à nouveau les étapes 1 à 6, mais entrez *myVm2* comme **Nom** de la machine virtuelle et sélectionnez *myVirtualNetwork2* comme **Réseau virtuel**.

Azure a affecté *10.0.0.4* comme adresse IP privée de la machine virtuelle *myVm1* et 10.1.0.4 à la machine virtuelle *myVm2*, car il s’agissait des premières adresses IP disponibles dans *Subnet1* des réseaux virtuels *myVirtualNetwork1* et *myVirtualNetwork2*, respectivement.

La création des machines virtuelles prend quelques minutes. Ne poursuivez pas les étapes restantes avant la création des deux machines virtuelles.

### <a name="test-virtual-machine-communication"></a>Tester la communication avec la machine virtuelle

1. Dans la zone de *recherche* en haut du portail, commencez à taper *myVm1*. Quand **myVm1** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Créez une connexion Bureau à distance à la machine virtuelle *myVm1* en sélectionnant **Connecter**, comme indiqué dans l’image suivante :

    ![Connexion à la machine virtuelle](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
4. Entrez le nom d’utilisateur et le mot de passe choisis lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
6. Dans une étape ultérieure, un test ping est utilisé pour communiquer avec la machine virtuelle *myVm2* depuis la machine virtuelle *myVmWeb*. Le test ping utilise le protocole IMCP, qui est bloqué par défaut par le pare-feu Windows. Autorisez le protocole IMCP dans le pare-feu Windows en entrant la commande suivante depuis une invite de commandes :

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bien que cet article utilise un test ping pour le test, il n’est pas recommandé d’autoriser le protocole IMCP dans le Pare-feu Windows lors de déploiements en production.

7. Pour établir la connexion à la machine virtuelle *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1* :

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Étant donné que vous avez activé la commande ping sur *myVm1*, vous pouvez désormais y effectuer un test ping par adresse IP :

    ```
    ping 10.0.0.4
    ```
    
    Vous recevez quatre réponses. Si vous effectuez un test ping avec le nom de la machine virtuelle (*myVm1*) au lieu de son adresse IP, le test ping échoue, car *myVm1* est un nom d’hôte inconnu. La résolution de noms par défaut d’Azure fonctionne entre des machines virtuelles dans le même réseau virtuel, mais pas entre des machines virtuelles dans des réseaux virtuels différents. Pour résoudre les noms sur plusieurs réseaux virtuels, vous devez [déployer votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) ou utiliser les [domaines privés Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Déconnectez vos sessions RDP sur *myVm1* et *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

**<a name="register"></a>S’inscrire à la préversion de l’appairage de réseaux virtuels mondiaux**

L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’appairage de réseaux virtuels dans des régions différentes est actuellement en version préliminaire. Consultez [Virtual network updates](https://azure.microsoft.com/updates/?product=virtual-network) (Mises à jour du réseau virtuel) pour les régions disponibles. Pour appairer des réseaux virtuels dans différentes régions, vous devez d’abord vous inscrire à la préversion. Il n’est pas possible de vous inscrire à l’aide du portail, mais vous pouvez le faire avec [PowerShell](tutorial-connect-virtual-networks-powershell.md#register) ou [Azure CLI](tutorial-connect-virtual-networks-cli.md#register). Si vous tentez d’appairer des réseaux virtuels dans différentes régions avant de vous inscrire pour utiliser cette fonctionnalité, l’appairage échoue.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment connecter deux réseaux avec l’appairage de réseaux virtuels.

Continuez à connecter votre propre ordinateur à un réseau virtuel via un VPN et interagir avec des ressources d’un réseau virtuel ou de réseaux virtuels appairés.

> [!div class="nextstepaction"]
> [Connecter votre ordinateur à un réseau virtuel](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
