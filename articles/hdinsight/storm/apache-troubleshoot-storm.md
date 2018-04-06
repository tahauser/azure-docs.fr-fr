---
title: Résolution de problèmes Storm à l’aide d’Azure HDInsight | Microsoft Docs
description: Obtenez les réponses aux questions courantes sur l’utilisation d’Apache Storm avec Azure HDInsight.
keywords: Azure HDInsight, Storm, FAQ, guide de dépannage, problèmes courants
services: Azure HDInsight
documentationcenter: na
author: raviperi
manager: ''
editor: ''
ms.assetid: 74E51183-3EF4-4C67-AA60-6E12FAC999B5
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: raviperi
ms.openlocfilehash: 6ee408b32650fe0f0e0b66294896756732a2ca5b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-storm-by-using-azure-hdinsight"></a>Résolution de problèmes Storm à l’aide d’Azure HDInsight

Découvrez les principaux problèmes rencontrés lors de l’utilisation de charges utiles Apache Storm dans Apache Ambari, et leur résolution.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Comment accéder à l’interface utilisateur Storm sur un cluster ?
Deux options vous permettent d’accéder à l’interface utilisateur Storm à partir d’un navigateur :

### <a name="ambari-ui"></a>Interface utilisateur Ambari
1. Accédez au tableau de bord Ambari.
2. Dans la liste des services, sélectionnez **Storm**.
3. Dans le menu **Quick Links** (Liens rapides), sélectionnez **Storm UI**.

### <a name="direct-link"></a>Lien direct
Vous pouvez accéder à l’interface utilisateur de Storm à l’URL suivante :

https://\<nom DNS du cluster\>/stormui

Exemple :

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Comment transférer les informations de point de contrôle du spout de concentrateurs d’événements Storm depuis une topologie vers une autre ?

Lorsque vous développez des topologies qui lisent à partir des hubs d’événements Azure en utilisant le fichier .jar du spout Storm Eventhub, vous devez déployer une topologie portant le même nom sur un nouveau cluster. Toutefois, vous devez conserver les données de point de contrôle validées dans Apache ZooKeeper sur l’ancien cluster.

### <a name="where-checkpoint-data-is-stored"></a>Où sont stockées les données de point de contrôle ?
Les données de point de contrôle des décalages sont enregistrées par le spout EventHub dans Zookeeper sous deux chemins d’accès racine :
- Les points de contrôle de spout non transactionnels sont stockés dans /eventhubspout.
- Les données de point de contrôle de spout transactionnel sont stockées dans /transactional.

### <a name="how-to-restore"></a>Comment effectuer une restauration ?
Pour obtenir les scripts et les bibliothèques utilisés pour exporter des données depuis ZooKeeper et les réimporter sous un nom différent, consultez les [exemples HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Le dossier lib renferme des fichiers .jar contenant les instructions d’implémentation de l’opération d’exportation/importation. Le dossier bash comporte un exemple de script montrant comment exporter des données depuis le serveur ZooKeeper de l’ancien cluster et les réimporter dans le serveur ZooKeeper du nouveau cluster.

Exécutez le script [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) à partir des nœuds ZooKeeper pour exporter et réimporter des données. Indiquez la version appropriée de Hortonworks Data Platform (HDP) dans le script. (Nous travaillons sur la création de scripts génériques dans HDInsight. Les scripts génériques peuvent s’exécuter à partir de n’importe quel nœud du cluster sans modifications de l’utilisateur.)

La commande d’exportation écrit les métadonnées sur un chemin d’accès Apache Hadoop Distributed File System (HDFS), dans un magasin de stockage Blob Azure ou Azure Data Lake Store) à un emplacement que vous définissez.

### <a name="examples"></a>Exemples

#### <a name="export-offset-metadata"></a>Exporter les métadonnées de décalage
1. À l’aide de SSH, accédez à l’ancien cluster ZooKeeper à partir duquel les données de décalage de point de contrôle doivent être exportées.
2. Exécutez la commande suivante (après avoir mis à jour la chaîne de la version HDP) pour exporter les données de décalage ZooKeeper vers le chemin de stockage HDFS /stormmetadta/zkdata :

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importer les métadonnées de décalage
1. À l’aide de SSH, accédez à l’ancien cluster ZooKeeper à partir duquel les données de décalage de point de contrôle doivent être importées.
2. Exécutez la commande suivante (après avoir mis à jour la chaîne de la version HDP) pour importer les données de décalage ZooKeeper à partir du chemin de stockage HDFS /stormmetadata/zkdata vers le serveur ZooKeeper dans le cluster cible :

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Supprimer les métadonnées de décalage afin que les topologies puissent commencer à traiter les données à partir du début ou d’un horodatage défini par l’utilisateur
1. À l’aide de SSH, accédez à l’ancien cluster ZooKeeper à partir duquel les données de décalage de point de contrôle doivent être supprimées.
2. Exécutez la commande suivante (après avoir mis à jour la chaîne de la version HDP) pour supprimer les données de décalage ZooKeeper du cluster actuel :

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Comment localiser les fichiers binaires de Storm sur un cluster ?
Les fichiers binaires Storm de la pile HDP actuelle se trouvent à l’emplacement /usr/hdp/current/storm-client. L’emplacement est le même pour les nœuds principaux et les nœuds de travail.
 
