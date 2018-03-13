---
title: "Créer un réseau virtuel dans Azure - Portail | Microsoft Docs"
description: "Découvrez rapidement comment créer un réseau virtuel à l’aide du portail Azure. Un réseau virtuel permet à de nombreux types de ressources Azure de communiquer en privé."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Créer un réseau virtuel au moyen du portail Azure

Dans cet article, vous allez apprendre à créer un réseau virtuel. Une fois le réseau virtuel créé, déployez deux machines virtuelles dans le réseau virtuel pour tester la communication réseau en privé entre elles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Sélectionnez **+ Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.

3. Comme indiqué dans l’image suivante, entrez *myVirtualNetwork* comme **Nom**, *myResourceGroup* comme **Groupe de ressources**, sélectionnez une  **Localisation** et votre **Abonnement**, acceptez les valeurs par défaut restantes, puis sélectionnez **Créer**. 

    ![Entrer des informations de base sur votre réseau virtuel](./media/quick-create-portal/virtual-network.png)

    **L’espace d’adressage** est spécifié en notation CIDR. Un réseau virtuel contient zéro ou plusieurs sous-réseaux. La **Plage d’adresses** 10.0.0.0/24 du sous-réseau par défaut utilise la plage d’adresses complète du réseau virtuel. Un autre sous-réseau ne peut donc pas être créé dans le réseau virtuel à l’aide de la plage et de l’espace d’adressage par défaut. La plage d’adresses spécifiée inclut les adresses IP 10.0.0.0-10.0.0.254. Toutefois, seule la plage 10.0.0.4-10.0.0.254 est disponible car Azure réserve les quatre premières adresses (0-3) et la dernière adresse de chaque sous-réseau. Les adresses IP disponibles sont affectées aux ressources déployées dans un réseau virtuel.

## <a name="test-network-communication"></a>Tester la communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir valider ultérieurement la communication en privé entre elles.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**.

3. Entrez les informations sur la machine virtuelle indiquées dans l’image qui suit. Le **nom d’utilisateur** et le **mot de passe** que vous entrez vous serviront à vous connecter à la machine virtuelle lors d’une étape ultérieure. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Sélectionnez votre **Abonnement**, choisissez d’utiliser le groupe de ressources *myResourceGroup* existant et vérifiez que la **Localisation** sélectionnée est la même que celle dans laquelle vous avez créé le réseau virtuel. Quand vous avez terminé, sélectionnez **OK**.

    ![Entrer des informations de base sur une machine virtuelle](./media/quick-create-portal/virtual-machine-basics.png)

4. Sélectionnez une taille de machine virtuelle, puis sélectionnez **Sélectionner**. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Les tailles qui apparaissent peuvent être différentes de celles présentées dans l’exemple suivant : 

    ![Sélectionner une taille de machine virtuelle](./media/quick-create-portal/virtual-machine-size.png)

5. Sous **Paramètres**, *myVirtualNetwork* doit déjà être sélectionné comme **Réseau virtuel**. Si ce n’est pas le cas, sélectionnez **Réseau virtuel**, puis sélectionnez *myVirtualNetwork*. Laissez l’option *default* sélectionnée pour le **sous-réseau**, puis sélectionnez **OK**.

    ![Sélectionner un réseau virtuel](./media/quick-create-portal/virtual-machine-network-settings.png)

6. Dans la page **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle. 

