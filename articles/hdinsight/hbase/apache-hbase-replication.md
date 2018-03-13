---
title: "Configurer la réplication de cluster HBase dans les réseaux virtuels Azure | Microsoft Docs"
description: "Découvrez comment configurer la réplication HBase d’une version HDInsight à une autre pour l’équilibrage de charge, la haute disponibilité, la mise à jour et migration sans interruption de service , ainsi que la récupération d’urgence."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: b0a22815dc0bf0ea31e47efe5152498f9aa45de4
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurer la réplication de cluster HBase dans les réseaux virtuels Azure

Découvrez comment configurer la réplication HBase dans un réseau virtuel ou entre deux réseaux virtuels dans Azure.

La réplication en cluster utilise une méthodologie par émission de données source. Un cluster HBase peut être une source, une destination ou jouer les deux rôles à la fois. La réplication est asynchrone. L'objectif de la réplication est une cohérence éventuelle. Quand la source reçoit une modification apportée à une famille de colonnes lorsque la réplication activée, cette modification est propagée à tous les clusters de destination. Quand les données sont répliquées d’un cluster à un autre, le cluster source et tous les clusters qui ont déjà utilisé les données font l’objet d’un suivi afin d’empêcher les boucles de réplication.

Dans ce didacticiel, vous configurez une réplication source-destination. Pour d'autres topologies de cluster, consultez le [Guide de référence d'Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Cas d’utilisation de la réplication HBase pour un seul réseau virtuel :

* Équilibrage de charge : vous pouvez par exemple exécuter des analyses ou des travaux MapReduce sur le cluster de destination et ingérer des données sur le cluster source
* Ajout de haute disponibilité
* Migration de données d’un cluster HBase vers un autre
* Mise à niveau d’un cluster Azure HDInsight d’une version vers une autre

Cas d’utilisation de la réplication HBase pour deux réseaux virtuels :

* Configuration d’une récupération d'urgence
* Équilibrage de charge et partitionnement de l’application
* Ajout de haute disponibilité

