---
title: Connecter HDInsight à votre réseau local - Azure HDInsight | Microsoft Docs
description: Découvrez comment créer un cluster HDInsight dans un réseau virtuel Azure, puis le connecter à votre réseau local. Découvrez comment configurer une résolution de noms entre HDInsight et votre réseau local à l’aide d’un serveur DNS personnalisé.
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: 9470e052b4f57e52fa140b53fa7c32d199c58e1e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>Connecter HDInsight à votre réseau local

Découvrez comment connecter HDInsight à votre réseau local à l’aide de réseaux virtuels Azure et d’une passerelle VPN. Ce document fournit des informations de planification concernant :

* l’utilisation de HDInsight dans un réseau virtuel Azure qui se connecte à votre réseau local ;

* la configuration de la résolution de noms DNS entre le réseau virtuel et votre réseau local ;

* la configuration de groupes de sécurité réseau pour restreindre l’accès Internet à HDInsight ;

* les ports fournis par HDInsight sur le réseau virtuel.

## <a name="create-the-virtual-network-configuration"></a>Créer la configuration de réseau virtuel

Pour découvrir comment créer un réseau virtuel Azure connecté à votre réseau local, consultez les documents suivants :
    
* [Utilisation du portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Utilisation de Microsoft Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Utilisation de l’interface de ligne de commande Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Configurer la résolution de noms

Pour permettre à HDInsight et aux ressources du réseau joint de communiquer par nom, vous devez effectuer les actions suivantes :

* créer un serveur DNS personnalisé dans le réseau virtuel Azure ;

* configurer le réseau virtuel pour utiliser le serveur DNS personnalisé au lieu du programme de résolution récursive Azure par défaut ;

* configurer le transfert entre le serveur DNS personnalisé et votre serveur DNS local.

Cette configuration active le comportement suivant :

* Les demandes de noms de domaine complets (FQDN) qui ont le suffixe DNS __pour le réseau virtuel__ sont transférées au serveur DNS personnalisé. Le serveur DNS personnalisé transfère ensuite ces demandes au programme de résolution récursive Azure qui retourne l’adresse IP.

* Toutes les autres demandes sont transférées au serveur DNS local. Même les demandes de ressources Internet publiques telles que microsoft.com sont transférées au serveur DNS local pour la résolution de noms.

Dans le schéma suivant, les lignes vertes correspondent à des demandes de ressources qui se terminent par le suffixe DNS du réseau virtuel. Les lignes bleues correspondent à des demandes de ressources dans le réseau local ou sur l’Internet public.

![Schéma illustrant la façon dont les demandes de DNS sont résolues dans la configuration utilisée dans ce document](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Créer un serveur DNS personnalisé

> [!IMPORTANT]
> Vous devez créer et configurer le serveur DNS avant d’installer HDInsight dans le réseau virtuel.

Pour créer une machine virtuelle Linux utilisant le logiciel DNS [Bind](https://www.isc.org/downloads/bind/), procédez comme suit :

> [!NOTE]
> Les étapes suivantes utilisent le [portail Azure](https://portal.azure.com) pour créer une machine virtuelle Azure. Pour d’autres méthodes permettant de créer une machine virtuelle, consultez les documents suivants :
>
> * [Créer une machine virtuelle - Interface de ligne de commande Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Créer une machine virtuelle - Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez __+__, __Compute__, puis __Ubuntu Server 16.04 LTS__.

    ![Créer une machine virtuelle Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Dans la section __De base__, entrez les informations suivantes :

    * __Nom__ : nom convivial identifiant cette machine virtuelle. Par exemple, __DNSProxy__.
    * __Nom d’utilisateur__ : nom du compte SSH.
    * __Clé publique SSH__ ou __Mot de passe__ : méthode d’authentification pour le compte SSH. Nous recommandons d’utiliser des clés publiques, car elles sont plus sécurisées. Pour plus d’informations, consultez [Créer et utiliser des clés SSH pour des machines virtuelles Linux](../virtual-machines/linux/mac-create-ssh-keys.md).
    * __Groupe de ressources__ : sélectionnez __Utiliser l’existant__, puis sélectionnez le groupe de ressources contenant le réseau virtuel créé précédemment.
    * __Emplacement__ : sélectionnez l’emplacement du réseau virtuel.

    ![Configuration de base de machine virtuelle](./media/connect-on-premises-network/vm-basics.png)

    Conservez les valeurs par défaut des autres entrées, puis sélectionnez __OK__.

3. Dans la section __Choisir une taille__, sélectionnez la taille de machine virtuelle. Pour ce tutoriel, sélectionnez la taille la plus petite et la moins coûteuse. Pour continuer, utilisez le bouton __Sélectionner__.

4. Dans la section __Paramètres__, entrez les informations suivantes :

    * __Réseau virtuel__ : sélectionnez le réseau virtuel que vous avez créé précédemment.

    * __Sous-réseau__ : sélectionnez le sous-réseau par défaut pour le réseau virtuel. Ne sélectionnez __pas__ le sous-réseau utilisé par la passerelle VPN.

    * __Compte de stockage des diagnostics__ : sélectionnez ou créez un compte de stockage.

    ![Paramètres de réseau virtuel](./media/connect-on-premises-network/virtual-network-settings.png)

    Conservez les valeurs par défaut des autres entrées, puis sélectionnez __OK__ pour continuer.

5. Dans la section __Acheter__, cliquez sur le bouton __Acheter__ pour créer la machine virtuelle.

6. Une fois la machine virtuelle créée, sa section __Vue d’ensemble__ s’affiche. Dans la liste à gauche, sélectionnez __Propriétés__. Enregistrez les valeurs __Adresse IP publique__ et __Adresse IP privée__. Elles seront utilisées dans la section suivante.

    ![Adresses IP publique et privée](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Installer et configurer Bind (logiciel DNS)

1. Utilisez SSH pour vous connecter à l’__adresse IP publique__ de la machine virtuelle. L’exemple suivant établit une connexion à une machine virtuelle dont l’adresse IP est 40.68.254.142 :

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Remplacez `sshuser` par le compte d’utilisateur SSH que vous avez spécifié lors de la création du cluster.

    > [!NOTE]
    > Il existe plusieurs façons d’obtenir l’utilitaire `ssh`. Sur Linux, Unix et macOS, il est généralement fourni en tant que composant du système d’exploitation. Si vous utilisez Windows, envisagez l’une des options suivantes :
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash sur Ubuntu sur Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Pour installer Bind, utilisez les commandes suivantes à partir de la session SSH :

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Pour configurer Bind afin de transférer les demandes de résolution de noms à votre serveur DNS local, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.options` :

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]
    > Remplacez les valeurs de la section `goodclients` par la plage d’adresses IP du réseau virtuel et du réseau local. Cette section définit les adresses d’origine des demandes que ce serveur DNS accepte.
    >
    > Remplacez l’entrée `192.168.0.1` dans la section `forwarders` par l’adresse IP de votre serveur DNS local. Cette entrée route les demandes DNS vers votre serveur DNS local pour la résolution.

    Pour modifier ce fichier, utilisez la commande suivante :

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Pour enregistrer le fichier, appuyez sur __Ctrl+X__, __O__, puis __Entrée__.

4. À partir de la session SSH, utilisez la commande suivante :

    ```bash
    hostname -f
    ```

    Cette commande retourne une valeur semblable au texte suivant :

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    Le texte `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` est le __suffixe DNS__ de ce réseau virtuel. Enregistrez cette valeur, car vous l’utiliserez plus tard.

5. Pour configurer Bind afin de résoudre les noms DNS pour les ressources au sein du réseau virtuel, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.local` :

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Vous devez remplacer `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` par le suffixe DNS que vous avez récupéré précédemment.

    Pour modifier ce fichier, utilisez la commande suivante :

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Pour enregistrer le fichier, appuyez sur __Ctrl+X__, __O__, puis __Entrée__.

6. Pour démarrer Bind, utilisez la commande suivante :

    ```bash
    sudo service bind9 restart
    ```

7. Pour vérifier que Bind peut résoudre les noms de ressources de votre réseau local, utilisez les commandes suivantes :

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Remplacez `dns.mynetwork.net` par le nom de domaine complet (FQDN) d’une ressource de votre réseau local.
    >
    > Remplacez `10.0.0.4` par __l’adresse IP interne__ de votre serveur DNS personnalisé dans le réseau virtuel.

    La réponse est similaire au texte suivant :

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Configurer le réseau virtuel pour utiliser le serveur DNS personnalisé

Pour configurer le réseau virtuel afin d’utiliser le serveur DNS personnalisé au lieu du programme de résolution récursive Azure, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le réseau virtuel, puis choisissez __Serveurs DNS__.

2. Sélectionnez __Personnalisé__, puis entrez l’__adresse IP interne__ du serveur DNS personnalisé. Enfin, sélectionnez __Enregistrer__.

    ![Définir le serveur DNS personnalisé pour le réseau](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Configurer le serveur DNS local

Dans la section précédente, vous avez configuré le serveur DNS personnalisé de façon à transférer les demandes au serveur DNS local. Ensuite, vous devez configurer le serveur DNS local pour transférer les demandes au serveur DNS personnalisé.

Pour savoir comment configurer votre serveur DNS, consultez la documentation relative au logiciel de votre serveur DNS. Recherchez la procédure à suivre pour configurer un __redirecteur conditionnel__.

Un redirecteur conditionnel transfère uniquement les demandes relatives à un suffixe DNS spécifique. En l’occurrence, vous devez configurer un redirecteur pour le suffixe DNS du réseau virtuel. Les demandes relatives à ce suffixe doivent être transmises à l’adresse IP du serveur DNS personnalisé. 

Le texte suivant est un exemple de configuration de redirecteur conditionnel pour le logiciel DNS **Bind** :

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Pour plus d’informations sur l’utilisation de DNS sur **Windows Server 2016**, consultez la documentation [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone).

Une fois que vous avez configuré le serveur DNS local, vous pouvez utiliser `nslookup` à partir du réseau local pour vérifier que vous pouvez résoudre les noms dans le réseau virtuel. Par exemple : 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Cet exemple utilise le serveur DNS local à l’adresse 196.168.0.4 pour résoudre le nom du serveur DNS personnalisé. Remplacez l’adresse IP par celle du serveur DNS local. Remplacez l’adresse `dnsproxy` par le nom de domaine complet (FQDN) du serveur DNS personnalisé.

## <a name="optional-control-network-traffic"></a>Facultatif : contrôler le trafic réseau

Vous pouvez utiliser des groupes de sécurité réseau (NSG) ou des routages définis par l’utilisateur (UDR) pour contrôler le trafic réseau. Les groupes de sécurité réseau permettent de filtrer le trafic entrant et sortant, ainsi que d’autoriser ou refuser le trafic. Les routages définis par l’utilisateur vous permettent de contrôler le flux du trafic entre les ressources du réseau virtuel, Internet et le réseau local.

> [!WARNING]
> HDInsight requiert un accès entrant en provenance d’adresses IP spécifiques dans le cloud Azure, et un accès sortant non restreint. Lorsque vous utilisez des groupes de sécurité réseau ou des routages définis par l’utilisateur pour contrôler le trafic, procédez comme suit :

1. Recherchez les adresses IP de l’emplacement contenant votre réseau virtuel. Pour obtenir la liste des adresses IP requises par emplacement, consultez [Adresses IP requises](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Pour les adresses IP identifiées à l’étape 1, autorisez le trafic entrant à partir de ces adresses.

   * Si vous utilisez __NSG__ : autorisez le trafic __entrant__ sur le port __443__ pour les adresses IP.
   * Si vous utilisez __UDR__ : définissez le type __Tronçon suivant__ de l’itinéraire vers __Internet__ pour les adresses IP.

Pour un exemple d’utilisation d’Azure PowerShell ou d’Azure CLI afin de créer des groupes de sécurité réseau, consultez [Étendre HDInsight avec des réseaux virtuels Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg).

## <a name="create-the-hdinsight-cluster"></a>Créer le cluster HDInsight

> [!WARNING]
> Vous devez configurer le serveur DNS personnalisé avant d’installer HDInsight sur le réseau virtuel.

Pour créer un cluster HDInsight, procédez de la manière décrite dans [Créer un cluster HDInsight à l’aide du portail Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

> [!WARNING]
> * Lors de la création du cluster, vous devez choisir l’emplacement contenant votre réseau virtuel.
>
> * Dans les __Paramètres avancés__ de la configuration, vous devez sélectionner le réseau virtuel et le sous-réseau que vous avez créés précédemment.

## <a name="connecting-to-hdinsight"></a>Connexion à HDInsight

L’essentiel de la documentation relative à HDInsight part du principe que vous avez accès au cluster via internet. Par exemple, vous pouvez vous connecter au cluster à l’adresse https://CLUSTERNAME.azurehdinsight.net. Cette adresse utilise la passerelle publique qui n’est pas disponible si vous avez utilisé des groupes de sécurité réseau ou des itinéraires définis par l’utilisateur pour restreindre l’accès à partir d’Internet.

Certains documents font également référence à `headnodehost` lors de la connexion au cluster à partir d’une session SSH. Cette adresse est uniquement disponible à partir des nœuds d’un cluster, et elle n’est pas utilisable sur les clients connectés au réseau virtuel.

Pour vous connecter directement à HDInsight par le biais du réseau virtuel, procédez comme suit :

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

2. Pour déterminer le port sur lequel un service est disponible, consultez [Ports utilisés par les services Hadoop dans HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

    > [!IMPORTANT]
    > Certains services hébergés sur les nœuds principaux ne sont actifs que sur un seul nœud à la fois. Si vous tentez d’accéder à un service sur un nœud principal et que l’opération échoue, basculez vers l’autre nœud principal.
    >
    > Par exemple, Ambari n’est actif que sur un nœud principal à la fois. Si vous tentez d’accéder à Ambari sur un nœud principal et recevez une erreur 404, cela signifie qu’Ambari s’exécute sur l’autre nœud principal.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation de HDInsight dans un réseau virtuel, consultez [Étendre HDInsight à l’aide de réseaux virtuels Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Pour plus d’informations sur les réseaux virtuels Azure, consultez [Vue d'ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

* Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

* Pour plus d’informations sur les routages par l’utilisateur, consultez [Routage définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).
