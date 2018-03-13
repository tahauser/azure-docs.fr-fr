---
title: "Haute disponibilité pour Hadoop - Azure HDInsight | Documents Microsoft"
description: "Découvrez comment les clusters HDInsight améliorent la fiabilité et la disponibilité en utilisant un nœud principal supplémentaire. Découvrez dans quelle mesure les services Hadoop tels qu’Ambari et Hive sont concernés, et comment se connecter à chaque nœud principal via SSH."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
keywords: "haute disponibilité hadoop"
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/08/2018
ms.author: larryfr
ms.openlocfilehash: 0822502bcd3e2b0d20f718a158d6db489a650bde
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Disponibilité et fiabilité des clusters Hadoop dans HDInsight

Les clusters HDInsight fournissent deux nœuds principaux afin d’augmenter la disponibilité et la fiabilité des services et travaux Hadoop en cours d’exécution.

Hadoop garantit de hauts niveaux de disponibilité et de fiabilité en répliquant des services et données sur plusieurs nœuds d’un cluster. Toutefois, les distributions standard de Hadoop ne comportent généralement qu’un seul nœud principal. Toute défaillance du nœud principal unique peut entraîner un arrêt de fonctionnement du cluster. HDInsight fournit deux nœuds principaux pour améliorer la disponibilité et la fiabilité de Hadoop.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Disponibilité et fiabilité des nœuds

Les nœuds d’un cluster HDInsight sont implémentés à l’aide de machines virtuelles Azure. Les sections ci-après décrivent les différents types de nœuds utilisés avec HDInsight. 

