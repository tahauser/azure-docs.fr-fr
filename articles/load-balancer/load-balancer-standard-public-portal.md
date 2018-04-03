---
title: Créer un équilibreur de charge standard à l’aide du portail Azure | Microsoft Docs
description: Apprenez à créer un équilibreur de charge standard à l’aide du portail Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/18
ms.author: kumud
ms.openlocfilehash: f67da7dc84878ca7418eb644daec1a9681e2f6f2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Créer un équilibreur de charge standard pour équilibrer la charge des machines virtuelles à l’aide du portail Azure

L’équilibrage de charge offre un niveau plus élevé de disponibilité et d’évolutivité en répartissant les demandes entrantes sur plusieurs machines virtuelles. Vous pouvez utiliser le portail Azure pour créer un équilibreur de charge qui équilibre la charge des machines virtuelles. Ce démarrage rapide vous montre comment équilibrer la charge des machines virtuelles à l’aide d’un équilibreur de charge standard.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-load-balancer"></a>Créer un équilibrage de charge public

Dans cette section, vous créez un équilibreur de charge public qui équilibre la charge des machines virtuelles. L’équilibreur de charge standard prend uniquement en charge une adresse IP publique standard. Lorsque vous créez un équilibreur de charge standard, vous devez également créer une nouvelle adresse IP publique standard configurée en tant que frontale (nommée *LoadBalancerFrontend* par défaut) pour cet équilibreur de charge standard. 

1. En haut à gauche de l’écran, cliquez sur **Créer une ressource** > **Mise en réseau** > **Équilibreur de charge**.
2. Dans la page **Créer un équilibreur de charge**, entrez les valeurs suivantes pour l’équilibreur de charge :
    - *myLoadBalancer* : pour le nom de l’équilibreur de charge.
    - **Public** : pour le type de l’équilibreur de charge.
     - *myPublicIP* : pour la **nouvelle** IP publique que vous créez.
    - *myResourceGroupSLB* : pour le nom du **nouveau** groupe de ressources à créer sélectionné.
    - **westeurope** : pour l’emplacement.