Il peut y avoir plusieurs fichiers binaires pour des versions spécifiques de HDP à l’emplacement /usr/hdp (par exemple, /usr/hdp/2.5.0.1233/storm). Un lien symbolique avec le dossier /usr/hdp/current/storm-client est créé vers la version la plus récente exécutée sur le cluster.

Pour plus d’informations, consultez les pages [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) et [Storm](http://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Comment déterminer la topologie de déploiement d’un cluster Storm ?
Commencez par identifier tous les composants installés avec HDInsight Storm. Un cluster Storm se compose de quatre catégories de nœuds :

* Nœuds de passerelle
* Nœuds principaux
* Nœuds ZooKeeper
* Nœuds de travail
 
### <a name="gateway-nodes"></a>Nœuds de passerelle
Un nœud de passerelle est un service de passerelle et de proxy inverse qui permet un accès public à un service de gestion Ambari actif. Il gère également le choix du leader Ambari.
 
### <a name="head-nodes"></a>Nœuds principaux
Les nœuds principaux Storm exécutent les services suivants :
* Nimbus
* Ambari Server
* Ambari Metrics Server
* Ambari Metrics Collector
 
### <a name="zookeeper-nodes"></a>Nœuds ZooKeeper
HDInsight est fourni avec un quorum de trois nœuds ZooKeeper. Sa taille est fixe et n’est pas reconfigurable.
 
Les services Storm du cluster sont configurés pour utiliser le quorum ZooKeeper automatiquement.
 
### <a name="worker-nodes"></a>Nœuds de travail
Les nœuds de travail Storm exécutent les services suivants :
* Supervisor
* Machines virtuelles Java (JVM) de travail, pour l’exécution des topologies
* Ambari Agent
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Comment localiser les fichiers binaires du spout de concentrateurs d’événements Storm pour le développement ?
 
Pour plus d’informations sur l’utilisation de fichiers .jar de spout EventHub Storm avec votre topologie, consultez les ressources suivantes.
 
### <a name="java-based-topology"></a>Topologie basée sur Java
[Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>Topologie basée sur C# (Mono sur les clusters Storm HDInsight 3.4+ Linux)
[Traiter des événements issus d’Azure Event Hubs avec Storm sur HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Fichiers binaires de spout EventHub Storm les plus récents pour les clusters Storm HDInsight 3.5+ Linux
Pour savoir comment utiliser le spout EventHub Storm le plus récent compatible avec les clusters Storm HDInsight 3.5+ Linux, consultez le [fichier readme](https://github.com/hdinsight/mvn-repo/blob/master/README.md) du référentiel mvn-repo.
 
### <a name="source-code-examples"></a>Exemples de code source
Consultez ces [exemples](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) pour savoir comment lire et écrire à partir d’Azure EventHub à l’aide d’une topologie Apache Storm (écrite en Java) sur un cluster Azure HDInsight.
 
## <a name="how-do-i-locate-storm-log4j-configuration-files-on-clusters"></a>Comment localiser les fichiers de configuration Storm Log4J sur les clusters ?
 
Pour identifier les fichiers de configuration Apache Log4J des services Storm.
 
### <a name="on-head-nodes"></a>Sur les nœuds principaux
Le fichier de configuration Log4J du service Nimbus est lu à partir du chemin /usr/hdp/\<version HDP\>/storm/log4j2/cluster.xml.
 
### <a name="on-worker-nodes"></a>Sur les nœuds de travail
Le fichier de configuration Log4J du service Supervisor est lu à partir du chemin /usr/hdp/\<version HDP\>/storm/log4j2/cluster.xml.
 
Le fichier de configuration Log4J du Worker est lu à partir du chemin /usr/hdp/\<version HDP\>/storm/log4j2/cluster.xml.
 
Par exemple : /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Voir aussi
[Résoudre des problèmes à l’aide d’Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
