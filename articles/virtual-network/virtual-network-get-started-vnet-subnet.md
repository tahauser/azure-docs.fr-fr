---
title: "Créer votre premier réseau virtuel Azure | Microsoft Docs"
description: "Apprenez à créer un réseau virtuel Azure (VNet), à connecter deux machines virtuelles au réseau virtuel et à se connecter aux machines virtuelles."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.openlocfilehash: 873f209bcfc6529c84c14a9288ec8222e9a3ccf7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-your-first-virtual-network"></a>Créer votre premier réseau virtuel

Apprenez à créer un réseau virtuel (VNet) avec deux sous-réseaux, à créer deux machines virtuelles et à connecter chaque machine virtuelle à l’un des sous-réseaux, comme illustré dans l’image suivante :

![Diagramme de réseau virtuel](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Un réseau virtuel Azure (VNet) est une représentation de votre propre réseau dans le cloud. Vous pouvez contrôler les paramètres de votre réseau Azure et définir les blocs d’adresses DHCP, les paramètres DNS, les stratégies de sécurité et le routage. Pour en savoir plus sur les concepts de réseau virtuel, consultez l’article [Présentation du réseau virtuel](virtual-networks-overview.md). Procédez comme suit pour créer les ressources présentées dans l’image :

1. [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet)
2. [Créer deux machines virtuelles, chacune avec une interface réseau (NIC),](#create-vms) et associer un groupe de sécurité réseau (NSG) à chaque NIC
3. [Se connecter aux machines virtuelles/à partir des machines virtuelles](#connect-to-from-vms)
4. [Supprimer toutes les ressources](#delete-resources) Vous allez être facturé pour certaines des ressources créées dans cet exercice pendant leur configuration. Pour réduire les frais, après avoir terminé l’exercice, veillez à effectuer les étapes décrites dans cette section pour supprimer les ressources créées.

Une fois que vous aurez effectué les étapes décrites dans cet article, vous aurez une connaissance élémentaire de la façon dont vous pouvez utiliser un réseau virtuel. Les étapes suivantes vous expliqueront comment utiliser les réseaux virtuels à un niveau plus approfondi.

## <a name="create-vnet"></a>Créer un réseau virtuel avec deux sous-réseaux

Pour créer un réseau virtuel avec deux sous-réseaux, effectuez les étapes suivantes. Des sous-réseaux différents sont généralement utilisés pour contrôler le flux du trafic entre sous-réseaux.

1. Connectez-vous au [portail Azure](<https://portal.azure.com>). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free). 
2. Cliquez sur **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
4.  Dans le panneau **Réseau virtuel**, laissez le modèle de déploiement *Resource Manager* sélectionné, puis cliquez sur **Créer**.
5.  Dans le **panneau Créer un réseau virtuel** qui s’affiche, entrez les valeurs suivantes, puis cliquez sur **Créer** :

    |**Paramètre**|**Valeur**|**Détails**|
    |---|---|---|
    |**Name**|*MyVNet*|Le nom doit être unique au sein du groupe de ressources.|
    |**Espace d’adressage**|*10.0.0.0/16*|Vous pouvez spécifier l’espace d’adressage de votre choix en notation CIDR.|
    |**Nom du sous-réseau**|*Front-end*|Le nom du sous-réseau doit être unique au sein du réseau virtuel.|
    |**Plage d’adresses de sous-réseau**|*10.0.0.0/24*| La plage que vous spécifiez doit exister dans l’espace d’adressage que vous avez définie pour le réseau virtuel.|
    |**Abonnement**|*[Votre abonnement]*|Sélectionnez l’abonnement dans lequel créer le réseau virtuel. Un réseau virtuel existe dans un seul abonnement.|
    |**Groupe de ressources**|**Créer :***MyRG*|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Emplacement**|*Ouest des États-Unis*| En règle générale, l’emplacement le plus proche de vous est sélectionné.|

    La création du réseau virtuel prend quelques secondes. Une fois créé, le tableau de bord du portail Azure s’affiche.

6. Une fois le réseau virtuel créé, dans le panneau **Favoris** du portail Azure, cliquez sur **Toutes les ressources**. Cliquez sur le réseau virtuel **MyVNet** dans le panneau **Toutes les ressources**. Si l’abonnement déjà sélectionné comporte plusieurs ressources, vous pouvez saisir *MyVNet* dans la case **Filtrer par nom...** pour accéder facilement au réseau virtuel.
7. Le panneau **MyVNet** s’ouvre et affiche des informations sur le réseau virtuel, comme indiqué dans l’image suivante :

    ![Diagramme de réseau virtuel](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Comme indiqué dans l’image précédente, cliquez sur **Sous-réseaux** pour afficher la liste des sous-réseaux du réseau virtuel. Le seul sous-réseau existant est le sous-réseau **Front-end** que vous avez créé à l’étape 5.
9. Dans le panneau MyVNet - Sous-réseaux, cliquez sur **+ Sous-réseau** pour créer un sous-réseau avec les informations suivantes et cliquez sur **OK** pour le créer :

    |**Paramètre**|**Valeur**|**Détails**|
    |---|---|---|
    |**Name**|*Back-end*|Le nom doit être unique au sein du réseau virtuel.|
    |**Plage d’adresses**|*10.0.1.0/24*|La plage que vous spécifiez doit exister dans l’espace d’adressage que vous avez définie pour le réseau virtuel.|
    |**Groupe de sécurité réseau** et **Table de routage**|*Aucun* (par défaut)|Les groupes de sécurité réseau (NSG) sont traités plus loin dans cet article. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).|

10. Une fois le nouveau sous-réseau ajouté sur le réseau virtuel, vous pouvez fermer le panneau **MyVNet – Sous-réseaux**, puis le panneau **Toutes les ressources**.

## <a name="create-vms"></a>Créer des machines virtuelles

Une fois le réseau virtuel et les sous-réseaux créés, vous pouvez créer les machines virtuelles. Dans cet exercice, les deux machines virtuelles exécutent le système d’exploitation Windows Server, mais elles peuvent exécuter n’importe quel système d’exploitation pris en charge par Azure, y compris plusieurs distributions différentes de Linux.

### <a name="create-web-server-vm"></a>Créer la machine virtuelle du serveur web

Pour créer la machine virtuelle du serveur web, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter**.
2. Cliquez sur **Créer**.
3. Dans le panneau **De base** qui s’affiche, entrez ou sélectionnez les valeurs suivantes et cliquez sur **OK** :

    |**Paramètre**| **Valeur**|**Détails**|
    |---|---|---|
    |**Name**|*MyWebServer*|Cette machine virtuelle fonctionne comme un serveur web auquel les ressources Internet se connectent.|
    |**Type de disque de machine virtuelle**|*SSD*|
    |**Nom d'utilisateur**|*Votre choix*|
    |**Mot de passe et Confirmer le mot de passe**|*Votre choix*|
    | **Abonnement**|*<Your subscription>*|L’abonnement doit être le même que celui sélectionné à l’étape 5 de la section [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet) de cet article. Le réseau virtuel auquel vous connectez une machine virtuelle doit exister dans le même abonnement que la machine virtuelle.|
    |**Groupe de ressources**|**Utiliser existant :** sélectionnez *MyRG*|Bien que nous utilisions le même groupe de ressources, comme nous l’avons fait pour le réseau virtuel, les ressources n’ont pas besoin de figurer dans le même groupe de ressources.|
    |**Lieu**|*Ouest des États-Unis*|L’emplacement doit être le même que celui spécifié à l’étape 5 de la section [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet) de cet article. Les machines virtuelles et les réseaux virtuels auxquels elles se connectent doivent figurer au même emplacement.|

4. Dans le panneau **Choisir une taille**, cliquez sur *DS1_V2 De base*, puis sur **Sélectionner**. Consultez l’article [Tailles des machines virtuelles Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour obtenir la liste de toutes les tailles des machines virtuelles Windows prises en charge par Azure.
5. Dans le panneau **Paramètres**, entrez ou sélectionnez les valeurs suivantes et cliquez sur **OK** :

    |**Paramètre**|**Valeur**|**Détails**|
    |---|---|---|
    |**Stockage : Utiliser des disques gérés**|*Oui*||
    |**Réseau virtuel**| Sélectionner *MyVNet*|Vous pouvez sélectionner n’importe quel réseau virtuel figurant au même emplacement que la machine virtuelle en cours de création. Pour en savoir plus sur les réseaux virtuels et les sous-réseaux, consultez l’article [Réseau virtuel](virtual-networks-overview.md).|
    |**Sous-réseau**|Sélectionner *Front-end*|Vous pouvez sélectionner n’importe quel sous-réseau figurant dans le réseau virtuel.|
    |**Adresse IP publique**|Accepter la valeur par défaut|Une adresse IP publique vous permet de vous connecter à la machine virtuelle à partir d’Internet. Pour en savoir plus sur les adresses IP publiques, consultez l’article [Adresses IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Groupe de sécurité réseau (pare-feu)**|Accepter la valeur par défaut|Cliquez sur le groupe de sécurité réseau par défaut **(nouveau) MyWebServer-nsg**, le portail créé pour afficher ses paramètres. Dans le panneau **Créer un groupe de sécurité réseau** qui s’ouvre, notez qu’une règle de trafic entrant autorise le trafic TCP/3389 (RDP) depuis n’importe quelle adresse IP source.|
    |**Toutes les autres valeurs**|Accepter les valeurs par défaut|Pour en savoir plus sur les autres paramètres, consultez l’article [À propos des machines virtuelles](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    Les groupes de sécurité réseau (NSG) vous permettent de créer des règles de trafic entrant et sortant pour le type de trafic réseau qui peut circuler vers et à partir de la machine virtuelle. Par défaut, tout le trafic entrant vers la machine virtuelle est refusé. Vous pouvez ajouter d’autres règles de trafic entrant pour TCP/80 (HTTP) et TCP/443 (HTTPS) pour un serveur web de production. Il n’existe aucune règle pour le trafic sortant car, par défaut, tout le trafic sortant est autorisé. Vous pouvez ajouter ou supprimer des règles pour contrôler le trafic conformément à vos stratégies. Lisez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md) pour en savoir plus sur les groupes de sécurité réseau.

6.  Dans le panneau **Résumé**, passez en revue les paramètres et cliquez sur **OK** pour créer la machine virtuelle. Une vignette d’état s’affiche sur le tableau de bord du portail lors de la création de la machine virtuelle. La création peut prendre plusieurs minutes. Vous n’avez pas besoin d’attendre que l’opération se termine. Pendant ce temps, vous pouvez passer à l’étape suivante.

### <a name="create-database-server-vm"></a>Créer la machine virtuelle du serveur de base de données

Pour créer la machine virtuelle du serveur de base de données, procédez comme suit :

1.  Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter**.
2.  Cliquez sur **Créer**.
3.  Dans le panneau **De base**, entrez ou sélectionnez les valeurs suivantes et cliquez sur **OK** :

    |**Paramètre**|**Valeur**|**Détails**|
    |---|---|---|
    |**Name**|*MyDBServer*|Cette machine virtuelle fait office de serveur de base de données auquel se connecte le serveur web, mais avec lequel il n’est pas possible d’établir une connexion Internet.|
    |**Type de disque de machine virtuelle**|*SSD*||
    |**Nom d'utilisateur**|Votre choix||
    |**Mot de passe et Confirmer le mot de passe**|Votre choix||
    |**Abonnement**|<Your subscription>|L’abonnement doit être le même que celui sélectionné à l’étape 5 de la section [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet) de cet article.|
    |**Groupe de ressources**|**Utiliser existant :** sélectionnez *MyRG*|Bien que nous utilisions le même groupe de ressources, comme nous l’avons fait pour le réseau virtuel, les ressources n’ont pas besoin de figurer dans le même groupe de ressources.|
    |**Lieu**|*Ouest des États-Unis*|L’emplacement doit être le même que celui spécifié à l’étape 5 de la section [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet) de cet article.|

4.  Dans le panneau **Choisir une taille**, cliquez sur *DS1_V2 De base*, puis sur **Sélectionner**.
5.  Dans le panneau **Paramètres**, entrez ou sélectionnez les valeurs suivantes et cliquez sur **OK** :

    |**Paramètre**|**Valeur**|**Détails**|
    |----|----|---|
    |**Stockage : Utiliser des disques gérés**|*Oui*||
    |**Réseau virtuel**|Sélectionner *MyVNet*|Vous pouvez sélectionner n’importe quel réseau virtuel figurant au même emplacement que la machine virtuelle en cours de création.|
    |**Sous-réseau**|Sélectionnez *Back-end* en cliquant sur la case **Sous-réseau**, puis en sélectionnant **Back-end** dans le panneau **Choisir un sous-réseau**.|Vous pouvez sélectionner n’importe quel sous-réseau figurant dans le réseau virtuel.|
    |**Adresse IP publique**|Aucun : cliquez sur l’adresse par défaut, puis cliquez sur **Aucun** dans le panneau **Choisir une adresse IP publique**.|Sans adresse IP publique, vous pouvez uniquement vous connecter à la machine virtuelle à partir d’une autre machine virtuelle connectée au même réseau virtuel. Vous ne pouvez pas vous y connecter directement depuis Internet.|
    |**Groupe de sécurité réseau (pare-feu)**|Accepter la valeur par défaut| Comme le groupe de sécurité réseau par défaut créé pour la machine virtuelle MyWebServer, ce groupe de sécurité réseau dispose également de la même règle de trafic entrant par défaut. Vous pouvez ajouter une nouvelle règle de trafic entrant pour TCP/1433 (MS SQL) pour un serveur de base de données. Il n’existe aucune règle pour le trafic sortant car, par défaut, tout le trafic sortant est autorisé. Vous pouvez ajouter ou supprimer des règles pour contrôler le trafic conformément à vos stratégies.|
    |**Toutes les autres valeurs**|Accepter les valeurs par défaut||

6.  Dans le panneau **Résumé**, passez en revue les paramètres et cliquez sur **OK** pour créer la machine virtuelle. Une vignette d’état s’affiche sur le tableau de bord du portail lors de la création de la machine virtuelle. La création peut prendre plusieurs minutes. Vous n’avez pas besoin d’attendre que l’opération se termine. Pendant ce temps, vous pouvez passer à l’étape suivante.

## <a name="review"></a>Passer en revue les ressources

Si vous avez créé un réseau virtuel et deux machines virtuelles, le portail Azure a créé pour vous plusieurs ressources supplémentaires dans le groupe de ressources MyRG. Passez en revue le contenu du groupe de ressources MyRG en procédant comme suit :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Tous les services**.
2. Saisissez *Groupes de ressources* dans la case comportant le mot *Filtre*. Cliquez sur **Groupes de ressources** dans la liste filtrée.
3. Dans le panneau **Groupes de ressources**, cliquez sur le groupe de ressources *MyRG*. Si votre abonnement comporte plusieurs groupes de ressources, saisissez *MyRG* dans la case contenant le texte *Filtrer par nom...* pour accéder rapidement au groupe de ressources MyRG.
4.  Dans le panneau **MyRG**, vous pouvez voir que le groupe de ressources contient 12 ressources, comme indiqué dans l’image suivante :

    ![Contenu du groupe de ressources](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Pour en savoir plus sur les machines virtuelles, les disques et les comptes de stockage, consultez les articles généraux [Machine virtuelle](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Disque](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Compte de stockage](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous pouvez voir les deux groupes de sécurité réseau par défaut que le portail a créé pour vous. Vous pouvez également voir que le portail a créé deux ressources d’interface réseau. Une carte réseau permet à une machine virtuelle de se connecter à d’autres ressources sur le réseau virtuel. Consultez l’article [Carte réseau](virtual-network-network-interface.md) pour en savoir plus sur les cartes réseau. Le portail a également créé une ressource d’adresse IP publique. Les adresses IP publiques correspondent à un paramètre de configuration d’une ressource d’adresse IP publique. Pour en savoir plus sur les adresses IP publiques, consultez l’article [Adresses IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Se connecter aux machines virtuelles

Une fois votre réseau virtuel et les deux machines virtuelles créés, vous pouvez désormais vous connecter aux machines virtuelles en effectuant les étapes décrites dans les sections suivantes :

### <a name="connect-from-internet"></a>Se connecter à la machine virtuelle du serveur web à partir d’Internet

Pour vous connecter à la machine virtuelle du serveur web à partir d’Internet, procédez comme suit :

1. Dans le portail, ouvrez le groupe de ressources MyRG en effectuant les étapes décrites dans la section [Passer en revue les ressources](#review) de cet article.
2. Dans le panneau **MyRG**, cliquez sur la machine virtuelle **MyWebServer**.
3. Dans le panneau **MyWebServer**, cliquez sur **Se connecter**, comme illustré dans l’image suivante :

    ![Se connecter à la machine virtuelle du serveur web](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Autorisez votre navigateur à télécharger le fichier *MyWebServer.rdp*, puis ouvrez-le.
5. Si une boîte de dialogue s’affiche pour vous informer que le serveur de publication de la connexion à distance ne peut pas être vérifié, cliquez sur **Se connecter**.
6. Lors de la saisie de vos informations d’identification, assurez-vous de vous connecter avec le nom d’utilisateur et le mot de passe spécifiés à l’étape 3 de la section [Créer la machine virtuelle du serveur web](#create-web-server-vm) de cet article. Si le champ **Sécurité Windows** qui apparaît n’affiche pas les bonnes informations d’identification, vous devrez peut-être cliquer sur **More choices (Plus de choix)**, puis sur **Utiliser un autre compte** pour pouvoir saisir le nom d’utilisateur et le mot de passe appropriés. Cliquez sur **OK** pour vous connecter à la machine virtuelle.
7. Si le message **Connexion Bureau à distance** s’affiche pour vous informer que l’identité de l’ordinateur distant ne peut pas être vérifiée, cliquez sur **Oui**.
8. Vous êtes maintenant connecté à la machine virtuelle MyWebServer à partir d’Internet. Laissez la connexion Bureau à distance ouverte pour effectuer les étapes décrites dans la section suivante.

La connexion à distance s’effectue à l’adresse IP publique affectée à la ressource d’adresse IP publique que le portail a créé à l’étape 5 de la section [Créer un réseau virtuel avec deux sous-réseaux](#create-vnet) de cet article. La connexion est autorisée, car la règle par défaut créée dans le groupe de sécurité réseau **MyWebServer-nsg** a autorisé le trafic entrant TCP/3389 (RDP) à la machine virtuelle depuis n’importe quelle adresse IP source. Si vous essayez de vous connecter à la machine virtuelle sur un autre port, la connexion échoue, sauf si vous ajoutez des règles de trafic entrant au groupe de sécurité réseau autorisant des ports supplémentaires.

>[!NOTE]
>Si vous ajoutez des règles de trafic entrant au groupe de sécurité réseau, assurez-vous que les mêmes ports sont ouverts sur le pare-feu Windows, sans quoi la connexion échouera.
>

### <a name="connect-to-internet"></a>Se connecter à Internet depuis la machine virtuelle du serveur web

Pour connecter le trafic sortant à Internet depuis la machine virtuelle du serveur web, procédez comme suit :

1. Si vous ne disposez pas encore d’une connexion à distance à la machine virtuelle MyWebServer, établissez-la en effectuant les étapes décrites dans la section [Se connecter à la machine virtuelle du serveur web à partir d’Internet](#connect-from-internet) de cet article.
2. Depuis le Bureau Windows, ouvrez Internet Explorer. Dans la boîte de dialogue **Configurer Internet Explorer 11**, cliquez sur **Ne pas utiliser les paramètres recommandés**, puis cliquez sur **OK**. Il est recommandé d’accepter les paramètres recommandés pour le serveur de production.
3. Dans la barre d’adresses Internet Explorer, saisissez [bing.com](http:www.bing.com). Si une boîte de dialogue Internet Explorer s’affiche, cliquez sur **Ajouter**, puis **Ajouter** dans la boîte de dialogue **Sites de confiance** et cliquez sur **Fermer**. Répétez ce processus pour les autres boîtes de dialogue Internet Explorer.
4. Sur la page de recherche Bing, saisissez *whatsmyipaddress*, puis cliquez sur le bouton en forme de loupe. Bing renvoie l’adresse IP publique affectée à la ressource d’adresse IP publique créée par le portail lorsque vous avez créé la machine virtuelle. Si vous examinez les paramètres de la ressource **MyWebServer ip**, vous pouvez voir la même adresse IP affectée à la ressource d’adresse IP publique, comme indiqué dans l’image suivante. Toutefois, l’adresse IP affectée à votre machine virtuelle est différente.

    ![Se connecter à la machine virtuelle du serveur web](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Laissez la connexion Bureau à distance ouverte pour effectuer les étapes décrites dans la section suivante.

Vous pouvez vous connecter à Internet depuis la machine virtuelle, car toute la connectivité sortante depuis la machine virtuelle est autorisée par défaut. Vous pouvez limiter la connectivité sortante en ajoutant des règles au groupe de sécurité réseau appliqué à la carte réseau, au sous-réseau auquel la carte réseau est connectée, ou bien les deux.

Si la machine virtuelle est placée à l’état arrêté (libéré) à l’aide du portail, l’adresse IP publique peut changer. Si vous souhaitez que l’adresse IP publique ne change jamais, vous pouvez utiliser la méthode d’allocation statique de l’adresse IP, plutôt que la méthode d’allocation dynamique (qui est la méthode par défaut). Pour en savoir plus sur les différences entre les méthodes d’allocation, consultez l’article [Types d’adresses IP et méthodes d’allocation](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Se connecter à la machine virtuelle du serveur de base de donnée à partir de la machine virtuelle du serveur web

Pour vous connecter à la machine virtuelle du serveur de base de donnée à partir de la machine virtuelle du serveur web, procédez comme suit :

1. Si vous ne disposez pas encore d’une connexion à distance à la machine virtuelle MyWebServer, établissez-la en effectuant les étapes décrites dans la section [Se connecter à la machine virtuelle du serveur web à partir d’Internet](#connect-from-internet) de cet article.
2. Cliquez sur le bouton Démarrer en bas à gauche du Bureau Windows, puis saisissez *Bureau à distance*. Lorsque la liste du menu Démarrer affiche **Connexion Bureau à distance**, cliquez dessus.
3. Dans la boîte de dialogue **Connexion Bureau à distance**, saisissez *MyDBServer* comme nom de l’ordinateur et cliquez sur **Se connecter**.
4. Entrez le nom d’utilisateur et les mots de passe saisis à l’étape 3 de la section [Créer la machine virtuelle du serveur de base de données](#create-database-server-vm) de cet article, puis cliquez sur **OK**.
5. Si un message s’affiche pour vous informer que l’identité de l’ordinateur distant ne peut pas être vérifiée, cliquez sur **Oui**.
6. Laissez la connexion Bureau à distance aux deux serveurs ouverte pour effectuer les étapes décrites dans la section suivante.

Vous pouvez établir la connexion à la machine virtuelle du serveur de base de données depuis la machine virtuelle du serveur web pour les raisons suivantes :

- Les connexions entrantes TCP/3389 sont activées pour toutes les adresses IP sources dans le groupe de sécurité réseau par défaut créé à l’étape 5 de la section [Créer la machine virtuelle du serveur de base de données](#create-database-server-vm) de cet article.
- Vous avez lancé la connexion à partir de la machine virtuelle du serveur web, qui est connectée au même réseau virtuel en tant que machine virtuelle du serveur de base de données. Pour vous connecter à une machine virtuelle à laquelle aucune adresse IP publique n’est affectée, vous devez vous connecter depuis une autre machine virtuelle connectée au même réseau virtuel, même si la machine virtuelle est connectée à un sous-réseau différent.
- Même si les machines virtuelles sont connectées à différents sous-réseaux, Azure crée les itinéraires par défaut qui activent la connectivité entre les sous-réseaux. Vous pouvez toutefois remplacer les itinéraires par défaut en créant les vôtres. Pour en savoir plus sur le routage dans Azure, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).

Si vous essayez d’établir une connexion à distance à la machine virtuelle du serveur de base de données à partir d’Internet, comme vous l’avez fait dans la section [Se connecter à la machine virtuelle du serveur web à partir d’Internet](#connect-from-internet) de cet article, vous pouvez voir que l’option **Se connecter** est grisée. L’option est grisée car il n’y a aucune adresse IP publique affectée à la machine virtuelle. Les connexions entrantes à partir d’Internet ne sont donc pas possibles.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Se connecter à Internet depuis la machine virtuelle du serveur de base de données

Connectez le trafic sortant à Internet depuis la machine virtuelle du serveur de base de données en procédant comme suit :

1. Si vous ne disposez pas encore d’une connexion à distance à la machine virtuelle MyDBServer depuis la machine virtuelle MyWebServer, suivez les étapes décrites dans la section [Se connecter à la machine virtuelle du serveur de base de donnée à partir de la machine virtuelle du serveur web](#webserver-to-dbserver) de cet article.
2. À partir du Bureau Windows sur la machine virtuelle MyDBServer, ouvrez Internet Explorer et répondez aux boîtes de dialogue comme vous l’avez fait aux étapes 2 et 3 de la section [Se connecter à Internet à partir de la machine virtuelle du serveur web](#connect-to-internet) de cet article.
3. Dans la barre d’adresses, saisissez [bing.com](http:www.bing.com).
4. Cliquez sur **Ajouter** dans la boîte de dialogue Internet Explorer qui s’affiche, puis sur **Ajouter** et sur **Fermer** dans la boîte de dialogue **Sites de confiance**. Effectuez ces étapes dans les boîtes de dialogue qui s’affichent.
5. Sur la page de recherche Bing, saisissez *whatsmyipaddress*, puis cliquez sur le bouton en forme de loupe. Bing renvoie l’adresse IP publique actuellement affectée à la machine virtuelle par l’infrastructure Azure. 6. Fermez la connexion à distance à la machine virtuelle MyDBServer depuis la machine virtuelle MyWebServer, puis fermez la connexion à distance à la machine virtuelle MyWebServer.

La connexion sortante à Internet est autorisée, car tout le trafic sortant est autorisé par défaut, même si aucune ressource d’adresse IP publique n’est affectée à la machine virtuelle MyDBServer. Par défaut, toutes les machines virtuelles peuvent se connecter à Internet, avec ou sans ressource d’adresse IP publique affectée à la machine virtuelle. Toutefois, vous n’êtes pas autorisé à vous connecter à l’adresse IP publique à partir d’Internet, comme cela était possible pour la machine virtuelle MyWebServer à laquelle a été affectée une ressource d’adresse IP publique.

## <a name="delete-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

1. Pour afficher le groupe de ressources MyRG créé dans cet article, effectuez les étapes 1 à 3 décrites dans la section [Passer en revue les ressources](#review) de cet article. Une nouvelle fois, passez en revue les ressources du groupe de ressources. Si vous avez créé le groupe de ressources MyRG, en suivant les étapes précédentes, vous pouvez voir les 12 ressources indiquées dans l’image à l’étape 4.
2. Dans le panneau MyRG, cliquez sur le bouton **Supprimer**.
3. Le portail nécessite que vous saisissiez le nom du groupe de ressources pour confirmer la suppression. Si vous voyez des ressources autres que celles affichées à l’étape 4 de la section [Passer en revue les ressources](#review) de cet article, cliquez sur **Annuler**. Si vous voyez uniquement les 12 ressources créées dans le cadre de cet article, saisissez *MyRG* comme nom du groupe de ressources, puis cliquez sur **Supprimer**. La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Veuillez donc toujours vérifier le contenu d’un groupe de ressources avant de le supprimer. Le portail supprime toutes les ressources contenues dans le groupe de ressources, puis supprime le groupe de ressources lui-même. Cette opération prend plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

Dans cet exercice, vous avez créé un réseau virtuel et deux machines virtuelles. Vous avez spécifié des paramètres personnalisés lors de la création des machines virtuelles et accepté plusieurs paramètres par défaut. Nous vous recommandons de lire les articles suivants, avant de procéder au déploiement des réseaux virtuels et des machines virtuelles de production, afin de bien comprendre tous les paramètres disponibles :

- [Réseaux virtuels](virtual-networks-overview.md)
- [Adresses IP publiques](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Interfaces réseau](virtual-network-network-interface.md)
- [Groupes de sécurité réseau](virtual-networks-nsg.md)
- [Machines virtuelles](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