> [!NOTE]
> Les types de nœuds utilisables varient selon le type de cluster concerné. Par exemple, un type de cluster Hadoop ne comporte aucun nœud Nimbus. Pour plus d’informations sur les nœuds utilisés par les types de clusters HDInsight, consultez la section Types de cluster du document [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

### <a name="head-nodes"></a>Nœuds principaux

Pour garantir une haute disponibilité des services Hadoop, HDInsight fournit deux nœuds principaux. Les deux nœuds principaux sont actifs et s’exécutent simultanément sur le cluster HDInsight. Certains services, par exemple HDFS et YARN, ne sont « actifs » que sur un seul nœud principal à un instant t. D'autres services tels que HiveServer2 ou Hive MetaStore sont actifs sur les deux nœuds principaux simultanément.

Les nœuds principaux (et les autres nœuds dans HDInsight) possèdent une valeur numérique comme partie du nom d’hôte du nœud. Par exemple, `hn0-CLUSTERNAME` ou `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> N’associez pas la valeur numérique avec l’information selon laquelle un nœud est principal ou secondaire. La valeur numérique est uniquement présente afin de fournir un nom unique à chaque nœud.

### <a name="nimbus-nodes"></a>Nœuds Nimbus

Des nœuds Nimbus sont disponibles avec les clusters Storm. Les nœuds Nimbus offrent des fonctionnalités comparables au service Hadoop JobTracker en distribuant et en surveillant le traitement sur l’ensemble des nœuds de travail. HDInsight fournit deux nœuds Nimbus pour les clusters Storm

### <a name="zookeeper-nodes"></a>Nœuds Zookeeper

Des nœuds [ZooKeeper](http://zookeeper.apache.org/) sont utilisés pour l’élection de leader des services maîtres sur nœuds principaux. Ils sont également utilisés pour s’assurer que les services, les nœuds de données (worker) et les passerelles sachent sur quel nœud principal un service maître est actif. Par défaut, HDInsight fournit trois nœuds ZooKeeper.

### <a name="worker-nodes"></a>Nœuds de travail

Les nœuds de travail effectuent l’analyse de données proprement dite lorsqu’un travail est soumis au cluster. En cas de défaillance d’un nœud de travail, la tâche en cours d’exécution sur ce dernier est soumise à un autre nœud de travail. Par défaut, HDInsight crée quatre nœuds de travail. Vous pouvez modifier ce chiffre en fonction vos besoins, pendant et après la création du cluster.

### <a name="edge-node"></a>Nœud de périmètre

Un nœud ne participe pas activement à l’analyse de données au sein du cluster. Il est utilisé par les développeurs ou chercheurs de données qui travaillent avec Hadoop. Le nœud de périmètre se trouve dans le même réseau virtuel Azure que les autres nœuds du cluster et peut accéder directement à tous les autres nœuds. Le nœud Edge peut être utilisé sans qu’il faille recourir à des ressources de services Hadoop ou de tâches d’analyse critiques.

Pour l’instant, R Server sur HDInsight est le seul type de cluster à fournir un nœud de périmètre par défaut. Dans le cas de R Server sur HDInsight, le nœud de périmètre est utilisé pour tester le code R localement sur le nœud avant de le soumettre au cluster à des fins de traitement distribué.

Pour plus d’informations sur l’utilisation d’un nœud de périmètre avec des types de clusters autres que R Server, consultez le document [Utiliser des nœuds de périmètre dans HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Accès aux nœuds

L’accès au cluster par Internet est fourni par le biais d’une passerelle publique. L’accès est limité à la connexion aux nœuds principaux et (s’il en existe un) au nœud de périmètre. L’accès aux services qui s’exécutent sur les nœuds principaux n’est pas affecté par la présence de plusieurs nœuds principaux. La passerelle publique achemine les demandes vers le nœud principal qui héberge le service demandé. Par exemple, si Ambari est actuellement hébergé sur le nœud principal secondaire, la passerelle achemine les demandes entrantes pour Ambari vers ce nœud.

L’accès via la passerelle publique est limité aux ports 443 (HTTPS), 22 et 23.

* Le port __443__ permet d’accéder à Ambari et à d’autres interfaces utilisateur web ou API REST hébergées sur les nœuds principaux.

* Le port __22__ est utilisé pour accéder au nœud principal primaire ou au nœud de périmètre via SSH.

* Le port __23__ est utilisé pour accéder au nœud principal secondaire via SSH. Par exemple, `ssh username@mycluster-ssh.azurehdinsight.net` établit une connexion au nœud principal primaire du cluster nommé **mycluster**.

Pour plus d’informations sur l’utilisation de SSH, consultez le document [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Noms de domaine pleinement qualifiés internes (FQDN)

Les nœuds présents dans un cluster HDInsight sont dotés d’une adresse IP interne et d’un nom de domaine complet uniquement accessibles à partir du cluster. Lorsque vous accédez à des services sur le cluster à l'aide de l'adresse IP ou du nom de domaine complet interne, vous devez utiliser Ambari pour vérifier l'adresse IP ou le nom de domaine complet à utiliser.

Par exemple, le service Oozie peut s'exécuter uniquement sur un nœud principal et l'utilisation de la commande `oozie` à partir d'une session SSH requiert l'URL du service. Cette URL peut être extraite d’Ambari avec la commande suivante :

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Cette commande renvoie une valeur qui se présente comme la commande suivante, qui contient l’URL interne à utiliser avec la commande `oozie` :

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Pour plus d’informations sur l’API REST Ambari, consultez la page [Surveiller et gérer HDInsight avec l’API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Accès à d’autres types de nœuds

Vous pouvez vous connecter aux nœuds qui ne sont pas directement accessibles sur Internet en utilisant les méthodes suivantes :

* **SSH** : une fois connecté à un nœud principal au moyen de SSH, vous pouvez utiliser SSH à partir de ce nœud principal pour vous connecter à d’autres nœuds du cluster. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Tunnel SSH** : si vous avez besoin d’accéder à un service web hébergé sur l’un des nœuds qui ne sont pas exposés à Internet, vous devez utiliser un tunnel SSH. Pour plus d’informations, consultez le document [Utiliser un tunnel SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

* **Réseau virtuel Azure** : si votre cluster HDInsight fait partie intégrante d’un réseau virtuel Azure, toutes les ressources du même réseau virtuel peuvent accéder directement à tous les nœuds du cluster. Pour plus d’informations, consultez le document [Étendre HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="how-to-check-on-a-service-status"></a>Comment contrôler  l'état d'un service

Pour vérifier l’état des services qui s’exécutent sur les nœuds principaux, utilisez l’interface utilisateur web d’Ambari ou l’API REST Ambari.

### <a name="ambari-web-ui"></a>Interface utilisateur web d'Ambari

Ouvrez l’interface utilisateur web d’Ambari à l’adresse https://CLUSTERNAME.azurehdinsight.net. Remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, saisissez les informations d'identification utilisateur de votre cluster. Le nom d'utilisateur HTTP par défaut est **admin** et le mot de passe est le mot de passe que vous avez saisi lors de la création du cluster.

Lorsque vous arrivez sur la page Ambari, les services installés apparaissent à gauche de la page.

![Services installés](./media/hdinsight-high-availability-linux/services.png)

Une série d'icônes s'affichent en regard d'un service pour indiquer son état. Des alertes liées à un service peuvent être affichées à l'aide du lien **Alertes** situé en haut de la page. Vous pouvez sélectionner chaque service pour afficher plus d'informations sur ce dernier.

La page de service fournit des informations sur l'état et la configuration de chaque service. Il ne fournit pas d'informations sur le nœud principal sur lequel le service s'exécute. Pour afficher ces informations, utilisez le lien **Hôtes** en haut de la page. Cette page affiche les hôtes au sein du cluster, notamment les nœuds principaux.

![liste des hôtes](./media/hdinsight-high-availability-linux/hosts.png)

Sélectionner le lien de l’un des nœuds principaux permet d’afficher les services et les composants qui s’exécutent sur ce nœud.

![État du composant](./media/hdinsight-high-availability-linux/nodeservices.png)

Pour plus d’informations sur Ambari, consultez la page [Surveiller et gérer HDInsight avec l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API Ambari REST

L’API REST Ambari est disponible sur internet. La passerelle publique HDInsight gère l’acheminement des demandes vers le nœud principal hébergeant l’API REST.

Vous pouvez utiliser la commande suivante pour vérifier l'état d'un service via l'API REST Ambari :

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Remplacez **PASSWORD** par le mot de passe du compte d’utilisateur HTTP (admin).
* Remplacez **CLUSTERNAME** par le nom de votre cluster.
* Remplacez **SERVICENAME** par le nom du service dont vous voulez contrôler l’état.

Par exemple, pour vérifier l’état du service **HDFS** dans un cluster nommé **mycluster**, avec un mot de passe **password**, vous devez utiliser la commande suivante :

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La réponse se présente comme le JSON suivant :

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

L’URL indique que le service est en cours d’exécution sur un nœud principal nommé **hn0-CLUSTERNAME**.

L'URL indique que le service est en cours d'exécution ou **démarré**.

Si vous ne connaissez pas les services installés sur le cluster, vous pouvez utiliser la commande suivante pour en récupérer la liste :

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Pour plus d’informations sur l’API REST Ambari, consultez la page [Surveiller et gérer HDInsight avec l’API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Composants du service

Les services peuvent contenir des composants dont vous souhaitez vérifier l'état individuellement. Par exemple, HDFS contient le composant NameNode. Pour afficher des informations relatives à un composant, la commande serait :

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Si vous ne connaissez pas les composants fournis par un service, vous pouvez utiliser la commande suivante pour en récupérer la liste :

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Accès aux fichiers journaux sur les nœuds principaux

### <a name="ssh"></a>SSH

Lorsque vous êtes connecté à un nœud principal via SSH, les fichiers journaux se trouvent sous **/var/log**. Par exemple, **/var/log/hadoop-yarn/yarn** contiennent les journaux correspondant à YARN.

Chaque nœud principal peut contenir des entrées de journal uniques. Vous devez donc vérifier les journaux correspondant aux deux.

### <a name="sftp"></a>SFTP

Vous pouvez également vous connecter au nœud principal à l’aide du protocole FTP SSH, ou protocole FTP sécurisé (SFTP), et télécharger directement les fichiers journaux.

Comme lors de l’utilisation d’un client SSH, lorsque vous vous connectez au cluster, vous devez fournir le nom du compte d’utilisateur SSH et l’adresse SSH du cluster. Par exemple, `sftp username@mycluster-ssh.azurehdinsight.net`. Lorsque vous y êtes invité, fournissez le mot de passe du compte ou une clé publique à l’aide du paramètre `-i`.

Une fois connecté, vous voyez apparaître une invite `sftp>` . À partir de cette invite, vous pouvez changer de répertoire, ainsi que charger et télécharger des fichiers. Par exemple, les commandes ci-après activent le répertoire **/var/log/hadoop/hdfs** , puis téléchargent tous les fichiers dans ce répertoire.

    cd /var/log/hadoop/hdfs
    get *

Pour obtenir la liste des commandes disponibles, entrez `help` au niveau de l’invite `sftp>`.

> [!NOTE]
> Il existe également des interfaces graphiques qui vous permettent de visualiser le système de fichiers lorsque vous êtes connecté à l’aide du protocole SFTP. Par exemple, [MobaXTerm](http://mobaxterm.mobatek.net/) vous offre la possibilité de parcourir le système de fichiers au moyen d’une interface semblable à l’Explorateur Windows.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Pour accéder aux fichiers journaux avec Ambari, vous devez utiliser un tunnel SSH. Les interfaces web des différents services ne sont pas exposée publiquement sur Internet. Pour plus d’informations sur l’utilisation du tunnel SSH, voir le document [Utilisation d’un Tunneling SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Dans l’interface utilisateur web d’Ambari, sélectionnez le service dont vous souhaitez afficher les journaux (par exemple, YARN). Utilisez ensuite les **liens rapides** pour sélectionner le nœud principal pour lequel vous souhaitez afficher les journaux.

![Utilisation des liens rapides pour afficher les journaux](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Configuration de la taille des nœuds

La taille d’un nœud n’est sélectionnable que lors de la création du cluster. Pour obtenir la liste des différentes tailles de machine virtuelle disponibles pour HDInsight, voir la page [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Lorsque vous créez un cluster, vous pouvez spécifier la taille des nœuds. Les informations suivantes aident à spécifier la taille avec le [Portail Azure][preview-portal], [Azure PowerShell][azure-powershell] et [l’interface de ligne de commande Azure][azure-cli] :

* **Portail Azure** : à la création d’un cluster, vous pouvez définir la taille des nœuds utilisés par le cluster :

    ![Image de l'Assistant de création de cluster avec sélection de taille de nœud](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Interface de ligne de commande Azure** : lorsque vous utilisez la commande `azure hdinsight cluster create`, vous pouvez définir la taille des nœuds principaux, de travail et ZooKeeper en utilisant les paramètres `--headNodeSize`, `--workerNodeSize` et `--zookeeperNodeSize`.

* **Azure PowerShell** : lorsque vous utilisez l’applet de commande `New-AzureRmHDInsightCluster`, vous pouvez définir la taille des nœuds principaux, de travail et ZooKeeper en utilisant les paramètres `-HeadNodeVMSize`, `-WorkerNodeSize` et `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens suivants pour en savoir plus sur les éléments mentionnés dans ce document.

* [Référence REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installation et configuration Azure CLI](../cli-install-nodejs.md)
* [Installation et configuration d'Azure PowerShell](/powershell/azure/overview)
* [Gestion de HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md)
* [Approvisionnement de clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