7. La création de la machine virtuelle prend quelques minutes. Au terme de cette opération, la machine virtuelle est épinglée au tableau de bord du portail Azure et le résumé de la machine virtuelle s’ouvre automatiquement. Sélectionnez **Mise en réseau**.

    ![Informations de mise en réseau sur la machine virtuelle](./media/quick-create-portal/virtual-machine-networking.png)

    Vous pouvez constater que **l’Adresse IP privée** est *10.0.0.4*. À l’étape 5, sous **Paramètres**, vous avez sélectionné le réseau virtuel *myVirtualNetwork* et avez accepté le sous-réseau nommé *default* comme **Sous-réseau**. Quand [vous avez créé le réseau virtuel](#create-a-virtual-network), vous avez accepté la valeur par défaut 10.0.0.0/24 comme **Plage d’adresses** du sous-réseau. Le serveur DHCP d’Azure affecte la première adresse disponible pour le sous-réseau que vous sélectionnez à la machine virtuelle. Azure réservant les quatre premières adresses (0-3) de chaque sous-réseau, 10.0.0.4 est la première adresse IP disponible pour le sous-réseau.

    **L’Adresse IP publique** affectée est différente de celle affectée à votre machine virtuelle. Par défaut, Azure affecte une adresse IP routable Internet publique à chaque machine virtuelle. L’adresse IP publique est affectée à la machine virtuelle à partir d’un [pool d’adresses affecté à chaque région Azure](https://www.microsoft.com/download/details.aspx?id=41653). Si Azure a connaissance de l’adresse IP publique qui est affectée à une machine virtuelle, le système d’exploitation en cours d’exécution dans une machine virtuelle l’ignore.

8. Répétez les étapes 1 à 7, mais à l’étape 3, nommez la machine virtuelle *myVm2*. 

9. Une fois la machine virtuelle créée, sélectionnez **Mise en réseau**, comme à l’étape 7. Vous pouvez constater que **l’Adresse IP privée** est *10.0.0.5*. Azure ayant précédemment affecté la première adresse utilisable dans le sous-réseau (*10.0.0.4*) à la machine virtuelle *myVm1*, il affecte *10.0.0.5* (la prochaine adresse disponible dans le sous-réseau) à la machine virtuelle *myVm2*.

### <a name="connect-to-a-virtual-machine"></a>Connexion à une machine virtuelle

1. Connectez-vous à distance à la machine virtuelle *myVm1*. En haut du portail Azure, entrez *myVm1*. Quand **myVm1** apparaît dans les résultats de la recherche, sélectionnez-la. Sélectionnez le bouton **Connexion**.

    ![Vue d’ensemble de la machine virtuelle](./media/quick-create-portal/virtual-machine-overview.png)

2. Après avoir sélectionné le bouton **Connexion**, le fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur.  

3. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés au moment de la création de la machine virtuelle, puis sélectionnez **OK**. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez**Oui** ou **Continuer** pour poursuivre le processus de connexion.

### <a name="validate-communication"></a>Valider la communication

Toute tentative de test ping sur une machine virtuelle Windows échoue car, par défaut, ce test n’est pas autorisé à franchir le pare-feu Windows. Pour autoriser un test ping sur *myVm1*, entrez la commande suivante à partir d’une invite de commandes :

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Pour valider la communication avec *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1*. Fournissez les informations d’identification que vous avez utilisées au moment de la création de la machine virtuelle, puis terminez la connexion :

```
mstsc /v:myVm2
```

La connexion Bureau à distance aboutit, ce qui s’explique par le fait que des adresses IP privées sont affectées aux deux machines virtuelles à partir du sous-réseau *default* et que, par défaut, le Bureau à distance est ouvert dans le pare-feu Windows. Vous pouvez vous connecter à *myVm2* par nom d’hôte, car Azure fournit automatiquement la résolution de nom DNS pour tous les hôtes au sein d’un réseau virtuel. À partir d’une invite de commandes, effectuez un test ping sur *myVm1* à partir de *myVm2*.

```
ping myvm1
```

Le test ping aboutit car vous l’avez autorisé à franchir le pare-feu Windows sur la machine virtuelle *myVm1* lors d’une étape précédente. Pour confirmer les communications sortantes à destination d’Internet, utilisez la commande suivante :

```
ping bing.com
```

Vous recevez quatre réponses de bing.com. Par défaut, toute machine virtuelle dans un réseau virtuel prend en charge les communications sortantes à destination d’Internet. 

Fermez la session Bureau à distance.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le et tout son contenu. En haut du portail Azure, entrez *myResourceGroup*. Quand **MyResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-la. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez déployé un réseau virtuel par défaut avec un sous-réseau. Pour découvrir comment créer un réseau virtuel personnalisé avec plusieurs sous-réseaux, passez au didacticiel couvrant la création d’un réseau virtuel personnalisé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel personnalisé](virtual-networks-create-vnet-arm-pportal.md#portal)