Vous pouvez répliquer des clusters à l’aide de scripts [d’action de script](../hdinsight-hadoop-customize-cluster-linux.md) disponibles dans [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Consultez [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurer les environnements

Il existe trois options de configuration :

- Deux clusters HBase dans un réseau virtuel Azure
- Deux clusters HBase dans deux réseaux virtuels différents dans la même région
- Deux clusters HBase dans deux réseaux virtuels différents dans deux régions distinctes (géoréplication)

Pour vous aider à configurer les environnements, nous avons créé des [modèles Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Si vous préférez configurer les environnements en utilisant d’autres méthodes, consultez :

- [Créer des clusters Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
- [Création de clusters HBase dans un réseau virtuel Azure](apache-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Configurer un seul réseau virtuel

Pour créer deux clusters HBase dans le même réseau virtuel, sélectionnez l’image suivante. Ce modèle est stocké dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Configurer deux réseaux virtuels dans la même région

Pour créer deux réseaux virtuels avec homologation de réseaux virtuels et deux clusters HBase dans la même région, sélectionnez l’image suivante. Ce modèle est stocké dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Ce scénario nécessite l’[homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md). Le modèle permet l’homologation de réseaux virtuels.   

La réplication HBase utilise des adresses IP des machines virtuelles ZooKeeper. Vous devez configurer des adresses IP statiques pour les nœuds ZooKeeper HBase de destination.

**Pour configurer des adresses IP statiques**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources ayant le cluster HBase de destination. Il s’agit du groupe de ressources que vous avez spécifié lorsque vous avez utilisé le modèle Resource Manager pour créer l’environnement. Vous pouvez utiliser le filtre pour restreindre la liste. Vous pouvez voir une liste de ressources contenant les deux réseaux virtuels.
4. Sélectionnez le réseau virtuel qui contient le cluster HBase de destination. Par exemple, sélectionnez **xxxx-vnet2**. Trois appareils avec des noms qui commencent par **nic-zookeepermode-** apparaissent. Ces appareils sont les trois machines virtuelles ZooKeeper.
5. Sélectionnez l’une des machines virtuelles ZooKeeper.
6. Sélectionnez **Configurations IP**.
7. Dans la liste, sélectionnez **ipConfig1**.
8. Sélectionnez **Statique**et copiez ou notez l’adresse IP réelle. Lorsque vous exécutez l’action de script pour activer la réplication, vous avez besoin de l’adresse IP.

  ![Adresse IP statique ZooKeeper de réplication HDInsight HBase](./media/apache-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Répétez l’étape 6 pour définir l’adresse IP statique des deux autres nœuds ZooKeeper.

Pour le scénario de réseaux virtuels croisés, vous devez utiliser le commutateur **-ip** lors de l’appel de l’action de script `hdi_enable_replication.sh`.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurer deux réseaux virtuels dans deux régions différentes

Pour créer deux réseaux virtuels dans deux régions différentes, ainsi que la connexion VPN entre les deux, cliquez sur l’image suivante. Ce modèle est stocké dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Voici quelques-unes des valeurs codées en dur dans le modèle :

**Réseau virtuel 1**

| Propriété | Valeur |
|----------|-------|
| Lieu | États-Unis de l’Ouest |
| Nom du réseau virtuel | &lt;ClusterNamePrevix>-vnet1 |
| Préfixe de l’espace d’adressage | 10.1.0.0/16 |
| Nom du sous-réseau | subnet 1 |
| Préfixe du sous-réseau | 10.1.0.0/24 |
| Nom du sous-réseau (passerelle) | GatewaySubnet (non modifiable) |
| Préfixe du sous-réseau (passerelle) | 10.1.255.0/27 |
| Nom de la passerelle | vnet1gw |
| Type de passerelle | Vpn |
| Type de VPN de la passerelle | RouteBased |
| SKU de la passerelle | De base |
| Adresse IP de la passerelle | vnet1gwip |
| Nom du cluster | &lt;ClusterNamePrefix>1 |
| Version du cluster | 3.6 |
| Type de cluster | hbase |
| Nombre de nœuds de travail du cluster | 2 |


**Réseau virtuel 2**

| Propriété | Valeur |
|----------|-------|
| Lieu | Est des États-Unis |
| Nom du réseau virtuel | &lt;ClusterNamePrevix>-vnet2 |
| Préfixe de l’espace d’adressage | 10.2.0.0/16 |
| Nom du sous-réseau | subnet 1 |
| Préfixe du sous-réseau | 10.2.0.0/24 |
| Nom du sous-réseau (passerelle) | GatewaySubnet (non modifiable) |
| Préfixe du sous-réseau (passerelle) | 10.2.255.0/27 |
| Nom de la passerelle | vnet2gw |
| Type de passerelle | Vpn |
| Type de VPN de la passerelle | RouteBased |
| SKU de la passerelle | De base |
| Adresse IP de la passerelle | vnet1gwip |
| Nom du cluster | &lt;ClusterNamePrefix>2 |
| Version du cluster | 3.6 |
| Type de cluster | hbase |
| Nombre de nœuds de travail du cluster | 2 |

La réplication HBase utilise des adresses IP des machines virtuelles ZooKeeper. Vous devez configurer des adresses IP statiques pour les nœuds ZooKeeper HBase de destination. Pour configurer une adresse IP statique, consultez la section [Configurer deux réseaux virtuels dans la même région](#set-up-two-virtual-networks-in-the-same-region) de cet article.

Pour le scénario de réseaux virtuels croisés, vous devez utiliser le commutateur **-ip** lors de l’appel de l’action de script `hdi_enable_replication.sh`.

## <a name="load-test-data"></a>Charger les données de test

Lorsque vous répliquez un cluster, vous devez spécifier les tables à répliquer. Dans cette section, vous chargez des données dans le cluster source. Dans la section suivante, vous allez activer la réplication entre les deux clusters.

Pour créer une table **Contacts** et y insérer des données, suivez les instructions du [didacticiel HBase : bien démarrer avec Apache HBase dans HDInsight Linux](apache-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Activer la réplication

Les étapes suivantes décrivent comment appeler le script d’action de script à partir du portail Azure. Pour savoir comment exécuter une action de script à l’aide d’Azure PowerShell et de l’outil en ligne de commande Azure (Azure CLI), consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](../hdinsight-hadoop-customize-cluster-linux.md).

**Pour activer la réplication HBase à partir du portail Azure**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Ouvrez le cluster HBase source.
3. Dans le menu de cluster, sélectionnez **Actions de script**.
4. En haut de la page, sélectionnez **Soumettre nouveau**.
5. Sélectionnez ou saisissez les informations suivantes :

  1. **Nom** : saisissez **Activer la réplication**.
  2. **Bash Script URL (URL de script bash)** : saisissez **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Principal** : assurez-vous que cette option est sélectionnée. Supprimez les autres types de nœuds.
  4. **Paramètres** : les paramètres d’exemple suivants activent la réplication pour toutes les tables existantes, puis copient toutes les données du cluster source vers le cluster de destination :

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Utilisez le nom d’hôte plutôt que le nom de domaine complet (FQDN) pour le nom DNS du cluster source et du cluster de destination.

6. Sélectionnez **Créer**. L’exécution du script peut prendre un certain temps, en particulier lorsque vous utilisez l’argument **-copydata**.

Arguments requis :

|NOM|Description|
|----|-----------|
|-s, --src-cluster | Spécifie le nom DNS du cluster HBase source. Par exemple : -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Spécifie le nom DNS du cluster HBase de destination (réplica). Par exemple : -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Spécifie le mot de passe d’administrateur pour Ambari sur le cluster HBase source. |
|-dp, --dst-ambari-password | Spécifie le mot de passe d’administrateur pour Ambari sur le cluster HBase de destination.|

Arguments facultatifs :

|NOM|Description|
|----|-----------|
|-su, --src-ambari-user | Spécifie le nom d’utilisateur administrateur pour Ambari sur le cluster HBase source. La valeur par défaut est **admin**. |
|-du, --dst-ambari-user | Spécifie le nom d’utilisateur administrateur pour Ambari sur le cluster HBase de destination. La valeur par défaut est **admin**. |
|-t, --table-list | Spécifie les tables à répliquer. Par exemple : --table-list="table1;table2;table3". Si vous ne spécifiez pas de tables, toutes les tables HBase existantes sont répliquées.|
|-m, --machine | Spécifie le nœud principal sur lequel l’action de script s’exécute. La valeur peut être **hn1** ou **hn0**. La valeur **hn0** étant généralement plus utilisée, nous vous recommandons de choisir **hn1**. Utilisez cette option lorsque vous exécutez le script $0 comme une action de script à partir du portail HDInsight ou d’Azure PowerShell.|
|-ip | Obligatoire lorsque vous activez la réplication entre deux réseaux virtuels. Cet argument agit comme un commutateur pour utiliser les adresses IP statiques des nœuds ZooKeeper à partir de clusters de réplica au lieu des noms de domaine complets. Vous devez préconfigurer les adresses IP statiques avant d’activer la réplication. |
|-cp, -copydata | Active la migration des données existantes sur les tables pour lesquelles la réplication est activée. |
|-rpm, -replicate-phoenix-meta | Active la réplication sur les tables système Phoenix. <br><br>*Utilisez cette option avec précaution.* Nous vous recommandons de recréer des tables Phoenix sur les clusters de réplica avant d’utiliser ce script. |
|-h, --help | Affiche des informations sur l’utilisation. |

La section `print_usage()` du [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) offre une explication détaillée des paramètres.

Une fois le déploiement de l’action de script terminé, vous pouvez utiliser SSH pour vous connecter au cluster HBase de destination, puis vérifier que les données ont été répliquées.

### <a name="replication-scenarios"></a>Scénarios de réplication

La liste suivante présente certains cas d’utilisation générale et la définition de leurs paramètres :

- **Activer la réplication sur toutes les tables entre les deux clusters**. Ce scénario ne nécessite pas la copie ou migration des données existantes dans les tables et n’utilise pas de tables Phoenix. Utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Activer la réplication sur des tables spécifiques**. Pour activer la réplication sur table1, table2 et table3, utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Activer la réplication sur des tables spécifiques et copier les données existantes**. Pour activer la réplication sur table1, table2 et table3, utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Activer la réplication sur toutes les tables et répliquer les métadonnées Phoenix de la source vers la destination**. La réplication des métadonnées Phoenix n’est pas parfaite. Utilisez-la avec précaution. Utilisez les paramètres suivants :

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copier et migrer des données

Il existe deux scripts d’action de script distincts disponibles pour copier ou migrer des données une fois la réplication activée :

- [Script pour les petites tables](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tables dont la taille est de quelques gigaoctets et dont la copie globale est censée durer moins d’une heure)

- [Script pour les tables volumineuses](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tables dont la copie est censée durer plus d’une heure)

Vous pouvez suivre la même procédure que celle décrite dans la section [Activer la réplication](#enable-replication) pour appeler l’action de script. Utilisez les paramètres suivants :

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La section `print_usage()` du [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) offre une description détaillée des paramètres.

### <a name="scenarios"></a>Scénarios

- **Copier des tables spécifiques (test1, test2 et test3) pour toutes les lignes modifiées jusqu’à présent (horodatage actuel)** :

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou :

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copier des tables spécifiques avec une plage horaire spécifiée** :

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Désactiver la réplication

Pour désactiver la réplication, utilisez un autre script d’action de script disponible dans [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). Vous pouvez suivre la même procédure que celle décrite dans la section [Activer la réplication](#enable-replication) pour appeler l’action de script. Utilisez les paramètres suivants :

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

La section `print_usage()` du [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) offre une explication détaillée des paramètres.

### <a name="scenarios"></a>Scénarios

- **Désactiver la réplication sur toutes les tables** :

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  or

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Désactiver la réplication sur les tables spécifiées (table1, table2 et table3)** :

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>étapes suivantes

Ce didacticiel a décrit la configuration de la réplication HBase dans un réseau virtuel ou entre deux réseaux virtuels dans Azure. Pour en savoir plus sur HDInsight et HBase, consultez les articles suivants :

* [Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Linux](./apache-hbase-tutorial-get-started-linux.md)
* [Vue d’ensemble de HDInsight HBase](./apache-hbase-overview.md)
* [Création de clusters HBase dans un réseau virtuel Azure](./apache-hbase-provision-vnet.md)

