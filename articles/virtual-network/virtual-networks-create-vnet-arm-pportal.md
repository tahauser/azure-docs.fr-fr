---
title: Créer un réseau virtuel Azure comprenant plusieurs sous-réseaux - Portail | Microsoft Docs
description: Découvrez comment créer un réseau virtuel comprenant des sous-réseaux à l’aide du portail Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Créer un réseau virtuel comprenant des sous-réseaux à l’aide du portail Azure

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. La création de plusieurs sous-réseaux dans un réseau virtuel permet de segmenter votre réseau afin que vous puissiez filtrer ou contrôler le flux du trafic entre les sous-réseaux. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer un sous-réseau
> * Tester la communication réseau entre les machines virtuelles

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Comme indiqué dans l’image suivante, entrez *myVirtualNetwork* comme **Nom**, *10.0.0.0/16* pour **Espace d’adressage**, **myResourceGroup** comme **Groupe de ressources**, *Public* comme **Nom du sous-réseau**, 10.0.0.0/24 comme **Plage d’adresses** du sous-réseau, sélectionnez un **Emplacement** et votre **Abonnement**, acceptez les valeurs par défaut restantes, puis sélectionnez **Créer** :

    ![Créez un réseau virtuel](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    L’**espace d’adressage** et la **plage d’adresses** sont spécifiés en notation CIDR. L’**espace d’adressage** spécifié inclut les adresses IP 10.0.0.0-10.0.255.254. La **plage d’adresses** spécifiée pour un sous-réseau doit figurer dans l’**espace d’adressage** défini pour le réseau virtuel. Azure DHCP attribue des adresses IP à partir d’une plage d’adresses de sous-réseau à des ressources déployées dans un sous-réseau. Azure attribue uniquement les adresses 10.0.0.4-10.0.0.254 à des ressources déployées dans le sous-réseau **Public**, car Azure réserve les quatre premières adresses (10.0.0.0-10.0.0.3 pour le sous-réseau, dans cet exemple) et la dernière adresse (10.0.0.255 pour le sous-réseau, dans cet exemple) dans chaque sous-réseau.

## <a name="create-a-subnet"></a>Créer un sous-réseau

1. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, commencez à taper *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sélectionnez **Sous-réseaux** puis **+ Sous-réseau**, comme montré dans l’image suivante :

     ![Ajouter un sous-réseau](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. Dans le champ **Ajouter un sous-réseau** qui s’affiche, entrez *Privé* comme **Nom**, *10.0.1.0/24* comme **Plage d’adresses**, puis sélectionnez **OK**.  La plage d’adresses d’un sous-réseau ne peut pas chevaucher les plages d’adresses d’autres sous-réseaux au sein d’un réseau virtuel. 

Avant de déployer des sous-réseaux et réseaux virtuels Azure pour une utilisation en production, il est recommandé de vous familiariser soigneusement avec les [considérations](manage-virtual-network.md#create-a-virtual-network) et [limites de réseau virtuel](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de l’espace d’adressage. Une fois que les ressources sont déployées dans des sous-réseaux, certaines modifications de réseau virtuel et de sous-réseau (p. ex. la modification des plages d’adresses) peuvent nécessiter le redéploiement des ressources Azure existantes, déployées dans les sous-réseaux.

## <a name="test-network-communication"></a>Tester la communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé entre elles et avec Internet. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir tester ultérieurement la communication réseau entre eux et Internet.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. Vous pouvez sélectionner différents systèmes d’exploitation, mais les étapes restantes partent du principe que vous avez sélectionné **Windows Server 2016 Datacenter**. 
3. Sélectionnez ou saisissez les informations suivantes pour **Informations de base**, puis sélectionnez **OK** :
    - **Nom** : *myVmWeb*
    - **Groupe de ressources** : sélectionnez **Utiliser existant**, puis *myResourceGroup*.
    - **Emplacement** : sélectionnez *États-Unis de l’Est*.

    Le **nom d’utilisateur** et le **mot de passe** que vous entrez vous serviront lors d’une étape ultérieure. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). L’**Emplacement** et l’**Abonnement** choisis doivent être identiques à l’emplacement et l’abonnement du réseau virtuel. Il n’est pas nécessaire de sélectionner le même groupe de ressources dans lequel a été créé le réseau virtuel,mais il est tout de même sélectionné dans ce didacticiel.
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Sélectionnez ou saisissez les informations suivantes pour **Paramètres**, puis sélectionnez **OK** :
    - **Réseau virtuel** : vérifiez que **myVirtualNetwork** est sélectionné. Sinon, sélectionnez **Réseau virtuel** puis **myVirtualNetwork** sous **Choisir un réseau virtuel**.
    - **Sous-réseau** : vérifiez que **Public** est sélectionné. Sinon, sélectionnez **Sous-réseau** puis **Public** sous **Choisir un sous-réseau**, comme montré dans l’image suivante :
    
        ![Paramètres de la machine virtuelle](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. Sous **Créer** dans le **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.
7. Réalisez de nouveau les étapes 1 à 6, mais entrez *myVmMgmt* comme **Nom** de la machine virtuelle et sélectionnez **Privé** comme **Sous-réseau**.

La création des machines virtuelles prend quelques minutes. Ne poursuivez pas les étapes restantes avant la création des deux machines virtuelles.

Les machines virtuelles créées dans cet article ont une [interface réseau](virtual-network-network-interface.md) avec une adresse IP attribuée dynamiquement à l’interface réseau. Une fois que vous avez déployé la machine virtuelle, vous pouvez [ajouter des adresses IP publiques et privées, ou modifier la méthode d’attribution d’adresse IP pour qu’elle soit définie sur « statique »](virtual-network-network-interface-addresses.md#add-ip-addresses). Vous pouvez [ajouter des interfaces réseau](virtual-network-network-interface-vm.md#vm-add-nic), jusqu’à la limite prise en charge par le [taille de machine virtuelle](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que vous sélectionnez lorsque vous créez une machine virtuelle. Vous pouvez également [activer la virtualisation d’E/S racine unique](create-vm-accelerated-networking-powershell.md) pour une machine virtuelle, mais uniquement lorsque vous créez une machine virtuelle avec une taille de machine virtuelle qui prend en charge la fonctionnalité.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Communiquer entre les machines virtuelles et avec Internet

1. Dans le champ *Recherche* en haut du portail, commencez à taper *myVmMgmt*. Quand **myVmMgmt** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Créez une connexion à distance dans la machine virtuelle *myVmMgmt* en sélectionnant **Connecter**, comme montré dans l’image suivante :

    ![Connexion à la machine virtuelle](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
4. Entrez le nom d’utilisateur et le mot de passe choisis lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
6. Dans une étape ultérieure, un test ping est utilisé pour communiquer avec la machine virtuelle *myVmMgmt* depuis la machine virtuelle *myVmWeb*. Le test ping utilise le protocole IMCP, qui est bloqué par défaut par le pare-feu Windows. Autorisez le protocole IMCP dans le pare-feu Windows en entrant la commande suivante depuis une invite de commandes :

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bien que cet article utilise un test ping, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements de production.
7. Pour des raisons de sécurité, il est courant de limiter le nombre de machines virtuelles qui peuvent être connectées à distance à un réseau virtuel. Dans ce didacticiel, la machine virtuelle *myVmMgmt* est utilisée pour gérer la machine virtuelle *myVmWeb* dans le réseau virtuel. Pour communiquer à distance avec la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt*, entrez la commande suivante à partir d’une invite de commandes :

    ``` 
    mstsc /v:myVmWeb
    ```
8. Pour communiquer avec la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*, entrez la commande suivante à partir d’une invite de commandes :

    ```
    ping myvmmgmt
    ```

    Le résultat ressemble à ce qui suit :
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Vous pouvez constater que l’adresse de la machine virtuelle *myVmMgmt* est 10.0.1.4. 10.0.1.4 était la première adresse IP disponible dans la plage d’adresses du sous-réseau *Privé* que vous avez déployé sur la machine virtuelle *myVmMgmt* à l’étape précédente.  Vous voyez que le nom de domaine complet de l’ordinateur virtuel est *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Bien que la partie *dar5p44cif3ulfq00wxznl3i3f* du nom de domaine soit différente pour votre machine virtuelle, les autres parties du nom de domaine sont les mêmes. 

    Par défaut, toutes les machines virtuelles Azure utilisent le service Azure DNS par défaut. Toutes les machines virtuelles au sein d’un réseau virtuel peuvent résoudre les noms de toutes les autres machines virtuelles dans le même réseau virtuel à l’aide du service DNS par défaut d’Azure. Au lieu d’utiliser le service DNS par défaut d’Azure, vous pouvez utiliser votre propre serveur DNS ou la fonctionnalité de domaine privé du service Azure DNS. Pour plus d’informations, consultez [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) ou [Utilisation d’Azure DNS pour les domaines privés](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Pour installer IIS pour Windows Server sur la machine virtuelle *myVmWeb*, entrez la commande suivante depuis une session PowerShell :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Une fois l’installation d’IIS terminée, déconnectez la session à distance avec la machine virtuelle *myVmWeb*. Vous vous retrouvez donc dans la session à distance avec la machine virtuelle *myVmMgmt*. Ouvrez un navigateur web et accédez à l’adresse http://myvmweb. Vous voyez la page d’accueil IIS.
11. Déconnectez la session à distance avec la machine virtuelle *myVmMgmt*.
12. Trouvez l’adresse IP publique de la machine virtuelle *myVmWeb*. Quand Azure a créé la machine virtuelle *myVmWeb*, une adresse IP publique nommée *myVmWeb* a été également créée et attribuée à la machine virtuelle. Vous pouvez voir que l’adresse 52.170.5.92 a été attribuée à la machine virtuelle *myVmMgmt* comme **Adresse IP publique** dans l’image de l’étape 2. Pour trouver l’adresse IP publique attribuée à la machine virtuelle *myVmWeb*, cherchez *myVmWeb* dans le champ de recherche du portail, puis sélectionnez-la lorsqu’elle apparaît dans les résultats.

    Bien qu’il ne soit pas obligatoire qu’une machine virtuelle ait une adresse IP publique attribuée, Azure attribue par défaut une adresse IP publique à chacune des machines virtuelles que vous créez. Pour communiquer à partir d’Internet vers une machine virtuelle, une adresse IP publique doit être attribuée à la machine virtuelle. Toutes les machines virtuelles peuvent communiquer en sortie avec Internet, qu’une adresse IP publique soit attribuée à la machine virtuelle ou non. Pour en savoir plus sur les connexions Internet sortantes dans Azure, consultez [Connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. Sur votre ordinateur, accédez à l’adresse IP publique de la machine virtuelle *myVmWeb*. La tentative d’affichage de la page d’accueil IIS depuis votre ordinateur échoue. Cet échec se produit car, quand les machines virtuelles ont été déployées, Azure a créé par défaut un groupe de sécurité réseau pour chaque machine virtuelle. 

     Un groupe de sécurité réseau contient des règles de sécurité qui autorisent ou rejettent le trafic réseau entrant et sortant en fonction du port et de l’adresse IP. Le groupe de sécurité réseau par défaut créé par Azure permet la communication sur tous les ports entre les ressources dans le même réseau virtuel. Pour les machines virtuelles Linux, le groupe de sécurité réseau par défaut refuse tout le trafic entrant à partir d’Internet sur tous les ports, à l’exception du port TCP 3389 (SSH). Par conséquent, par défaut, vous pouvez également vous connecter à distance directement sur la machine virtuelle *myVmWeb* à partir d’Internet, même si vous ne souhaitez pas ouvrir le port 3389 à un serveur web. Étant donné que la navigation web communique via le port 80, la communication depuis Internet échoue, car aucune règle n’est présente dans le groupe de sécurité réseau par défaut autorisant le trafic sur le port 80.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment déployer un réseau virtuel comprenant plusieurs sous-réseaux. Vous avez également appris que, lorsque vous créez une machine virtuelle Windows, Azure crée une interface réseau qu’il associe à la machine virtuelle et crée un groupe de sécurité réseau qui autorise uniquement le trafic sur le port 3389, à partir d’Internet. Passez au didacticiel suivant pour apprendre à filtrer le trafic réseau vers des sous-réseaux, plutôt que vers des machines virtuelles individuelles.

> [!div class="nextstepaction"]
> [Filtrer le trafic réseau vers des sous-réseaux](./virtual-networks-create-nsg-arm-pportal.md)
