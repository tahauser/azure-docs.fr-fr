---
title: "Créer un routage défini par l’utilisateur pour router le trafic réseau via une appliance virtuelle réseau - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer un routage défini par l’utilisateur pour remplacer le routage par défaut d’Azure en routant le trafic réseau via une appliance virtuelle réseau."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2018
---
# <a name="create-a-user-defined-route---azure-portal"></a>Créer un routage défini par l’utilisateur - Portail Azure

Ce didacticiel explique comment créer un routage (ou itinéraire) défini par l’utilisateur pour router le trafic entre deux sous-réseaux d’un [réseau virtuel](virtual-networks-overview.md) via une appliance virtuelle réseau. Une appliance virtuelle réseau est une machine virtuelle qui exécute une application réseau telle qu’un pare-feu. Pour en savoir plus sur les appliances virtuelles réseau préconfigurées que vous pouvez déployer dans un réseau virtuel Azure, voir [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Lorsque vous créez des sous-réseaux dans un réseau virtuel, Azure crée des [routes système](virtual-networks-udr-overview.md#system-routes) par défaut qui permettent aux ressources dans tous les sous-réseaux de communiquer entre elles, comme illustré dans l’image suivante :

![Itinéraires par défaut](./media/create-user-defined-route/default-routes.png)

Dans ce didacticiel, vous allez créer un réseau virtuel comprenant des sous-réseaux public, privé et de périmètre (DMZ), comme illustré dans l’image qui suit. Généralement, des serveurs web peuvent être déployés sur un sous-réseau public, et une application ou un serveur de base de données peuvent être déployés sur un sous-réseau privé. Vous créez une machine virtuelle faisant office d’appliance virtuelle réseau dans le sous-réseau de périmètre (DMZ) et, éventuellement, une machine virtuelle dans chaque sous-réseau communiquant via l’appliance virtuelle réseau. Tout le trafic entre les sous-réseaux privés et publics est routé via l’appliance, comme indiqué dans l’image suivante :

![Itinéraires définis par l’utilisateur](./media/create-user-defined-route/user-defined-routes.png)

Cet article explique comment créer un routage (ou itinéraire) défini par l'utilisateur à l’aide du modèle de déploiement Resource Manager, qui est le modèle de déploiement que nous recommandons d’utiliser lors de la création de routages définis par l'utilisateur. Si vous avez besoin créer un routage défini par l’utilisateur (classique), voir [Création des itinéraires définis par l’utilisateur](virtual-network-create-udr-classic-ps.md). Si vous ne connaissez pas les modèles de déploiement Azure, consultez [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour en savoir plus sur les routages définis par l’utilisateur, voir [Vue d’ensemble des routages définis par l’utilisateur](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Créer des itinéraires et une appliance virtuelle réseau

1. **Condition préalable** : créez un réseau virtuel comprenant deux sous-réseaux en procédant de la manière décrite dans [Créer un réseau virtuel](#create-a-virtual-network).
2. Après avoir créé le réseau virtuel, dans un navigateur Internet, accédez au [portail Azure](https://portal.azure.com). Connectez-vous à votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Dans le champ **Rechercher des ressources** situé en haut du portail, entrez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Le groupe de ressources a été créé dans le cadre de la condition préalable.
4. Cliquez sur **myVnet**, comme illustré dans l’image suivante :

    ![Paramètres d’interface réseau](./media/create-user-defined-route/virtual-network.png)

5. Créez un sous-réseau pour l’appliance virtuelle réseau :
 
    - Sous **myVnet**, sous **Paramètres** sur le côté gauche, cliquez sur **Sous-réseaux**.
    - Cliquez sur **+ Sous-réseau**, comme illustré dans l’image suivante :

        ![Sous-réseaux](./media/create-user-defined-route/subnets.png) 
    - Sous **Ajouter un sous-réseau**, entrez les valeurs suivantes, puis cliquez sur **OK** :

        |Paramètre|Valeur|
        |-----|-----|
        |NOM|DMZ|
        |Plage d’adresses (bloc CIDR)|10.0.2.0/24|

6. Créez une machine virtuelle d’appliance virtuelle réseau :

    - Sur le côté gauche du portail, cliquez sur **+ Nouveau**, **Compute**, puis **Windows Server 2016 Datacenter** ou **Ubuntu Server 16.04 LTS**.
    - Dans le panneau **De base** qui s’affiche, entrez les valeurs suivantes, puis cliquez sur **OK** :

        |Paramètre|Valeur|
        |---|---|
        |NOM|myVm-Nva|
        |Nom d'utilisateur|azureuser|
        |Mot de passe et Confirmer le mot de passe|Mot de passe de votre choix.|
        |Abonnement|Sélectionnez votre abonnement|
        |Groupe de ressources|Cliquez sur **Utiliser l’existant**, puis sélectionnez **myResourceGroup**.|
        |Lieu|Est des États-Unis|
    - Dans le panneau **Choisir une taille** qui s’affiche, cliquez sur **DS1_V2 Standard**, puis sur **Sélectionner**.
    - Dans le panneau **Paramètres** qui s’affiche, cliquez sur **Réseau virtuel**. Dans le panneau **Choisir un réseau virtuel** qui s’affiche, cliquez sur **myVnet**.
    - Dans le panneau **Paramètres**, cliquez sur **Sous-réseau**. Dans le panneau **Choisir un sous-réseau** qui s’affiche, cliquez sur **DMZ**. 
    - Conservez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK**.
    - Dans le panneau **Créer** qui s’affiche, cliquez sur **Créer**. Le déploiement de la machine virtuelle prend quelques minutes.

    > [!NOTE]
    > Outre la création de la machine virtuelle, par défaut, le portail Azure crée une adresse IP publique qu’il assigne à la machine virtuelle. Si vous déployez la machine virtuelle dans un environnement de production, vous pouvez choisir ne pas assigner d’adresse IP publique à la machine virtuelle. Au lieu de cela, vous pouvez accéder à l’appliance virtuelle réseau à partir de toute machine virtuelle au sein du réseau virtuel. Pour en savoir plus sur les adresses IP publiques, voir [Créer, modifier ou supprimer une adresse IP publique](virtual-network-public-ip-address.md).

7. Assignez une adresse IP privée statique et activez le transfert IP pour l’interface réseau que le portail a créée à l’étape précédente. 
    - Dans le champ **Rechercher des ressources** en haut de la page, entrez *myVm-Nva*.
    - Quand la mention **myVm-Nva** apparaît dans les résultats de recherche, cliquez dessus.
    - Sous **Paramètres** sur le côté gauche, cliquez sur **Mise en réseau**.
    - Sous **myVm-Nva - Interfaces réseau**, cliquez sur le nom de l’interface réseau. Le nom est **myvm-nva***X*, où *X* est un numéro attribué par le portail.
    - Cliquez sur **Configurations IP** sous **Paramètres** pour l’interface réseau, comme illustré dans l’image suivante :

        ![Paramètres d’interface réseau](./media/create-user-defined-route/network-interface-settings.png)
        
    - Pour le paramètre **Transfert IP**, cliquez sur **Activé**, puis sur **Enregistrer**. Le transfert IP doit être activé pour chaque interface réseau qui reçoit du trafic non adressé à une adresse IP qui lui est assignée. L’activation du transfert IP désactive la vérification de la source ou de la destination d’Azure pour l’interface réseau.
    - Dans la liste des configurations IP, cliquez sur **ipconfig1**.
    - Sous **ipconfig1**, pour l’**Affectation** de l’adresse IP privée, cliquez sur **Statique**, comme illustré dans l’image suivante :

        ![Configuration IP](./media/create-user-defined-route/ip-configuration.png)
    - Comme indiqué dans l’image précédente, sous **Adresse IP**, dans **Paramètres d’adresse IP privée**, entrez *10.0.2.4*. L’affectation d’une adresse IP statique à l’interface réseau garantit que l’adresse ne change pas pendant toute la durée de vie de la machine virtuelle à laquelle l’interface réseau est attachée. L’adresse entrée n’est actuellement assignée à une autre ressource dans le sous-réseau de périmètre (DMZ) dans lequel se trouve l’interface réseau. 
    - Pour enregistrer la configuration, sous **ipconfig1**, cliquez sur **Enregistrer**. Ne fermez pas la zone ipconfig1 tant que vous n’avez pas reçu, dans le portail, de notification indiquant que l’interface réseau a été enregistrée.
 
8. Créez deux tables de routage. Les tables de routage peuvent contenir de zéro à plusieurs itinéraires :

    - Sur le côté gauche du portail, cliquez sur **+ Nouveau** > **Réseau** > **Table de routage**.
    - Sous **Créer une table de routage**, entrez les valeurs suivantes, puis cliquez sur **Créer** :

        |Paramètre|Valeur|
        |---|---|
        |NOM|myRouteTable-Publique|
        |Abonnement|Sélectionnez votre abonnement|
        |Groupe de ressources|Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**|
        |Lieu|Est des États-Unis|
    
    - Réexécutez les sous-étapes précédentes de l’étape 8, mais nommez la table de routage *myRouteTable-Privée*.
9. Ajoutez des itinéraires à la table de routage *myRouteTable-Publique*, puis associez la table de routage au sous-réseau *Public* :

    - Dans la zone **Rechercher des ressources** en haut du portail, entrez *myRouteTable-Publique*. Cliquez sur **myRouteTable-Publique** lorsque cette mention apparaît dans les résultats de la recherche.
    - Sous **myRouteTable Publique**, dans la liste des **Paramètres**, cliquez sur **Routes**.
    - Sous **myRouteTable-Publique - Routes**, cliquez sur **+ Ajouter**.
    -  Par défaut, Azure route le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Créez un itinéraire pour modifier le routage par défaut d’Azure, de sorte que le trafic en provenance du sous-réseau *Public* soit routé via l’appliance virtuelle réseau, plutôt que directement vers le sous-réseau *Privé*. Dans le panneau **Ajouter un itinéraire** qui s’affiche, entrez les valeurs suivantes, puis cliquez sur **OK** :

        |Paramètre|Valeur|Explication|
        |---|---|---|
        |Nom de l’itinéraire|ToPrivateSubnet|Cet itinéraire achemine le trafic vers le sous-réseau privé via l’appliance virtuelle réseau.|
        |Préfixe de l’adresse|10.0.1.0/24| Tout trafic envoyé à des adresses utilisant ce préfixe d’adresse (10.0.1.0 - 10.0.1.254) est acheminé vers l’appliance virtuelle réseau.|
        |Type de tronçon suivant| Sélectionnez **Appliance virtuelle**.||
        |adresse de tronçon suivant|10.0.2.4| Adresse IP privée statique de l’interface réseau attachée à l’appliance virtuelle réseau. Le seul type de tronçon pour lequel vous pouvez spécifier une adresse est **Appliance virtuelle**.|

    - Sous **myRouteTable-Publique**, sous **Paramètres**, cliquez sur **Sous-réseaux**. 
    - Sous **myRouteTable-Public - Sous-réseaux**, cliquez sur **+ Associer**.
    - Sous **Associer un sous-réseau**, cliquez sur **Réseau virtuel**, puis sur **myVnet**.
    - Sous **Associer un sous-réseau**, cliquez sur **Sous-réseau**, puis, sous **Choisir un sous-réseau**, cliquez sur **Public**. 
    - Pour enregistrer la configuration, sous **Associer un sous-réseau**, cliquez sur **OK**. Un sous-réseau peut avoir zéro ou une table de routage associée.
10. Répétez l’étape 9, en recherchant **myRouteTable-Privée**, en créant un itinéraire avec les paramètres suivants, puis en associant la table de routage au sous-réseau **Privé** du réseau virtuel **myVnet** :

    |Paramètre|Valeur|Explication|
    |---|---|---|
    |Nom de l’itinéraire|ToPublicSubnet|Cet itinéraire achemine le trafic vers le sous-réseau public via l’appliance virtuelle réseau.|
    |Préfixe de l’adresse|10.0.0.0/24| Tout trafic envoyé à des adresses utilisant ce préfixe d’adresse (10.0.0.0 - 10.0.1.254) est acheminé vers l’appliance virtuelle réseau.|
    |Type de tronçon suivant| Sélectionnez **Appliance virtuelle**.||
    |adresse de tronçon suivant|10.0.2.4||

    Le trafic réseau entre les ressources des sous-réseaux privé et public transite par l’appliance virtuelle réseau. 
11. **Facultatif :** créez une machine virtuelle dans les sous-réseaux public et privé, et validez le fait que la communication entre les machines virtuelles est acheminée via l’appliance virtuelle réseau en procédant de la manière décrite dans [Valider le routage](#validate-routing). 
12. **Facultatif :** supprimez les ressources que vous avez créées dans ce didacticiel en procédant de la manière décrite dans [Supprimer des ressources](#delete-resources).

## <a name="validate-routing"></a>Valider le routage

1. Si ce n’est pas encore fait, suivez les étapes décrites dans [Créer des itinéraires et une appliance virtuelle réseau](#create-routes-and-network-virtual-appliance).
2. Cliquez sur le bouton **Essayez** dans la zone qui suit, ce qui a pour effet d’ouvrir Azure Cloud Shell. Si vous y êtes invité, connectez-vous à Azure à l’aide votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud Shell est un interpréteur de commandes Bash gratuit, avec l’interface de ligne de commande Azure préinstallée. 

    Les scripts suivants créent deux machines virtuelles, l’une dans le sous-réseau *Public*, et l’autre dans le sous-réseau *Privé*. Les scripts permettent également le transfert IP pour l’interface réseau au sein du système d’exploitation de l’appliance virtuelle réseau pour permettre au système d’exploitation de router le trafic via l’interface réseau. Une appliance virtuelle réseau de production inspecte généralement le trafic avant de le router mais, dans ce didacticiel, l’appliance virtuelle réseau route simplement le trafic sans l’inspecter. 

    Cliquez sur le bouton **Copier** dans les scripts **Linux** ou **Windows** qui suivent, et collez le contenu du script dans un éditeur de texte. Modifiez le mot de passe dans la variable *adminPassword*, puis collez le script dans Azure Cloud Shell. Exécutez le script pour le système d’exploitation que vous avez sélectionné lorsque vous avez créé l’appliance virtuelle réseau à l’étape 6 de la procédure [Créer des itinéraires et une appliance virtuelle réseau](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Validez la communication entre les machines virtuelles des sous-réseaux public et privé. 

    - Ouvrez une connexion [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou [Bureau à distance](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) à l’adresse IP publique de la machine virtuelle *myVm-Public*.
    - À partir d’une invite de commandes sur la machine virtuelle *myVm-Public*, entrez `ping myVm-Private`. Vous recevez des réponses parce que l’appliance virtuelle réseau route le sous-réseau public vers le sous-réseau privé.
    - À partir de la machine virtuelle *myVm-Public*, exécutez une commande de suivi de l’itinéraire entre les machines virtuelles des sous-réseaux public et privé. Entrez la commande appropriée qui suit, selon le système d’exploitation installé sur les machines virtuelles des sous-réseaux Public et Privé :
        - **Windows** : à partir d'une invite de commandes, exécutez la commande `tracert myvm-private`.
        - **Ubuntu** : exécutez la commande `tracepath myvm-private`.
      Le trafic transite par 10.0.2.4 (l’appliance virtuelle réseau) avant d’atteindre 10.0.1.4 (la machine virtuelle dans le sous-réseau Privé). 
    - Exécutez les étapes précédentes en vous connectant à la machine virtuelle *myVm-Private* et en effectuant un test ping sur la machine virtuelle *myVm-Public*. La commande de suivi de l’itinéraire montre la communication transitant via 10.0.2.4 avant d’atteindre 10.0.0.4 (la machine virtuelle dans le sous-réseau Public).

      > [!NOTE]
      > Les étapes précédentes vous permettent de confirmer le routage entre les adresses IP privées Azure. Si vous voulez transférer ou rediriger via proxy le trafic vers des adresses IP publiques par le biais d’une appliance virtuelle réseau :
      > - L’application doit fournir la traduction d’adresses réseau ou la fonctionnalité de proxy. Si vous utilisez la traduction d’adresses réseau, l’appliance doit convertir l’adresse IP source en sa propre adresse, puis transférer cette requête à l’adresse IP publique. Si l’appliance a traduit l’adresse source ou redirige via proxy, Azure traduit l’adresse IP privée de l’appliance virtuelle réseau en adresse IP publique. Pour plus d’informations sur les différentes méthodes qu’Azure utilise pour traduire des adresses IP privées en adresses IP publiques, consultez [Présentation des connexions sortantes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Itinéraire supplémentaire dans la table de routage tel que le préfixe : 0.0.0.0/0, le type de tronçon suivant VirtualAppliance et l’adresse IP de tronçon suivant 10.0.2.4 (dans l’exemple de script précédent).
      >
    - **Facultativement** : pour valider le tronçon suivant entre deux machines virtuelles dans Azure, utilisez la fonctionnalité Tronçon suivant d’Azure Network Watcher. Avant d’utiliser Network Watcher, vous devez [créer une instance Azure Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour la région où vous souhaitez l’utiliser. Ce didacticiel utilise la région Est des États-Unis. Une fois que vous avez activé une instance Network Watcher pour la région, entrez la commande suivante pour consulter les informations du tronçon suivant entre les machines virtuelles des sous-réseaux Public et Privé :
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Le résultat renvoie *10.0.2.4* pour **nextHopIpAddress** et *VirtualAppliance* pour **nextHopType**. 

> [!NOTE]
> Pour illustrer les concepts de ce didacticiel, des adresses IP publiques sont attribuées aux machines virtuelles des sous-réseaux Public et Privé, et l'accès à tous les ports réseau est activé dans Azure pour les deux machines virtuelles. Lorsque vous créez des machines virtuelles pour la production, vous ne pouvez pas leur attribuer des adresses IP publiques mais vous pouvez filtrer le trafic réseau vers le sous-réseau Privé en déployant en amont une appliance virtuelle réseau ou en attribuant un groupe de sécurité réseau aux sous-réseaux, à l'interface réseau, ou aux deux. Pour en savoir plus sur les groupes de sécurité réseau, voir [Groupes de sécurité réseau](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Ce didacticiel nécessite de disposer d’un réseau virtuel avec deux sous-réseaux. Pour créer rapidement un réseau virtuel, cliquez sur le bouton **Essayez** dans la zone qui suit. Cliquer sur le bouton **Essayez** a pour effet d’ouvrir [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si Azure Cloud Shell exécute PowerShell ou un interpréteur de commandes Bash, dans cette section, l’interpréteur de commandes Bash est utilisé pour créer le réseau virtuel. L’interface de ligne de commande Azure est installée dans l'interpréteur de commandes Bash. Si Azure Cloud Shell vous y invite, connectez-vous à Azure à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p). Pour créer le réseau virtuel utilisé dans ce didacticiel, cliquez sur le bouton **Copier** dans la zone suivante, puis collez le script dans Azure Cloud Shell :

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Pour en savoir plus sur la façon d’utiliser le portail, PowerShell ou un modèle Azure Resource Manager afin de créer un réseau virtuel, voir [Créer un réseau virtuel](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Supprimer des ressources

Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources créées afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient. Dans portail ouvert, procédez comme suit :

1. Dans le champ de recherche du portail, entrez **myResourceGroup**. Dans les résultats de la recherche, cliquez sur **myResourceGroup**.
2. Dans le panneau **myResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez **myResourceGroup**, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

- Créez une [appliance virtuelle réseau hautement disponible](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Souvent, plusieurs interfaces réseau et adresses IP sont attribuées aux appliances virtuelles réseau. Découvrez comment [ajouter des interfaces réseau à une machine virtuelle existante](virtual-network-network-interface-vm.md#vm-add-nic) et [ajouter des adresses IP à une interface réseau existante](virtual-network-network-interface-addresses.md#add-ip-addresses). Même si au moins deux interfaces réseau peuvent être attribuées à des machines virtuelles, quelle que soit leur taille, chaque taille de machine virtuelle prend en charge un nombre maximal d’interfaces réseau. Pour connaître le nombre d’interfaces réseau pris en charge par chaque la taille de machine virtuelle, consultez les tailles de machine virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Créez un itinéraire défini par l’utilisateur pour forcer le trafic local via une [connexion VPN site à site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
