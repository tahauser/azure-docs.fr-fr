---
title: "Création de clusters HBase sur un réseau virtuel - Azure | Microsoft Docs"
description: "Prise en main de HBase dans Azure HDInsight Découvrez comment créer des clusters HDInsight HBase sur Azure Virtual Network."
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 08f71340fea7424262fc9c549351bd50bcfcafe2
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Création de clusters HBase sur HDInsight dans un réseau virtuel Microsoft Azure
Découvrez comment créer des clusters Azure HDInsight sur [Azure Virtual Network][1].

Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase. Voici les avantages :

* Connectivité directe de l’application web aux nœuds du cluster HBase, ce qui permet les communications au moyen d’API d’appel de procédure distante (RPC) Java HBase.
* Amélioration des performances en évitant à votre trafic de transiter par plusieurs passerelles et équilibrages de charge.
* Capacité de traitement des informations critiques de façon plus sécurisée sans exposer de points de terminaison publics.

### <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Création de clusters HBase sur un réseau virtuel
Dans cette section, vous allez créer un cluster HBase basé sur Linux avec un compte de stockage Azure dépendant dans un réseau virtuel Azure à l’aide d’un [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Pour d'autres méthodes de création de cluster et comprendre les paramètres, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation d’un modèle pour créer des clusters Hadoop dans HDInsight, consultez [Création de clusters Hadoop dans HDInsight à l’aide de modèles Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Certaines propriétés sont codées en dur dans le modèle. Par exemple : 
>
> * **Emplacement** : Est des États-Unis 2
> * **Version du cluster** : 3.6
> * **Nombre de nœuds de travail du cluster** : 2
> * **Compte de stockage par défaut** : une chaîne unique
> * **Nom du réseau virtuel** : &lt;Nom du cluster&gt;-réseau virtuel
> * **Espace d’adressage du réseau virtuel** : 10.0.0.0/16
> * **Nom du sous-réseau** : subnet1
> * **Plage d’adresses de sous-réseau** : 10.0.0.0/24
>
> La valeur &lt;Nom du cluster> est remplacée par le nom de cluster que vous fournissez lors de l’utilisation du modèle.
>
>

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Ce modèle se trouve dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Dans le panneau **Déploiement personnalisé**, entrez les propriétés suivantes :

   * **Abonnement** : sélectionnez un abonnement Azure utilisé pour créer le cluster HDInsight, le compte de stockage dépendant et le réseau virtuel Azure.
   * **Groupe de ressources** : sélectionnez **Créer** et donnez un nouveau nom au groupe de ressources.
   * **Emplacement** : sélectionnez un emplacement pour le groupe de ressources.
   * **ClusterName** : entrez un nom pour le cluster Hadoop à créer.
   * **Nom d’utilisateur et mot de passe de cluster** : le nom de connexion par défaut est **admin**.
   * **Nom d’utilisateur SSH et mot de passe** : le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez le renommer.
   * **J’accepte les termes et conditions mentionnés ci-dessus** : (sélectionner)
3. Cliquez sur **Acheter**. La création d’un cluster prend environ 20 minutes. Une fois le cluster créé, vous pouvez cliquer sur le panneau du cluster dans le portail pour l’ouvrir.

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Pour obtenir des instructions sur la suppression d’un cluster, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

Pour commencer à utiliser votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans la rubrique [Prise en main de HBase avec Hadoop dans HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Connexion au cluster HBase avec les API RPC Java HBase
1. Créez une machine virtuelle IaaS dans le même réseau virtuel Azure et le même sous-réseau. Pour plus d’informations sur la création d’une machine virtuelle IaaS, consultez [Création d’une machine virtuelle exécutant Windows Server](../../virtual-machines/windows/quick-create-portal.md). Lors des étapes décrites dans ce document, vous devez utiliser les valeurs suivantes pour la configuration du réseau :

   * **Réseau virtuel** : &lt;Nom du cluster&gt;-réseau virtuel
   * **Sous-réseau** : subnet1

   > [!IMPORTANT]
   > Remplacez &lt;Nom du cluster> par le nom que vous avez utilisé lors de la création du cluster HDInsight dans les étapes précédentes.
   >
   >

   À l’aide de ces valeurs, la machine virtuelle est placée dans le même réseau virtuel et le même sous-réseau que ceux du cluster HDInsight. Cette configuration leur permet de communiquer directement entre eux. Il existe un moyen de créer un cluster HDInsight avec un nœud de périphérie vide. Le nœud de périphérie peut servir à gérer le cluster.  Pour plus d’informations, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](../hdinsight-apps-use-edge-node.md).

2. Lorsque vous utilisez une application Java pour vous connecter à distance à HBase, vous devez utiliser le nom de domaine complet. Pour déterminer cela, vous devez obtenir le suffixe DNS propre à la connexion du cluster HBase. Pour ce faire, vous pouvez utiliser l’une des méthodes suivantes :

   * Utiliser un navigateur Web pour effectuer un appel Ambari :

     Accédez à https://&lt;Nomducluster>.azurehdinsight.net/api/v1/clusters/&lt;Nomducluster>/hosts?minimal_response=true. Un fichier JSON avec les suffixes DNS est renvoyé.
   * Utiliser le site Web Ambari :

     1. Accédez à https://&lt;Nomducluster>.azurehdinsight.net.
     2. Cliquez sur **Hôtes** dans le menu supérieur.
   * Utiliser Curl pour effectuer des appels REST :

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     Dans les données JavaScript Object Notation (JSON) renvoyées, recherchez l’entrée « host_name ». Elle contient le nom de domaine complet des nœuds du cluster. Par exemple : 

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     La partie du nom de domaine commençant par le nom de cluster est le suffixe DNS. Par exemple, mon_cluster.b1.cloudapp.net.
   * Utilisation d'Azure PowerShell

     Utilisez le script Azure PowerShell suivant pour enregistrer la fonction **Get-ClusterDetail** , qui peut être utilisée pour renvoyer le suffixe DNS :

    ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
    ```

     Après avoir exécuté le script Azure PowerShell, utilisez la commande suivante pour renvoyer le suffixe DNS au moyen de la fonction **Get-ClusterDetail** . Spécifiez votre nom de cluster HDInsight HBase, ainsi que le nom et le mot de passe de l'administrateur lors de l'utilisation de cette commande.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     Cette commande renvoie le suffixe DNS. Par exemple, **votre_nom_cluster.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Pour vérifier que la machine virtuelle peut communiquer avec le cluster HBase, utilisez la commande `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mon_cluster.b1.cloudapp.net.

Pour utiliser ces informations dans une application Java, vous pouvez suivre la procédure décrite dans la rubrique [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) pour créer une application. Pour que l’application soit connectée à un serveur HBase distant, modifiez le fichier **hbase-site.xml** dans cet exemple afin d’utiliser le nom de domaine complet pour Zookeeper. Par exemple : 

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Pour plus d’informations sur la résolution de noms dans des réseaux virtuels Azure, y compris sur la façon d’utiliser votre propre serveur DNS, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer un cluster HBase. Pour plus d'informations, consultez les rubriques suivantes :

* [Prise en main de HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Utiliser des nœuds de périphérie vides dans HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configuration de la géo-réplication HBase dans HDInsigtht](apache-hbase-replication.md)
* [Créer des clusters Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Prise en main de HBase avec Hadoop dans HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Présentation du réseau virtuel.](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

