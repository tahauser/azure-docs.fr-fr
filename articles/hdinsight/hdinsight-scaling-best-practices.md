---
title: "Mettre à l’échelle les tailles de cluster - Azure HDInsight | Microsoft Docs"
description: "Mettez à l’échelle un cluster HDInsight dans votre charge de travail."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 7e9ee660c07d6265e55e94cf79ed13334fcb3d16
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="scale-hdinsight-clusters"></a>Mettre à l’échelle les clusters HDInsight

HDInsight fournit l’élasticité en vous offrant la possibilité de monter ou de descendre en puissance le nombre de nœuds de travail dans vos clusters. Cela vous permet de réduire un cluster après certaines heures ou les week-ends, et de le développer pendant les pics d’activité.

Par exemple, si vous effectuez un traitement par lots une fois par jour ou une fois par mois, le cluster HDInsight peut être monté en puissance quelques minutes avant cet événement planifié, et il y aura donc suffisamment de mémoire et de puissance de calcul. Vous pouvez automatiser la mise à l’échelle avec l’applet de commande PowerShell [`Set–AzureRmHDInsightClusterSize`](hdinsight-administer-use-powershell.md#scale-clusters).  Plus tard, une fois que le traitement a été effectué et que l’utilisation baisse à nouveau, vous pouvez descendre en puissance le cluster HDInsight afin de réduire le nombre de nœuds de travail.

* Pour mettre à l’échelle votre cluster via [PowerShell](hdinsight-administer-use-powershell.md) :

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Pour mettre l’échelle votre cluster via l[’interface CLI Azure](hdinsight-administer-use-command-line.md) :

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Pour mettre à l’échelle votre cluster via le [portail Azure](https://portal.azure.com), ouvrez le volet de votre cluster HDInsight, sélectionnez **Mettre à l’échelle le cluster** dans le menu de gauche, puis dans le volet Mettre à l’échelle le cluster, entrez le nombre de nœuds de travail, puis sélectionnez Enregistrer.

    ![Mettre à l’échelle le cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Grâce à ces méthodes, vous pouvez monter ou descendre en puissance votre cluster HDInsight en quelques minutes.

## <a name="scaling-impacts-on-running-jobs"></a>Impact de la mise à l’échelle sur les travaux en cours d’exécution

Lorsque vous **ajoutez** des nœuds à votre cluster HDInsight en cours d’exécution, tous les travaux en attente ou en cours d’exécution ne seront pas affectés. En outre, de nouveaux travaux peuvent être soumis en toute sécurité pendant que le processus de mise à l’échelle est en cours d’exécution. Si les opérations de mise à l’échelle échouent pour une raison quelconque, l’échec est géré en douceur, laissant le cluster dans un état fonctionnel.

Mais si vous descendez en puissance votre cluster en **supprimant** des nœuds, tous les travaux en attente ou en cours d’exécution échouent à la fin de l’opération de mise à l’échelle. Cet échec est dû au redémarrage des services au cours du processus.

Pour résoudre ce problème, vous pouvez attendre la fin des travaux avant de descendre en puissance votre cluster, arrêter manuellement les travaux, ou renvoyer ces travaux une fois l’opération de mise à l’échelle terminée.

Pour afficher une liste des travaux en attente ou en cours d’exécution, vous pouvez utiliser l’interface utilisateur YARN ResourceManager en procédant comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Parcourir**, **Clusters HDInsight**, puis choisissez votre cluster.
3. Dans le volet de votre cluster HDInsight, sélectionnez **Tableau de bord** dans le menu supérieur pour ouvrir l’interface utilisateur Ambari. Entrez les informations d'identification de votre cluster.
4. Cliquez sur **YARN** dans la liste des services du menu à gauche. Dans la page YARN, sélectionnez **Quick Links** (Liens rapides), placez le curseur sur le nœud principal actif, puis cliquez sur **ResourceManager UI** (Interface utilisateur ResourceManager).

    ![Interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Vous pouvez accéder directement à l’interface utilisateur ResourceManager avec `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Une liste de travaux avec leur état actuel apparaît. Dans la capture d’écran, un travail est en cours d’exécution :

![Applications de l’interface utilisateur ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Pour arrêter manuellement cette application en cours d’exécution, exécutez la commande suivante à partir de l’interpréteur de commandes SSH :

```bash
yarn application -kill <application_id>
```

Par exemple : 

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Rééquilibrer un cluster HBase

Les serveurs de région sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Pour équilibrer manuellement les serveurs de région, procédez comme suit :

1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Lancez l’interpréteur de commandes HBase :

        hbase shell

3. Utilisez la commande suivante pour équilibrer manuellement les serveurs de région :

        balancer

## <a name="scale-down-implications"></a>Implications en matière de mise à l’échelle

Comme mentionné précédemment, tous les travaux en attente ou en cours d’exécution se terminent à la fin d’une opération de descente en puissance. Toutefois, il existe d’autres implications potentielles à cette descente en puissance.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Le nœud de nom HDInsight reste en mode sans échec après la descente en puissance

![Mettre à l’échelle le cluster](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Si vous réduisez votre cluster jusqu'à la valeur minimale d’un nœud de travail, comme indiqué dans l’image précédente, HDFS peut se bloquer en mode sans échec si les nœuds de travail sont redémarrés en raison d’une mise à jour corrective ou immédiatement après l’opération de mise à l’échelle.

La principale cause de cette situation vient du fait que Hive utilise quelques fichiers `scratchdir` et que, par défaut, il attend trois réplicas de chaque bloc, mais qu’un seul réplica est possible si vous descendez en puissance jusqu’à au moins un nœud de travail. Par conséquent, les fichiers dans `scratchdir` deviennent *sous-répliqués*. HDFS peut alors rester en mode sans échec lorsque les services sont redémarrés après la mise à l’échelle.

En cas de tentative de descente en puissance, HDInsight s’appuie sur les interfaces de gestion Ambari pour désactiver les nœuds de travail supplémentaires inutiles, ce qui réplique les blocs HDFS vers d’autres nœuds de travail en ligne, puis descend en puissance le cluster en toute sécurité. HDFS bascule en mode sans échec lors de la fenêtre de maintenance et il est censé en sortir une fois la mise à l’échelle terminée. C’est à ce stade que HDFS peut se retrouver bloqué en mode sans échec.

HDFS est configuré avec un paramètre `dfs.replication` défini sur 3. Par conséquent, les blocs des fichiers de travail sont sous-répliqués chaque fois qu’il y a moins de trois nœuds de travail en ligne, car ils ne représentent pas les trois copies de chaque bloc de fichier disponible.

Vous pouvez exécuter une commande afin de sortir HDFS du mode sans échec. Par exemple, si vous savez que la seule raison pour laquelle le mode sécurisé est activé est que les fichiers temporaires sont sous-répliqués, alors vous pouvez en toute sécurité quitter le mode sans échec. En effet, les fichiers sous-répliqués sont des fichiers de travail temporaires Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Après avoir quitté le mode sans échec, vous pouvez manuellement supprimer les fichiers temporaires, ou attendre que Hive les nettoie automatiquement.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exemples d’erreurs lorsque le mode sécurisé est activé

* H070 Unable to open Hive session. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Name node is in safe mode**. The reported blocks 75 needs additional 12 blocks to reach the threshold 0.9900 of total blocks 87. The number of live datanodes 10 has reached the minimum number 0. Safe mode will be turned off automatically once the thresholds have been reached.

* H100 Unable to submit statement show databases: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: **Connection refused**

* H020 Could not establish connection to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: Could not create http connection to http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed: Connection refused: org.apache.thrift.transport.TTransportException: Could not create http connection to http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed: **Connection refused**

* From the Hive logs: WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Error starting HiveServer2 on attempt 21, will retry in 60 seconds java.lang.RuntimeException: Error applying authorization policy on hive configuration: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Name node is in safe mode**.
    The reported blocks 0 needs additional 9 blocks to reach the threshold 0.9900 of total blocks 9.
    The number of live datanodes 10 has reached the minimum number 0. **Safe mode will be turned off automatically once the thresholds have been reached**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Vous pouvez examiner les journaux du nœud de nom dans le dossier `/var/log/hadoop/hdfs/`, aux alentours du moment où le cluster a été mis à l’échelle, pour voir quand il est entré en mode sans échec. Les fichiers journaux sont nommés `Hadoop-hdfs-namenode-hn0-clustername.*`.

La cause principale des erreurs précédentes vient du fait que Hive dépend de fichiers temporaires dans HDFS lors de l’exécution des requêtes. Quand HDFS bascule en mode sans échec, Hive ne peut pas exécuter de requêtes car il ne peut pas écrire dans HDFS. Les fichiers temporaires dans HDFS sont situés dans le lecteur local monté sur les machines virtuelles de nœud de travail individuelles, et répliqué entre les autres nœuds de travail en au moins trois réplicas.

Le paramètre `hive.exec.scratchdir` dans Hive est configuré dans `/etc/hive/conf/hive-site.xml` :

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Afficher l’intégrité et l’état de votre système de fichiers HDFS

Vous pouvez afficher un rapport d’état de chaque nœud de nom pour voir si des nœuds sont en mode sans échec. Pour afficher le rapport, connectez-vous avec SSH à chaque nœud principal puis exécutez la commande suivante :

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Safe mode off](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> Le commutateur `-D` est nécessaire car le système de fichiers par défaut dans HDInsight est Stockage Azure ou Azure Data Lake Store. `-D` spécifie que les commandes s’exécutent sur le système de fichiers HDFS local.

Ensuite, vous pouvez afficher un rapport précisant les détails de l’état HDFS :

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Cette commande entraîne la situation suivante sur un cluster sain où tous les blocs sont répliqués au degré attendu :

![Safe mode off](./media/hdinsight-scaling-best-practices/report.png)

HDFS prend en charge la commande `fsck` pour rechercher des incohérences avec divers fichiers, notamment l’absence de blocs dans un fichier ou des blocs sous-répliqués. Pour exécuter la commande `fsck` par rapport aux fichiers `scratchdir` (disque de travail temporaire) :

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Lors de l’exécution sur un système de fichiers HDFS sain sans blocs sous-répliqués, le résultat ressemble à ce qui suit :

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

En revanche, lorsque la commande `fsck` est exécutée sur un système de fichiers HDFS avec des blocs sous-répliqués, le résultat ressemble à ce qui suit :

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Vous pouvez également afficher l’état HDFS dans l’interface utilisateur Ambari en sélectionnant le service **HDFS** sur la gauche, ou avec `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![État HDFS dans Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Vous pouvez également voir une ou plusieurs erreurs critiques dans les blocs NameNode actifs ou en veille. Pour afficher l’intégrité de blocs NameNode, sélectionnez le lien NameNode en regard de l’alerte.

![Intégrité des blocs NameNode](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Pour nettoyer les fichiers de travail, ce qui supprime les erreurs de réplication de blocs, connectez-vous avec SSH à chaque nœud principal puis exécutez la commande suivante :

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Cette commande peut interrompre Hive si certaines tâches sont en cours d’exécution.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Guide pratique pour empêcher le blocage de HDInsight en mode sans échec en raison de blocs sous-répliqués

Il existe plusieurs façons d’empêcher HDInsight de rester en mode sans échec :

* Arrêter tous les travaux Hive avant la descente en puissance de HDInsight. Vous pouvez également planifier le processus de descente en puissance pour d’éviter tout conflit avec les travaux Hive en cours d’exécution.
* Nettoyer manuellement les fichiers du répertoire de travail temporaire `tmp` Hive dans HDFS avant la descente en puissance.
* Effectuer uniquement la descente en puissance de HDInsight à au moins trois nœuds de travail. Éviter de descendre à un nœud de travail.
* Exécuter la commande pour quitter le mode sans échec, si nécessaire.

Les sections suivantes décrivent ces options.

#### <a name="stop-all-hive-jobs"></a>Arrêter tous les travaux Hive

Arrêtez tous les travaux Hive avant la descente en puissance à un nœud de travail. Si votre charge de travail est planifiée, effectuez votre descente en puissance une fois travail Hive terminé.

Cela permet de réduire le nombre de fichiers de travail dans le dossier temporaire (le cas échéant).

#### <a name="manually-clean-up-hives-scratch-files"></a>Nettoyer manuellement les fichiers de travail Hive

Si Hive a laissé des fichiers temporaires, vous pouvez nettoyer manuellement ces fichiers avant la descente en puissance pour éviter le mode sans échec.

1. Arrêtez les services Hive et vérifiez que toutes les requêtes et tous les travaux sont terminés.

2. Répertoriez le contenu du répertoire `hdfs://mycluster/tmp/hive/` pour voir s’il contient des fichiers :

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    S’il existe des fichiers, le résultat ressemble à ce qui suit :

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Si vous savez que Hive a fini d’utiliser ces fichiers, vous pouvez les supprimer. Assurez-vous que Hive ne contient aucune requête en cours d’exécution en consultant la page de l’interface utilisateur Yarn ResourceManager.

    Exemple de ligne de commande pour supprimer des fichiers de HDFS :

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Descente en puissance de HDInsight à trois nœuds de travail

Si vous vous retrouvez souvent en mode sans échec et que les étapes précédentes n’ont pas résolu le problème, vous pouvez éviter cette situation en descendant en puissance à trois nœuds de travail uniquement. En raison de contraintes liées au coût, cette méthode n’est peut-être pas optimale par rapport à la descente en puissance à un nœud. Toutefois, avec un seul nœud de travail, HDFS ne peut pas garantir la disponibilité de trois réplicas des données au cluster.

#### <a name="run-the-command-to-leave-safe-mode"></a>Exécuter la commande pour quitter le mode sans échec

La dernière option consiste à rechercher les rares cas où HDFS passe en mode sans échec, puis à exécuter la commande pour quitter le mode sans échec. Une fois que vous avez déterminé que HDFS entre en mode sans échec lorsque les fichiers Hive sont sous-répliqués, exécutez la commande suivante pour quitter le mode sans échec :

* HDInsight sur Linux :

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight sur Windows :

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Étapes suivantes

* [Présentation d'Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Mise à l’échelle des clusters](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)