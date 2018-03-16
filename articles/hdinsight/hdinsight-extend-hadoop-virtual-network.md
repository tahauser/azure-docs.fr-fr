---
title: "Étendre HDInsight avec un réseau virtuel - Azure | Microsoft Docs"
description: "Apprenez à utiliser Azure Virtual Network pour connecter HDInsight à d'autres ressources de cloud ou à des ressources de votre centre de données"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: ea686ea5dd6166d6e4e4055cb4a2cdd70af81ab4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Étendre HDInsight à l’aide d’un réseau virtuel Azure

Découvrez comment utiliser HDInsight avec un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). L’utilisation d’un réseau virtuel Azure permet les scénarios suivants :

* connexion à HDInsight directement à partir d’un réseau local ;

* connexion de HDInsight à des banques de données dans un réseau virtuel Azure ;

* accès direct aux services Hadoop qui ne sont pas disponibles publiquement sur Internet, tels que les API Kafka ou l’API Java HBase.

> [!WARNING]
> Les informations contenues dans ce document nécessitent une compréhension de la gestion de réseau TCP/IP. Si vous n’êtes pas familiarisé avec la gestion de réseau TCP/IP, vous devez travailler en partenariat avec une personne connaissant le sujet avant d’apporter des modifications aux réseaux de production.

> [!IMPORTANT]
> Si vous recherchez des instructions pas à pas pour connecter HDInsight à votre réseau local en utilisant un réseau virtuel Azure, consultez le document [Connecter HDInsight à votre réseau local](connect-on-premises-network.md).

## <a name="planning"></a>Planification

Les questions auxquelles vous devez répondre lors de la planification de l’installation de HDInsight dans un réseau virtuel sont les suivantes :