3. Cliquez sur **Créer** pour créer l’équilibreur de charge.
   
    ![Créer un équilibrage de charge](./media/load-balancer-standard-public-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cette section, vous créez un réseau virtuel ainsi que deux machines virtuelles pour le pool principal de votre équilibreur de charge, puis vous installez IIS sur les machines virtuelles afin de tester l’équilibreur de charge.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
1. Sur le côté gauche de l’écran, cliquez sur **Nouveau** > **Réseau** > **Réseau virtuel** et entrez ces valeurs pour le réseau virtuel :
    - *myVnet* : pour le nom du réseau virtuel.
    - *myResourceGroupSLB* : pour le nom du groupe de ressources existant
    - *myBackendSubnet* : pour le nom du sous-réseau.
2. Cliquez sur **Créer** pour créer le réseau virtuel.

    ![Créez un réseau virtuel](./media/load-balancer-standard-public-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

1. Sur le côté gauche de l’écran, cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter** et entrez ces valeurs pour la machine virtuelle :
    - *myVM1* : pour le nom de la machine virtuelle.        
    - *azureuser* : pour le nom d’utilisateur administrateur.    
    - *myResourceGroupSLB* : pour **Groupe de ressources**, sélectionnez **Utiliser existant**, puis *myResourceGroupSLB*.
2. Cliquez sur **OK**.
3. Sélectionnez **DS1_V2** pour la taille de la machine virtuelle, puis cliquez sur **Sélectionner**.
4. Entrez ces valeurs pour les paramètres de la machine virtuelle :
    - *myAvailabilitySet* : pour le nom du nouveau groupe à haute disponibilité que vous créez.
    -  *myVNet* : vérifiez qu’il est sélectionné en tant que réseau virtuel.
    - *myBackendSubnet* : vérifiez qu’il est sélectionné en tant que sous-réseau.
    - *myNetworkSecurityGroup* : pour le nom du nouveau groupe de sécurité réseau (pare-feu) que vous devez créer.
5. Cliquez sur **Désactivé** pour désactiver les diagnostics de démarrage.
6. Cliquez sur **OK**, vérifiez les paramètres sur la page de résumé, puis cliquez sur **Créer**.
7. Créez une deuxième machine virtuelle nommée *VM2* avec *myAvailibilityset* en tant que groupe à haute disponibilité, *myVnet* en tant que réseau virtuel, *myBackendSubnet* en tant que sous-réseau et **myNetworkSecurityGroup* en tant que groupe de sécurité réseau à l’aide des étapes 1 à 6. 

### <a name="create-nsg-rules"></a>Créer les règles du groupe de sécurité réseau

Dans cette section, vous créez des règles du groupe de sécurité réseau pour autoriser les connexions entrantes à l’aide de HTTP et RDP.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis dans la liste de ressources, cliquez sur **myNetworkSecurityGroup** qui se trouve dans le groupe de ressources **myResourceGroupSLB**.
2. Sous **Paramètres**, cliquez sur **Règles de sécurité entrantes**, puis sur **Ajouter**.
3. Entrez ces valeurs pour la règle de sécurité entrante nommée *myHTTPRule* afin d’autoriser les connexions HTTP entrantes à l’aide du port 80 :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *80* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *100* pour **Priorité**
    - *myHTTPRule* pour le nom
    - *Allow HTTP* pour la description
4. Cliquez sur **OK**.
 
 ![Créez un réseau virtuel](./media/load-balancer-standard-public-portal/8-load-balancer-nsg-rules.png)
5. Répétez les étapes 2 à 4 pour créer une autre règle nommée *myRDPRule* pour autoriser une connexion RDP entrante à l’aide du port 3389 avec les valeurs suivantes :
    - *Service Tag* : pour **Source**.
    - *Internet* : pour **Balise de service source**
    - *3389* : pour **Plages de port de destination**
    - *TCP* : pour **Protocole**
    - *Allow* : pour **Action**
    - *200* pour **Priorité**
    - *myRDPRule* pour le nom
    - *Allow RDP* pour la description

### <a name="install-iis"></a>Installer IIS

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis dans la liste de ressources, cliquez sur **myVM1** qui se trouve dans le groupe de ressources *myResourceGroupLB*.
2. Sur la page **Vue d’ensemble**, cliquez sur **Connexion** à RDP dans la machine virtuelle.
3. Connectez-vous à la machine virtuelle avec le nom d’utilisateur *azureuser*.
4. Sur le bureau du serveur, accédez à **Outils d’administration Windows**>**Gestionnaire de serveur**.
5. Dans le Gestionnaire de serveur, cliquez sur **Ajouter des rôles et des fonctionnalités**.
6. Dans l’Assistant **Ajouter des rôles et fonctionnalités**, utilisez les valeurs suivantes :
    - Dans la page **Sélectionner le type d’installation**, cliquez sur **Installation basée sur un rôle ou une fonctionnalité**.
    - Dans la page **Sélectionner le serveur de destination**, cliquez sur **myVM1**
    - Dans la page **Sélectionner le rôle du serveur**, cliquez sur **Serveur Web (IIS)**
    - Suivez les instructions jusqu’à la fin de l’Assistant 
7. Répétez les étapes 1 à 6 pour la machine virtuelle *myVM2*.

## <a name="create-load-balancer-resources"></a>Créer les ressources d’équilibreur de charge

Dans cette section, vous configurez les paramètres de l’équilibreur de charge pour un pool d’adresses principal et une sonde d’intégrité et spécifiez l’équilibreur de charge et les règles NAT.


### <a name="create-a-backend-address-pool"></a>Créer un pool d’adresses principal

Pour distribuer le trafic vers les machines virtuelles, un pool d’adresses principal contient les adresses IP des cartes d’interface réseau virtuelles connectées à l’équilibreur de charge. Créez le pool d’adresses principal *myBackendPool* pour inclure *VM1* et *VM2*.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Pools principaux**, puis sur **Ajouter**.
3. Sur la page **Ajouter un pool principal**, procédez comme suit :
    - Pour nom, tapez *myBackEndPool, comme nom du pool principal.
    - Pour **Associé à**, dans le menu déroulant, cliquez sur **Groupe à haute disponibilité**
    - Pour **Groupe à haute disponibilité**, cliquez sur **myAvailabilitySet**.
    - Cliquez sur **Ajouter une configuration IP de réseau cible** pour ajouter chaque machine virtuelle (*myVM1* & *myVM2*) créée au pool principal.
    - Cliquez sur **OK**.

    ![Ajout au pool d’adresses principal - ](./media/load-balancer-standard-public-portal/3-load-balancer-backend-02.png)

3. Vérifiez que le paramètre du pool principal de l’équilibreur de charge affiche les machines virtuelles **VM1** et **VM2**.

### <a name="create-a-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Créez une sonde d’intégrité *myHealthProbe* pour surveiller l’intégrité des machines virtuelles.

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, sur **Sondes d’intégrité**, puis sur **Ajouter**.
3. Utilisez ces valeurs pour créer la sonde d’intégrité :
    - *myHealthProbe* : pour le nom de la sonde d’intégrité.
    - **HTTP** : pour le type de protocole.
    - *80* : pour le numéro de port.
    - *15* : pour **l’intervalle** en secondes entre les tentatives de la sonde.
    - *2* : pour le nombre de **seuils de défaillance** ou d’échecs de sonde consécutifs qui se produisent avant qu’une machine virtuelle soit considérée comme défaillante.
4. Cliquez sur **OK**.

   ![Ajout d'une sonde](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. Créez une règle d’équilibreur de charge *myLoadBalancerRuleWeb* pour écouter le port 80 dans le frontal *FrontendLoadBalancer* et envoyer le trafic réseau équilibré en charge vers le pool d’adresses principal *myBackEndPool* qui utilisé également le port 80. 

1. Cliquez sur **Toutes les ressources** dans le menu de gauche, puis cliquez sur **myLoadBalancer** dans la liste des ressources.
2. Cliquez sur **Paramètres**, **Règles d’équilibrage de charge**, puis sur **Ajouter**.
3. Utilisez ces valeurs pour configurer la règle d’équilibrage de charge :
    - *myHTTPRule* : pour que le nom de la règle d’équilibrage de charge.
    - **TCP** : pour le type de protocole.
    - *80* : pour le numéro de port.
    - *80* : pour le port principal.
    - *myBackendPool* : pour le nom du pool principal.
    - *myHealthProbe* : pour le nom de la sonde d’intégrité.
4. Cliquez sur **OK**.
    
    ![Ajout d’une règle d’équilibrage de charge](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge
1. Recherchez l’adresse IP publique de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Cliquez sur **Toutes les ressources**, puis sur **myPublicIP**.

2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

      ![Serveur Web IIS](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’équilibreur de charge et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources qui contient l’équilibreur de charge, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [l’équilibreur de charge standard](load-balancer-standard-overview.md).
