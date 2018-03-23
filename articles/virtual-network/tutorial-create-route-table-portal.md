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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Acheminer le trafic réseau avec une table de routage à l’aide du portail Azure

Par défaut, Azure achemine automatiquement le trafic entre tous les sous-réseaux au sein d’un réseau virtuel. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez acheminer le trafic entre des sous-réseaux via un pare-feu. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Associer une table de routage à un sous-réseau de réseau virtuel
> * Tester le routage
> * Résoudre les problèmes de routage

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-route-table"></a>Créer une table de routage

Par défaut, Azure achemine le trafic entre tous les sous-réseaux dans un réseau virtuel. Pour en savoir plus sur les itinéraires par défaut d’Azure, consultez [Itinéraires système](virtual-networks-udr-overview.md). Pour remplacer le routage par défaut d’Azure, vous créez une table de routage qui contient des itinéraires et associez la table de routage à un sous-réseau de réseau virtuel.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Table de routage**.
3. Spécifiez votre **abonnement**, sélectionnez ou entrez les informations suivantes, puis choisissez **Créer** :
 
    ![Créer une table de routage](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Créer un itinéraire

Une table de routage peut contenir plusieurs itinéraires ou aucun. 

1. Dans le champ *Rechercher des ressources, services et documents* en haut du portail, commencez à saisir *myRouteTablePublic*. Quand **myRouteTablePublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
2. Sous **PARAMÈTRES**, sélectionnez **Itinéraires**, puis **+ Ajouter**, comme indiqué dans l’image suivante :

    ![Ajouter un itinéraire](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. Sous **Ajouter un itinéraire**, sélectionnez ou saisissez les informations suivantes, puis cliquez sur **OK** :
    - **Nom de l’itinéraire** : *ToPrivateSubnet*
    - **Préfixe de l’adresse** : 10.0.1.0/24
    - **Type de tronçon suivant** : sélectionnez **Appliance virtuelle**.
    - **Adresse de tronçon suivant** : 10.0.2.4

    L’itinéraire dirige tout le trafic destiné au préfixe d’adresse 10.0.1.0/24 via une appliance virtuelle réseau avec l’adresse IP 10.0.2.4. L’appliance virtuelle réseau et le sous-réseau avec le préfixe d’adresse spécifié sont créés à des étapes ultérieures. L’itinéraire remplace le routage par défaut d’Azure, qui achemine directement le trafic entre les sous-réseaux. Chaque itinéraire spécifie un type de tronçon suivant. Le type de tronçon suivant indique à Azure comment acheminer le trafic. Dans cet exemple, le type de tronçon suivant est *VirtualAppliance*. Pour en savoir plus sur tous les types de tronçons suivants disponibles dans Azure et quand les utiliser, consultez [Types de tronçons suivants](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Pour pouvoir associer une table de routage à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau :

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Sous **Créer un réseau virtuel**, sélectionnez ou entrez les informations suivantes, puis choisissez **Créer** :
    
    - **Nom** : *myVirtualNetwork*
    - **Espace d’adressage** : *10.0.0.0/16*
    - **Abonnement** : sélectionnez votre abonnement.
    - **Groupe de ressources** : sélectionnez **Utiliser existant**, puis **myResourceGroup**.
    - **Emplacement** : sélectionnez *Est des États-Unis*
    - **Nom du sous-réseau** : *Public*
    - **Plage d’adresses** : *10.0.0.0/24*

4. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, commencez à taper *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
5. Ajoutez deux sous-réseaux supplémentaires au réseau virtuel. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**, comme indiqué dans l’image suivante :

    ![Ajouter un sous-réseau](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Sélectionnez ou saisissez les informations suivantes, puis cliquez sur **OK** :
    - **Nom** : Private
    - **Espace d’adressage** : *10.0.1.0/24*

7. Effectuez les étapes 5 et 6, en fournissant les informations suivantes :
    - **Nom** : DMZ
    - **Espace d’adressage** : *10.0.2.0/24*

Vous pouvez associer une table de routage à plusieurs sous-réseaux, ou aucun. Un sous-réseau peut avoir zéro ou une table de routage associée. Le trafic sortant d’un sous-réseau est acheminé selon les itinéraires par défaut d’Azure et les itinéraires personnalisés que vous avez ajoutés à une table de routage associée à un sous-réseau. Associez la table de routage *myRouteTablePublic* au sous-réseau *Public* :

1. La zone **myVirtualNetwork - Sous-réseaux** s’affiche une fois que vous avez effectué l’étape précédente. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **Public**.
2. Comme indiqué dans l’image suivante, sélectionnez **Table de routage**, puis **MyRouteTablePublic**.

    ![Associer une table de routage](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Sélectionnez **Enregistrer**.

## <a name="test-routing"></a>Tester le routage

Pour tester le routage, vous allez créer une machine virtuelle qui sert d’appliance virtuelle réseau par laquelle passe l’itinéraire que vous avez créé lors d’une étape précédente. Après avoir créé l’appliance virtuelle réseau, vous allez déployer une machine virtuelle dans les sous-réseaux *Public* et *Private*. Vous allez ensuite acheminer le trafic entre le sous-réseau *Public* et le sous-réseau *Private* via l’appliance virtuelle réseau.

### <a name="create-a-network-virtual-appliance"></a>Créer une appliance virtuelle réseau

Lors d’une étape précédente, vous avez créé un itinéraire qui indiquait une appliance virtuelle réseau comme type de tronçon suivant. Une machine virtuelle exécutant une application réseau est souvent appelée appliance virtuelle réseau. Dans les environnements de production, l’appliance virtuelle réseau que vous déployez est souvent une machine virtuelle préconfigurée. Plusieurs appliances virtuelles réseau sont disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Dans cet article, une machine virtuelle de base est créée.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. Vous pouvez sélectionner différents systèmes d’exploitation, mais les étapes restantes partent du principe que vous avez sélectionné **Windows Server 2016 Datacenter**. 
3. Sélectionnez ou saisissez les informations suivantes pour **Informations de base**, puis sélectionnez **OK** :
    - **Nom** : *myVmNva*
    - **Groupe de ressources** : sélectionnez **Utiliser existant**, puis *myResourceGroup*.
    - **Emplacement** : sélectionnez *États-Unis de l’Est*.

    Le **nom d’utilisateur** et le **mot de passe** que vous entrez vous serviront lors d’une étape ultérieure. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). L’**Emplacement** et l’**Abonnement** choisis doivent être identiques à l’emplacement et l’abonnement du réseau virtuel. Il n’est pas nécessaire de sélectionner le même groupe de ressources dans lequel a été créé le réseau virtuel,mais il est tout de même sélectionné dans ce didacticiel.
4. Sélectionnez une taille de machine virtuelle sous **Choisir une taille**.
5. Sélectionnez ou saisissez les informations suivantes pour **Paramètres**, puis sélectionnez **OK** :
    - **Réseau virtuel** : vérifiez que **myVirtualNetwork** est sélectionné. Sinon, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork** sous **Choisir un réseau virtuel**.
    - **Sous-réseau** : sélectionnez **Sous-réseau**, puis **DMZ** sous **Choisir un sous-réseau**.
    - **Adresse IP publique** : sélectionnez **Adresse IP publique**, puis **Aucune** sous **Choisir une adresse IP publique**. Aucune adresse IP publique n’est attribuée à cette machine virtuelle, car elle ne sera pas connectée à Internet.
6. Sous **Créer** dans le **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle.

La création de la machine virtuelle prend quelques minutes. Ne passez pas à l’étape suivante tant qu’Azure n’a pas terminé la création de la machine virtuelle et ouvre une fenêtre contenant des informations sur la machine virtuelle.

En même temps que la machine virtuelle, Azure a également créé une [interface réseau](virtual-network-network-interface.md) dans le sous-réseau *DMZ* et attaché cette interface réseau à la machine virtuelle. Le transfert IP doit être activé pour chaque interface réseau Azure qui transfère le trafic destiné à n’importe quelle adresse IP qui n’est pas affectée à l’interface réseau.

1. Dans la fenêtre qui s’est ouverte après la création de la machine virtuelle, sous **PARAMÈTRES**, sélectionnez **Mise en réseau**, puis **myvmnva158** (l’interface réseau Azure créée pour votre machine virtuelle a un numéro différent après **myvmnva**), comme illustré dans l’image suivante :

    ![Mise en réseau de machine virtuelle](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    Quand vous avez créé l’appliance virtuelle réseau dans le sous-réseau *DMZ*, Azure a automatiquement créé l’interface réseau, l’a attachée à la machine virtuelle et lui a affecté l’adresse IP privée *10.0.2.4*. 

2. Sous **PARAMÈTRES**, sélectionnez **Configurations IP**, **activez** le **transfert IP**, puis choisissez **Enregistrer**, comme illustré dans l’image suivante :

    ![Activer le transfert IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel pour pouvoir confirmer que le trafic provenant du sous-réseau *Public* est acheminé vers le sous-réseau *Private* via l’appliance virtuelle réseau à une étape ultérieure.

Effectuez les étapes 1 à 6 de la section [Créer une appliance virtuelle réseau](#create-a-network-virtual-appliance). Utilisez les mêmes paramètres aux étapes 3 et 5, avec les exceptions suivantes :

|Machine virtuelle   |NOM      |Sous-réseau      | Adresse IP publique     |
|---------         |--------- | -----------|---------              |
|Machine virtuelle 1 | myVmWeb  | Public     | Accepter la valeur par défaut du portail |
|Machine virtuelle 2 | myVmMgmt | Privé    | Accepter la valeur par défaut du portail |

Vous pouvez créer la machine virtuelle *myVmMgmt* pendant qu’Azure crée la machine virtuelle *myVmWeb*. Attendez qu’Azure ait créé les deux machines virtuelles pour passer aux étapes suivantes.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Acheminer le trafic via une appliance virtuelle réseau

1. Dans le champ *Recherche* en haut du portail, commencez à taper *myVmMgmt*. Quand **myVmMgmt** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Créez une connexion à distance dans la machine virtuelle *myVmMgmt* en sélectionnant **Connecter**, comme montré dans l’image suivante :

    ![Connexion à la machine virtuelle](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.
4. Entrez le nom d’utilisateur et le mot de passe choisis lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** pour poursuivre le processus de connexion.
6. À une étape ultérieure, la commande tracert.exe est utilisée pour tester le routage. Tracert utilise le protocole IMCP, qui est bloqué par le pare-feu Windows. Autorisez le protocole IMCP dans le pare-feu Windows en entrant la commande suivante depuis une invite de commandes :

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Bien que cet article utilise tracert pour tester le routage, il n’est pas recommandé d’autoriser le protocole IMCP dans le pare-feu Windows lors de déploiements en production.
7. Vous avez activé le transfert IP dans Azure pour l’interface réseau de la machine virtuelle dans [Activer le transfert IP](#enable-ip-forwarding). Dans la machine virtuelle, le système d’exploitation ou une application en cours d’exécution au sein de la machine virtuelle doit également être en mesure de transférer le trafic réseau. Quand vous déployez une appliance virtuelle réseau dans un environnement de production, l’appliance filtre, enregistre ou exécute généralement une autre fonction avant de transférer le trafic. Dans cet article, toutefois, le système d’exploitation transfère simplement tout le trafic qu’il reçoit. Activez le transfert IP au sein du système d’exploitation de *myVmNva* en effectuant les étapes suivantes à partir de la machine virtuelle *myVmMgmt* :

    Établissez une connexion Bureau à distance à la machine virtuelle *myVmNva* avec la commande suivante à partir d’une invite de commandes :

    ``` 
    mstsc /v:myvmnva
    ```
    
    Pour activer le transfert IP au sein du système d’exploitation, entrez la commande suivante dans PowerShell :

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Redémarrez la machine virtuelle, ce qui déconnecte également la session Bureau à distance.
8. Tout en conservant la connexion à la machine virtuelle *myVmMgmt*, après le redémarrage de la machine virtuelle *myVmNva*, créez une session Bureau à distance sur la machine virtuelle *myVmWeb* avec la commande suivante :

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Autorisez le protocole IMCP dans le pare-feu Windows en entrant la commande suivante depuis une invite de commandes :

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. Pour tester le routage du trafic réseau vers la machine virtuelle *myVmMgmt* à partir de la machine virtuelle *myVmWeb*, entrez la commande suivante à partir d’une invite de commandes :

    ```
    tracert myvmmgmt
    ```

    La réponse ressemble à ce qui suit :
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Vous voyez que le premier tronçon est 10.0.2.4, ce qui correspond à l’adresse IP privée de l’appliance virtuelle réseau. Le second tronçon est 10.0.1.4, l’adresse IP privée de la machine virtuelle *myVmMgmt*. L’itinéraire ajouté à la table de routage *myRouteTablePublic* et associé au sous-réseau *Public* a contraint Azure à acheminer le trafic via l’appliance virtuelle réseau et non directement au sous-réseau *Private*.
10.  Fermez la session Bureau à distance sur la machine virtuelle *myVmWeb*. Cela n’interrompt pas la connexion à la machine virtuelle *myVmMgmt*.
11. Pour tester le routage du trafic réseau vers la machine virtuelle *myVmWeb* à partir de la machine virtuelle *myVmMgmt*, entrez la commande suivante à partir d’une invite de commandes :

    ```
    tracert myvmweb
    ```

    La réponse ressemble à ce qui suit :

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Vous voyez que le trafic est acheminé directement de la machine virtuelle *myVmMgmt* vers la machine virtuelle *myVmWeb*. Par défaut, Azure achemine directement le trafic entre les sous-réseaux.
12. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Résoudre les problèmes de routage

Comme vous l’avez appris lors des étapes précédentes, Azure applique les itinéraires par défaut, que vous pouvez éventuellement remplacer par vos propres itinéraires. Parfois, le trafic peut ne pas être acheminé comme vous le voulez. Vous pouvez utiliser la fonctionnalité de [tronçon suivant](../network-watcher/network-watcher-check-next-hop-portal.md) d’Azure Network Watcher pour déterminer le routage du trafic entre deux machines virtuelles dans Azure. 

1. Dans le champ *Recherche* en haut du portail, commencez à saisir *Network Watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
2. Sous **OUTILS DE DIAGNOSTIC RÉSEAU**, sélectionnez **Tronçon suivant**.
3. Pour tester le routage du trafic à partir de la machine virtuelle *myVmWeb* (10.0.0.4) vers la machine virtuelle *myVmMgmt* (10.0.1.4), sélectionnez votre abonnement, entrez les informations affichées dans l’image suivante (le nom de votre **interface réseau** est légèrement différent), puis sélectionnez **Tronçon suivant** :

    ![Tronçon suivant](./media/tutorial-create-route-table-portal/next-hop.png)  

    La sortie**Résultat** vous informe que l’adresse IP du tronçon suivant pour le trafic entre *myVmWeb* et *myVmMgmt* est 10.0.2.4 (la machine virtuelle *myVmNva*), que le type de tronçon suivant est *VirtualAppliance* et que la table de routage à l’origine du routage est *myRouteTablePublic*.

Les itinéraires effectifs pour chaque interface réseau sont une combinaison des itinéraires par défaut d’Azure et des itinéraires que vous définissez. Pour afficher tous les itinéraires effectifs pour une interface réseau dans une machine virtuelle, procédez comme suit :

1. Dans le champ *Recherche* en haut du portail, commencez à saisir *myVmWeb*. Quand **myVmWeb** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
2. Sous **PARAMÈTRES**, sélectionnez **Mise en réseau**, puis **myvmweb369** (l’interface réseau Azure créée pour votre machine virtuelle a un numéro différent après **myvmweb**).
3. Sous **SUPPORT + DÉPANNAGE**, sélectionnez **Itinéraires effectifs**, comme indiqué dans l’image suivante :

    ![Itinéraires effectifs](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Vous voyez les itinéraires par défaut d’Azure et l’itinéraire que vous avez ajouté à la table de routage *myRouteTablePublic*. Pour plus d’informations sur la façon dont Azure sélectionne un itinéraire à partir de la liste des itinéraires, consultez [Comment Azure choisit un itinéraire](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau qui a acheminé le trafic d’un sous-réseau public vers un sous-réseau privé. Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, les ressources pour certains services Azure PaaS ne peuvent pas être déployées dans un réseau virtuel. Cependant, vous pouvez toujours restreindre l’accès aux ressources de certains services Azure PaaS au trafic provenant uniquement d’un sous-réseau de réseau virtuel. Passez au didacticiel suivant pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