* Devez installer HDInsight dans un réseau virtuel existant ? Ou bien créez-vous un réseau ?

    Si vous utilisez un réseau virtuel existant, vous devrez peut-être modifier la configuration de celui-ci avant d’installer HDInsight. Pour plus d’informations, voir la section [Ajouter HDInsight à un réseau virtuel existant](#existingvnet).

* Vous souhaitez connecter le réseau virtuel contenant HDInsight à un autre réseau virtuel ou à votre réseau local ?

    Pour utiliser aisément des ressources de différents réseaux, il se peut que vous deviez créer un DNS personnalisé et configurer un transfert de DNS. Pour plus d’informations, voir la section [Connecter plusieurs réseaux](#multinet).

* Souhaitez-vous restreindre/rediriger le trafic entrant ou sortant échangé avec HDInsight ?

    HDInsight doit disposer d’une communication illimitée avec les adresses IP spécifiques dans le centre de données Azure. Il existe également plusieurs ports qui doivent être autorisés au travers de pare-feu pour la communication du client. Pour plus d’informations, voir la section [Contrôler le trafic réseau](#networktraffic).

## <a id="existingvnet"></a>Ajouter HDInsight à un réseau virtuel existant

Suivez les étapes de cette section pour découvrir comment ajouter un nouveau cluster HDInsight à un réseau virtuel Azure existant.

> [!NOTE]
> Vous ne pouvez pas ajouter un cluster HDInsight existant à un réseau virtuel.

1. Utilisez-vous un modèle de déploiement classique ou Resource Manager pour le réseau virtuel ?

    HDInsight 3.4 et versions ultérieures nécessitent un réseau virtuel Resource Manager. Les versions antérieures de HDInsight nécessitaient un réseau virtuel classique.

    Si votre réseau existant est un réseau virtuel classique, vous devez créer un réseau virtuel Resource Manager, puis connecter les deux. [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Une fois joint, HDInsight installé dans le réseau peut interagir avec des ressources du réseau classique.

2. Voulez-vous utiliser un tunneling forcé ? Le tunneling forcé est un paramètre de sous-réseau qui force l’acheminement du trafic Internet sortant vers un appareil à des fins d’inspection et de journalisation. HDInsight ne prend pas en charge le tunneling forcé. Supprimez le tunneling forcé avant d’installer HDInsight dans un sous-réseau, ou de créer un sous-réseau pour HDInsight.

3. Utilisez-vous des groupes de sécurité réseau, des itinéraires définis par l'utilisateur ou des appliances de réseau virtuel pour restreindre le trafic échangé avec le réseau virtuel ?

    Service administré, HDInsight requiert un accès illimité à plusieurs adresses IP dans le centre de données Azure. Pour permettre la communication avec ces adresses IP, mettez à jour des groupes de sécurité réseau ou des itinéraires définis par l'utilisateur.

    HDInsight héberge plusieurs services qui une série de ports. Ne bloquez pas le trafic vers ces ports. Pour obtenir la liste des ports auxquels autoriser l’accès via des pare-feu d’appliance virtuelle, voir la section [Sécurité](#security).

    Pour rechercher votre configuration de sécurité existante, utilisez les commandes Azure PowerShell ou Azure CLI suivantes :

    * Groupes de sécurité réseau

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Pour plus d’informations, voir le document [Résoudre les problèmes relatifs aux groupes de sécurité réseau](../virtual-network/virtual-network-nsg-troubleshoot-portal.md).

        > [!IMPORTANT]
        > Les règles de groupe de sécurité réseau sont appliquées dans un ordre basé sur leur priorité. La première règle correspondant au modèle de trafic est appliquée, et aucune autre n’est appliquée à ce trafic. Règles d’ordre de la plus permissive à la moins permissive. Pour plus d’informations, voir le document [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

    * Itinéraires définis par l’utilisateur

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Pour plus d’informations, voir le document [Résoudre les problèmes relatifs aux itinéraires](../virtual-network/virtual-network-routes-troubleshoot-portal.md).

4. Créez un cluster HDInsight et sélectionnez le réseau virtuel Azure pendant la configuration. Pour comprendre le processus de création du cluster, utilisez les étapes indiquées dans les documents suivants :

    * [Créer un cluster HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Créer un cluster HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Créer un cluster HDInsight à l’aide de l’interface de ligne de commande Azure 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Créer un cluster HDInsight à l’aide d’un modèle Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > L’ajout de HDInsight à un réseau virtuel est une étape de configuration facultative. Veillez à sélectionner le réseau virtuel lors de la configuration du cluster.

## <a id="multinet"></a>Connexion de plusieurs réseaux

Le principal défi avec une configuration de réseau multiples est la résolution de noms entre les réseaux.

Azure assure la résolution de noms pour les services Azure installés dans un réseau virtuel. Cette résolution de noms intégrée permet à HDInsight de se connecter aux ressources suivantes à l’aide d’un nom de domaine complet (FQDN) :

* Toute ressource disponible sur Internet. Par exemple, microsoft.com ou google.com.

* Toute ressource figurant dans le même réseau virtuel Azure, en utilisant le __nom DNS interne__ de la ressource. Par exemple, lorsque vous utilisez la résolution de noms par défaut, les éléments suivants sont des exemples de noms DNS internes attribués aux nœuds worker HDInsight :

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Les deux nœuds peuvent communiquer directement entre eux et avec d’autres nœuds dans HDInsight en utilisant des noms DNS internes.

La résolution de noms par défaut ne permet __pas__ à HDInsight de résoudre les noms des ressources en réseaux joints au réseau virtuel. Par exemple, il est courant de joindre un réseau local au réseau virtuel. Avec uniquement la résolution de noms par défaut, HDInsight ne peut pas accéder aux ressources du réseau local par leur nom. L’inverse est également vrai ; les ressources de votre réseau local ne peuvent pas accéder aux ressources du réseau virtuel par leur nom.

> [!WARNING]
> Vous devez créer le serveur DNS personnalisé et configurer le réseau virtuel pour l’utiliser avant de créer le cluster HDInsight.

Pour permettre la résolution de noms entre le réseau virtuel et les ressources dans des réseaux joints, vous devez effectuer les actions suivantes :

1. Créez un serveur DNS personnalisé dans le réseau virtuel Azure où vous prévoyez d’installer HDInsight.

2. Configurez le réseau virtuel pour utiliser le serveur DNS personnalisé.

3. Recherchez le suffixe DNS qu'Azure à affecté à votre réseau virtuel. Cette valeur est similaire à `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Pour plus d’informations sur la recherche de suffixe DNS, voir la section [Exemple : DNS personnalisé](#example-dns).

4. Configurez le transfert entre les serveurs DNS. La configuration dépend du type de réseau distant.

    * Si le réseau distant est un réseau local, configurez le DNS comme suit :
        
        * __DNS personnalisé__ (dans le réseau virtuel) :

            * Transférez les demandes relatives au suffixe DNS du réseau virtuel au programme de résolution récursive d’Azure (168.63.129.16). Azure gère les demandes de ressources dans le réseau virtuel.

            * Transférez toutes les autres demandes au serveur DNS local. Le serveur DNS local gère toutes les autres demandes de résolution de noms, y compris les demandes de ressources Internet telles que Microsoft.com.

        * __DNS local__ : transférez les demandes de suffixe DNS de réseau virtuel vers le serveur DNS personnalisé. Le serveur DNS personnalisé transfère alors les demandes au programme de résolution récursive d’Azure.

        Cette configuration a pour effet de router les demandes de noms de domaine complets (FQDN) qui contiennent le suffixe DNS du réseau virtuel vers le serveur DNS personnalisé. Toutes les autres demandes (même d’adresses Internet publiques) sont gérées par le serveur DNS local.

    * Si le réseau distant est un autre réseau virtuel Azure, configurez DNS comme suit :

        * __DNS personnalisé__ (dans chaque réseau virtuel) :

            * Les demandes de suffixe DNS des réseaux virtuels sont transférées aux serveurs DNS personnalisés. Le DNS de chaque réseau virtuel est chargé de résoudre les ressources au sein de son réseau.

            * Transférez toutes les autres demandes au programme de résolution récursive d’Azure. Le programme de résolution récursive est responsable de la résolution des ressources locales et Internet.

        Le serveur DNS de chaque réseau transfère les demandes à l’autre, en fonction du suffixe DNS. Les autres requêtes sont résolues à l’aide du programme de résolution récursive d’Azure.

    Pour un exemple de chaque configuration, voir la section [Exemple : DNS personnalisé](#example-dns).

Pour plus d’informations, voir le document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="directly-connect-to-hadoop-services"></a>Se connecter directement aux services Hadoop

L’essentiel de la documentation relative à HDInsight part du principe que vous avez accès au cluster via Internet. Par exemple, vous pouvez vous connecter au cluster à l’adresse https://CLUSTERNAME.azurehdinsight.net. Cette adresse utilise la passerelle publique qui n’est pas disponible si vous avez utilisé des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur pour restreindre l’accès à partir d’Internet.

Pour vous connecter à Ambari et à d’autres pages web via le réseau virtuel, procédez comme suit :

1. Pour découvrir les noms de domaine complets (FQDN) internes des nœuds de cluster HDInsight, utilisez l’une des méthodes suivantes :

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Dans la liste des nœuds retournés, recherchez le nom de domaine complet des nœuds principaux, puis utilisez-les pour vous connecter à Ambari et à d’autres services web. Par exemple, utilisez `http://<headnode-fqdn>:8080` pour accéder à Ambari.

    > [!IMPORTANT]
    > Certains services hébergés sur les nœuds principaux ne sont actifs que sur un seul nœud à la fois. Si vous tentez d’accéder à un service sur un nœud principal et que l’opération retourne l’erreur 404, basculez vers l’autre nœud principal.

2. Pour déterminer le nœud et le port sur lesquels un service est disponible, voir [Ports utilisés par les services Hadoop dans HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a> Contrôler le trafic réseau

Le trafic réseau dans les réseaux virtuels Azure peut être contrôlé à l’aide des méthodes suivantes :

* Les **Groupes de sécurité réseau** (NSG) vous permettent de filtrer le trafic entrant et sortant changé avec le réseau. Pour plus d’informations, voir le document [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

    > [!WARNING]
    > HDInsight ne prend pas en charge la restriction du trafic sortant.

* Les **Itinéraires définis par l’utilisateur** définissent la manière dont trafic circule entre les ressources du réseau. Pour plus d’informations, voir le document [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

* Les **appliances virtuelles réseau** répliquent les fonctionnalités d’appareils tels que des routeurs et pare-feu. Pour plus d’informations, voir le document [Appliances réseau](https://azure.microsoft.com/solutions/network-appliances).

Service administré, HDInsight requiert un accès illimité aux services de gestion et de contrôle d’intégrité Azure dans le cloud Azure. Lorsque vous utilisez des groupes de sécurité réseau et des itinéraires définis par l’utilisateur, vous devez vous assurer que ces services peuvent toujours communiquer avec HDInsight.

HDInsight expose des services sur plusieurs ports. Lorsque vous utilisez un pare-feu d’appliance virtuelle, vous devez autoriser le trafic sur les ports utilisés pour ces services. Pour plus d’informations, voir la section [Ports requis].

### <a id="hdinsight-ip"></a> HDInsight avec des groupes de sécurité réseau et des itinéraires définis par l’utilisateur

Si vous prévoyez d’utiliser des **groupes de sécurité réseau** ou des **itinéraires définis par l’utilisateur** pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight :

1. Identifiez la région Azure que vous projetez d’utiliser pour HDInsight.

2. Identifiez les adresses IP que HDInsight requiert. Pour plus d’informations, consultez la section [Adresses IP requises par HDInsight](#hdinsight-ip).

3. Créez ou modifiez les groupes de sécurité réseau ou les itinéraires définis par l’utilisateur pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight.

    * __Groupes de sécurité réseau__ : autorisez le trafic __entrant__ sur le port __443__ à partir des adresses IP.
    * __Itinéraires définis par l’utilisateur__ : créez un itinéraire pour chaque adresse IP et définissez __Type de tronçon suivant__ sur __Internet__.

Pour plus d’informations sur les groupes de sécurité réseau ou les itinéraires définis par l’utilisateur, voir la documentation suivante :

* [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)

* [Itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Tunneling forcé

Le tunneling forcé est une configuration d’itinéraire défini par l’utilisateur où tout le trafic en provenance d’un sous-réseau est acheminé de force vers un réseau ou un emplacement spécifique, tel que votre réseau local. HDInsight ne prend __pas__ en charge le tunneling forcé.

## <a id="hdinsight-ip"></a> Adresses IP requises

> [!IMPORTANT]
> Les services de gestion et de contrôle d’intégrité Azure doivent être en mesure de communiquer avec HDInsight. Si vous utilisez des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur, autorisez le trafic provenant des adresses IP de ces services à se diriger vers HDInsight.
>
> Si vous n’utilisez pas de groupes de sécurité réseau ou d’itinéraires définis par l’utilisateur pour contrôler le trafic, vous pouvez ignorer cette section.

Si vous utilisez des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur, vous devez autoriser le trafic provenant des services de gestion et d’intégrité Azure à se diriger vers HDInsight. Pour trouver les adresses IP qui doivent être autorisées, effectuez les opérations suivantes :

1. Vous devez toujours autoriser le trafic à partir des adresses IP suivantes :

    | Adresse IP | Port autorisé | Direction |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Trafic entrant |
    | 23.99.5.239 | 443 | Trafic entrant |
    | 168.61.48.131 | 443 | Trafic entrant |
    | 138.91.141.162 | 443 | Trafic entrant |

2. Si votre cluster HDInsight est dans une des régions suivantes, vous devez autoriser le trafic à partir des adresses IP répertoriées pour la région concernée :

    > [!IMPORTANT]
    > Si la région Azure que vous utilisez n’est pas répertoriée, utilisez uniquement les quatre adresses IP de l’étape 1.

    | Pays | Région | Adresses IP autorisées | Port autorisé | Direction |
    | ---- | ---- | ---- | ---- | ----- |
    | Asie | Est de l'Asie | 23.102.235.122</br>52.175.38.134 | 443 | Trafic entrant |
    | &nbsp; | Asie du Sud-Est | 13.76.245.160</br>13.76.136.249 | 443 | Trafic entrant |
    | Australie | Est de l’Australie | 104.210.84.115</br>13.75.152.195 | 443 | Trafic entrant |
    | &nbsp; | Sud-est de l’Australie | 13.77.2.56</br>13.77.2.94 | 443 | Trafic entrant |
    | Brésil | Sud du Brésil | 191.235.84.104</br>191.235.87.113 | 443 | Trafic entrant |
    | Canada | Est du Canada | 52.229.127.96</br>52.229.123.172 | 443 | Trafic entrant |
    | &nbsp; | Centre du Canada | 52.228.37.66</br>52.228.45.222 | 443 | Trafic entrant |
    | Chine | Chine du Nord | 42.159.96.170</br>139.217.2.219 | 443 | Trafic entrant |
    | &nbsp; | Chine orientale | 42.159.198.178</br>42.159.234.157 | 443 | Trafic entrant |
    | Europe | Europe du Nord | 52.164.210.96</br>13.74.153.132 | 443 | Trafic entrant |
    | &nbsp; | Europe de l'Ouest| 52.166.243.90</br>52.174.36.244 | 443 | Trafic entrant |
    | Allemagne | Centre de l’Allemagne | 51.4.146.68</br>51.4.146.80 | 443 | Trafic entrant |
    | &nbsp; | Nord-Est de l’Allemagne | 51.5.150.132</br>51.5.144.101 | 443 | Trafic entrant |
    | Inde | Inde centrale | 52.172.153.209</br>52.172.152.49 | 443 | Trafic entrant |
    | Japon | Est du Japon | 13.78.125.90</br>13.78.89.60 | 443 | Trafic entrant |
    | &nbsp; | Ouest du Japon | 40.74.125.69</br>138.91.29.150 | 443 | Trafic entrant |
    | Corée du Sud | Centre de la Corée | 52.231.39.142</br>52.231.36.209 | 433 | Trafic entrant |
    | &nbsp; | Corée du Sud | 52.231.203.16</br>52.231.205.214 | 443 | Trafic entrant
    | Royaume-Uni | Ouest du Royaume-Uni | 51.141.13.110</br>51.141.7.20 | 443 | Trafic entrant |
    | &nbsp; | Sud du Royaume-Uni | 51.140.47.39</br>51.140.52.16 | 443 | Trafic entrant |
    | États-Unis | Centre des États-Unis | 13.67.223.215</br>40.86.83.253 | 443 | Trafic entrant |
    | &nbsp; | Est des États-Unis | 13.82.225.233</br>40.71.175.99 | 443 | Trafic entrant |
    | &nbsp; | Centre-Nord des États-Unis | 157.56.8.38</br>157.55.213.99 | 443 | Trafic entrant |
    | &nbsp; | Centre-Ouest des États-Unis | 52.161.23.15</br>52.161.10.167 | 443 | Trafic entrant |
    | &nbsp; | États-Unis de l’Ouest | 13.64.254.98</br>23.101.196.19 | 443 | Trafic entrant |
    | &nbsp; | Ouest des États-Unis 2 | 52.175.211.210</br>52.175.222.222 | 443 | Trafic entrant |

    Pour plus d’informations sur les adresses IP à utiliser pour Azure Government, voir le document [Intelligence et analyse Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics).

3. Si vous utilisez un serveur DNS personnalisé avec votre réseau virtuel, vous devez également autoriser l’accès à partir de __168.63.129.16__. Cette adresse est celle d’un programme de résolution récursive d’Azure. Pour plus d’informations, voir le document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Pour plus d’informations, voir la section [Contrôler le trafic réseau](#networktraffic).

## <a id="hdinsight-ports"></a> Ports requis

Si vous prévoyez d’utiliser un **pare-feu d’appliance virtuelle** réseau pour sécuriser le réseau virtuel, vous devez autoriser le trafic sortant sur les ports suivants :

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Pour obtenir la liste des ports utilisés pour des services spécifiques, voir le document [Ports utilisés par les services Hadoop sur HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pour plus d’informations sur les règles de pare-feu pour les appliances virtuelles, voir le document [Scénario d’appliance virtuelle](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a id="hdinsight-nsg"></a>Exemple : groupes de sécurité réseau avec HDInsight

Les exemples de cette section montrent comment créer des règles de groupe de sécurité réseau qui permettent à HDInsight de communiquer avec les services de gestion Azure. Avant d’utiliser les exemples, ajustez les adresses IP pour qu’elles correspondent à celles de la région Azure que vous utilisez. Pour trouver ces informations, voir la section [HDInsight avec des groupes de sécurité réseau et des itinéraires définis par l’utilisateur](#hdinsight-ip).

### <a name="azure-resource-management-template"></a>Modèle de gestion des ressources Azure

Le modèle de gestion des ressources suivant crée un réseau virtuel qui restreint le trafic entrant, mais autorise le trafic en provenance des adresses IP requises par HDInsight. Ce modèle crée également un cluster HDInsight dans le réseau virtuel.

* [Déployer un réseau virtuel Azure sécurisé et un cluster Hadoop HDInsight](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Modifiez les adresses IP utilisées dans cet exemple pour les faire correspondre à la région Azure que vous utilisez. Pour trouver ces informations, voir la section [HDInsight avec des groupes de sécurité réseau et des itinéraires définis par l’utilisateur](#hdinsight-ip).

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez le script PowerShell suivant pour créer un réseau virtuel qui restreint le trafic entrant et autorise le trafic en provenance des adresses IP de la région Europe du Nord.

> [!IMPORTANT]
> Modifiez les adresses IP utilisées dans cet exemple pour les faire correspondre à la région Azure que vous utilisez. Pour trouver ces informations, voir la section [HDInsight avec des groupes de sécurité réseau et des itinéraires définis par l’utilisateur](#hdinsight-ip).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzureRmVirtual Network
```

> [!IMPORTANT]
> Cet exemple montre comment ajouter des règles pour autoriser le trafic entrant sur les adresses IP requises. Il ne contient pas de règle pour restreindre l’accès entrant à partir d’autres sources.
>
> L’exemple suivant montre comment activer l’accès SSH depuis Internet :
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

Suivez les étapes suivantes pour créer un réseau virtuel qui restreint le trafic entrant, mais autorise le trafic en provenance des adresses IP requises par HDInsight.

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez **RESOURCEGROUPNAME** par le groupe de ressources qui contient le réseau virtuel Azure. Remplacez **LOCATION** par l’emplacement (région) où le groupe a été créé.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Une fois que le groupe est créé, vous recevez des informations sur le nouveau groupe.

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez la valeur **RESOURCEGROUPNAME** par le nom du groupe de ressources qui contient le réseau virtuel Azure.

    > [!IMPORTANT]
    > Modifiez les adresses IP utilisées dans cet exemple pour les faire correspondre à la région Azure que vous utilisez. Pour trouver ces informations, voir la section [HDInsight avec des groupes de sécurité réseau et des itinéraires définis par l’utilisateur](#hdinsight-ip).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Pour récupérer l’identificateur unique pour ce groupe de sécurité réseau, utilisez la commande suivante :

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Cette commande retourne une valeur semblable au texte suivant :

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Utilisez des guillemets autour des ID dans la commande si vous n’obtenez pas les résultats attendus.

4. Pour appliquer un groupe de sécurité réseau à un sous-réseau, utilisez la commande suivante. Remplacez les valeurs __GUID__ et __RESOURCEGROUPNAME__ par celles renvoyées à l’étape précédente. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom de réseau virtuel et le nom de sous-réseau que vous souhaitez créer.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Une fois l’exécution de cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel.

> [!IMPORTANT]
> Ces étapes donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Tout autre accès au cluster HDInsight à partir de l’extérieur du réseau virtuel est bloqué. Pour activer l’accès depuis l’extérieur du réseau virtuel, vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau.
>
> L’exemple suivant montre comment activer l’accès SSH depuis Internet :
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Exemple : configuration de DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Résolution de noms entre un réseau virtuel et un réseau local connecté

Cet exemple repose sur les hypothèses suivantes :

* Vous avez un réseau virtuel Azure qui est connecté à un réseau local à l’aide d’une passerelle VPN.

* Le serveur DNS personnalisé dans le réseau virtuel exécute le système d’exploitation Linux ou Unix.

* [Bind](https://www.isc.org/downloads/bind/) est installé sur le serveur DNS personnalisé.

Sur le serveur DNS personnalisé dans le réseau virtuel :

1. Utilisez Azure PowerShell ou Azure CLI pour rechercher le suffixe DNS du réseau virtuel :

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Sur le serveur DNS personnalisé pour le réseau virtuel, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.local` :

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Remplacez la valeur `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` par le suffixe DNS de votre réseau virtuel.

    Cette configuration a pour effet de router toutes les demandes DNS pour le suffixe DNS du réseau virtuel vers le programme de résolution récursive d’Azure.

2. Sur le serveur DNS personnalisé pour le réseau virtuel, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Remplacez la valeur `10.0.0.0/16` par la plage d'adresses IP de votre réseau virtuel. Cette entrée permet que la résolution de noms demande des adresses à l’intérieur de cette plage.

    * Ajoutez la plage d’adresses IP du réseau local à la section `acl goodclients { ... }`.  L’entrée autorise les demandes de résolution de noms en provenance de ressources dans le réseau local.
    
    * Remplacez la valeur `192.168.0.1` par l’adresse IP de votre serveur DNS local. Cette entrée a pour effet de router toutes les autres demandes DNS vers le serveur DNS local.

3. Pour utiliser la configuration, redémarrez Bind. Par exemple : `sudo service bind9 restart`.

4. Ajouter un redirecteur conditionnel au serveur DNS local. Configurez le redirecteur conditionnel de façon envoyer des demandes du suffixe DNS de l’étape 1 au serveur DNS personnalisé.

    > [!NOTE]
    > Pour plus de détails sur l’ajout d’un redirecteur conditionnel, consultez la documentation de votre logiciel DNS.

Après avoir suivi ces étapes, vous pouvez vous connecter aux ressources de chaque réseau à l’aide de noms de domaine complets (FQDN). Vous pouvez à présent installer HDInsight dans le réseau virtuel.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Résolution de noms entre deux réseaux virtuels connectés

Cet exemple repose sur les hypothèses suivantes :

* Vous avez deux réseaux virtuels Azure connectés à l’aide d’une passerelle VPN ou d’une homologation.

* Le serveur DNS personnalisé dans les deux réseaux exécute le système d’exploitation Linux ou Unix.

* [Bind](https://www.isc.org/downloads/bind/) est installé sur les serveurs DNS personnalisés.

1. Utilisez Azure PowerShell ou Azure CLI pour rechercher le suffixe DNS des deux réseaux virtuels :

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Sur le serveur DNS personnalisé, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.config.local`. Apportez cette modification sur le serveur DNS personnalisé dans les deux réseaux virtuels.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Remplacez la valeur `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` par le suffixe DNS de l’__autre__ réseau virtuel. Cette entrée a pour effet de router les demandes du suffixe DNS du réseau distant vers le DNS personnalisé dans ce réseau.

3. Sur les serveurs DNS personnalisés dans les deux réseaux virtuels, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Remplacez les valeurs `10.0.0.0/16` et `10.1.0.0/16` par les plages d’adresses IP de vos réseaux virtuels. Cette entrée a pour effet de permettre aux ressources de chaque réseau d’effectuer des demandes des serveurs DNS.

    Toute demande n’ayant pas trait au suffixes DNS des réseaux virtuels (par exemple, microsoft.com) est gérée par le programme de résolution récursive d’Azure.

4. Pour utiliser la configuration, redémarrez Bind. Par exemple, `sudo service bind9 restart` sur les deux serveurs DNS.

Après avoir suivi ces étapes, vous pouvez vous connecter aux ressources du réseau virtuel à l’aide de noms de domaine complets (FQDN). Vous pouvez à présent installer HDInsight dans le réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

* Pour un exemple de bout en bout de configuration de HDInsight pour se connecter à un réseau local, voir [Connecter HDInsight à un réseau local](./connect-on-premises-network.md).
* Pour configurer des clusters Hbase dans des réseaux virtuels Azure, consultez [Créer des clusters HBase sur HDInsight dans Réseau virtuel Azure](hbase/apache-hbase-provision-vnet.md).
* Pour configurer la géoréplication HBase, consultez [Configurer la réplication de cluster HBase dans les réseaux virtuels Azure](hbase/apache-hbase-replication.md).
* Pour plus d’informations sur les réseaux virtuels Azure, voir [Vue d'ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

* Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

* Pour plus d’informations sur les routages par l’utilisateur, consultez [Routage définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).
