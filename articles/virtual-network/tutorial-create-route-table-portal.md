---
title: Acheminer le trafic réseau - Portail Azure | Microsoft Docs
description: Découvrez comment acheminer le trafic réseau avec une table de routage à l’aide du portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 45b07c6ca86802d0cc3e773234e1122ba7bd9ea7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Acheminer le trafic réseau avec une table de routage à l’aide du portail Azure

Par défaut, Azure achemine automatiquement le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez router le trafic entre des sous-réseaux via une appliance virtuelle réseau (NVA). Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Créer un réseau virtuel comprenant plusieurs sous-réseaux
> * Associer une table de routage à un sous-réseau
> * Créer une appliance NVA qui route le trafic
> * Déployer des machines virtuelles sur différents sous-réseaux
> * Router le trafic d’un sous-réseau vers un autre via une NVA

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="create-a-route-table"></a>Créer une table de routage

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Table de routage**.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myRouteTablePublic|
    |Abonnement| Sélectionnez votre abonnement.|
    |Groupe de ressources | Sélectionnez **Créer** et entrez *myResourceGroup*.|
    |Lieu|Est des États-Unis|
 
    ![Créer une table de routage](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Créer un itinéraire

1. Dans le champ *Rechercher des ressources, services et documents* en haut du portail, commencez à saisir *myRouteTablePublic*. Quand **myRouteTablePublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
2. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**, puis **+ Ajouter**, comme indiqué dans l’image suivante :

    ![Ajouter un itinéraire](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. Sous **Ajouter un itinéraire**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |Nom de l’itinéraire|ToPrivateSubnet|
    |Préfixe de l’adresse| 10.0.1.0/24|
    |Type de tronçon suivant | Sélectionnez **Appliance virtuelle**.|
    |adresse de tronçon suivant| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Avant de pouvoir associer une table d’itinéraires à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau :

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Sous **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myVirtualNetwork|
    |Espace d’adressage| 10.0.0.0/16|
    |Abonnement | Sélectionnez votre abonnement.|
    |Groupe de ressources|Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    |Lieu|Sélectionnez *Est des États-Unis*.|
    |Nom du sous-réseau|Public|
    |Plage d’adresses|10.0.0.0/24|
    
4. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, commencez à taper *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
5. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**, comme indiqué dans l’image suivante :

    ![Ajouter un sous-réseau](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Sélectionnez ou saisissez les informations suivantes, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|Privé|
    |Espace d’adressage| 10.0.1.0/24|

7. Effectuez les étapes 5 et 6, en fournissant les informations suivantes :

    |Paramètre|Valeur|
    |---|---|
    |NOM|DMZ|
    |Espace d’adressage| 10.0.2.0/24|

8. La zone **myVirtualNetwork - Sous-réseaux** s’affiche une fois que vous avez effectué l’étape précédente. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **Public**.
9. Comme indiqué dans l’image suivante, sélectionnez **Table d’itinéraires**, puis **MyRouteTablePublic** et sélectionnez **Enregistrer** :

    ![Associer une table de routage](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Créer une NVA

Une NVA est une machine virtuelle qui exécute une fonction réseau, telle que le routage, la fonction de pare-feu ou l’optimisation WAN.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. Vous pouvez sélectionner différents systèmes d’exploitation, mais les étapes restantes partent du principe que vous avez sélectionné **Windows Server 2016 Datacenter**. 
3. Sélectionnez ou saisissez les informations suivantes pour **Informations de base**, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |---|---|
    |NOM|myVmNva|
    |Nom d'utilisateur|Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe|Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Groupe de ressources| Sélectionnez **Utiliser l’existant**, puis *myResourceGroup*.|
    |Lieu|Sélectionnez **Est des États-Unis**.|
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Sélectionnez ou saisissez les informations suivantes pour **Paramètres**, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|myVirtualNetwork : si cette option est sélectionnée, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork** sous **Choisir un réseau virtuel**.|
    |Sous-réseau|Sélectionnez **Sous-réseau**, puis **DMZ** sous **Choisir un sous-réseau**. |
    |Adresse IP publique| Sélectionnez **Adresse IP publique** et sélectionnez **Aucune** sous **Choisir une adresse IP publique**. Aucune adresse IP publique n’est assignée à cette machine virtuelle, car elle ne sera pas connectée à Internet.
6. Sous **Créer** dans **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.

    La création de la machine virtuelle ne nécessite que quelques minutes. Ne passez pas à l’étape suivante tant qu’Azure n’a pas terminé la création de la machine virtuelle et n’a pas ouvert une boîte contenant des informations sur la machine virtuelle.

7. Dans la boîte qui s’est ouverte pour la machine virtuelle après sa création, sous **PARAMÈTRES**, sélectionnez **Mise en réseau**, puis **myvmnva158** (l’interface réseau Azure créée pour votre machine virtuelle a un numéro différent après **myvmnva**), comme illustré dans l’image suivante :

    ![Mise en réseau de machines virtuelles](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Pour qu’une interface réseau soit en mesure de transférer le trafic réseau qui lui est envoyé, mais qui n’est pas destiné à sa propre adresse IP, le transfert IP doit être activé pour l’interface réseau. Sous **PARAMÈTRES**, sélectionnez **Configurations IP**, **activez** le **transfert IP**, puis choisissez **Enregistrer**, comme illustré dans l’image suivante :

    ![Activer le transfert IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel de manière à pouvoir valider que le trafic provenant du sous-réseau *Public* est routé vers le sous-réseau *Privé* via la NVA ultérieurement. Suivez les étapes 1 à 6 de [Créer une NVA](#create-a-network-virtual-appliance). Utilisez les mêmes paramètres aux étapes 3 et 5, avec les exceptions suivantes :

|Nom de la machine virtuelle      |Sous-réseau      | Adresse IP publique     |
|--------- | -----------|---------              |
| myVmPublic  | Public     | Accepter la valeur par défaut du portail |
| myVmPrivate | Privé    | Accepter la valeur par défaut du portail |

Vous pouvez créer la machine virtuelle *myVmPrivate* pendant qu’Azure crée la machine virtuelle *myVmPublic*. Attendez qu’Azure ait fini de créer les deux machines virtuelles pour passer aux étapes suivantes.

## <a name="route-traffic-through-an-nva"></a>Router le trafic via une NVA

1. Dans la boîte *Rechercher* en haut du portail, commencez à saisir *myVmPrivate*. Quand la machine virtuelle **myVmPrivate** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Créez une connexion Bureau à distance avec la machine virtuelle *myVmPrivate* en sélectionnant **Connecter**, comme indiqué dans l’image suivante :

    ![Se connecter à une machine virtuelle ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
4. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
6. À une étape ultérieure, la commande tracert.exe est utilisée pour tester le routage. Tracert utilise le protocole ICMP (Internet Control Message Protocol), qui est refusé via le Pare-feu Windows. Autorisez le protocole ICMP (Internet Control Message Protocol) dans le pare-feu Windows en entrant la commande suivante de PowerShell :

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Bien que cet article utilise tracert pour tester le routage, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements en production.
7. Vous avez activé le transfert d’IP dans Azure pour l’interface réseau de la machine virtuelle dans [Activer le transfert IP](#enable-ip-forwarding). Sur la machine virtuelle, le système d’exploitation ou une application exécutée dans la machine virtuelle, doit également pouvoir transférer le trafic réseau. Activez le transfert IP dans le système d’exploitation de la machine virtuelle *myVmNva* en effectuant les étapes suivantes sur la machine virtuelle *myVmPrivate* :

    Bureau à distance à *myVmNva* avec la commande suivante à partir d’une invite de commandes :

    ``` 
    mstsc /v:myvmnva
    ```
    
    Pour activer le transfert IP au sein du système d’exploitation, entrez la commande suivante dans PowerShell :

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Redémarrez la machine virtuelle, ce qui va également déconnecter la session Bureau à distance.
8. Tout en conservant la connexion à la machine virtuelle *myVmPrivate*, créez une session Bureau à distance sur la machine virtuelle *myVmPublic* avec la commande suivante, une fois la machine virtuelle *myVmPublic* redémarrée :

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Autorisez le protocole ICMP (Internet Control Message Protocol) dans le pare-feu Windows en entrant la commande suivante de PowerShell :

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Pour tester le routage du trafic réseau vers la machine virtuelle *myVmPrivate* à partir de la machine virtuelle *myVmPublic*, entrez la commande suivante de PowerShell :

    ```
    tracert myVmPrivate
    ```

    La réponse ressemble à ce qui suit :
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Vous voyez que le premier tronçon est 10.0.2.4, ce qui correspond à l’adresse IP privée de l’appliance virtuelle réseau (NVA). Le second tronçon est 10.0.1.4, ce qui correspond à l’adresse IP privée de la machine virtuelle *myVmPrivate*. L’itinéraire ajouté à la table de routage *myRouteTablePublic* et associé au sous-réseau *Public* a contraint Azure à acheminer le trafic via l’appliance virtuelle réseau et non directement au sous-réseau *Private*.
10.  Fermez la session Bureau à distance sur la machine virtuelle *myVmPublic*. Cela n’interrompt pas la connexion à la machine virtuelle *myVmPrivate*.
11. Pour tester le routage du trafic réseau vers la machine virtuelle *myVmPublic* à partir de la machine virtuelle *myVmPrivate*, entrez la commande suivante à l’invite de commandes :

    ```
    tracert myVmPublic
    ```

    La réponse ressemble à ce qui suit :

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Vous pouvez voir que le trafic est routé directement de la machine virtuelle *myVmPrivate* vers la machine virtuelle *myVmPublic*. Par défaut, Azure achemine directement le trafic entre les sous-réseaux.
12. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau (NVA) simple qui a routé le trafic d’un sous-réseau public vers un sous-réseau privé. Déployez différentes NVA préconfigurées exécutant des fonctions de réseau, telles que la fonction de pare-feu ou l’optimisation WAN, à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Avant de déployer des tables de routage dans un environnement de production, il est recommandé de bien se familiariser avec le [routage dans Azure](virtual-networks-udr-overview.md), la [gestion des tables de routage](manage-route-table.md) et les [limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, les ressources pour certains services Azure PaaS ne peuvent pas être déployées dans un réseau virtuel. Cependant, vous pouvez toujours restreindre l’accès aux ressources de certains services Azure PaaS au trafic provenant uniquement d’un sous-réseau de réseau virtuel. Passez au tutoriel suivant pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
