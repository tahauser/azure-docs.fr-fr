---
title: "Optimisation des requêtes Hive dans Azure HDInsight | Microsoft Docs"
description: "Apprenez à optimiser vos requêtes pour Hadoop dans HDInsight"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 3577b06bfb23457c17099902a7ac9fb8eb6e3087
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Optimisation des requêtes Hive dans Azure HDInsight

Par défaut, les clusters Hadoop ne sont pas optimisés pour les performances. Cet article présente quelques-unes des méthodes d’optimisation des performances Hive courantes que vous pouvez appliquer à nos requêtes.

## <a name="scale-out-worker-nodes"></a>Montée en charge des nœuds de travail

L’augmentation du nombre de nœuds de travail d’un cluster permet d’exploiter l’exécution de mappeurs et de raccords de réduction en parallèle. Il existe deux manières d’accroître la montée en charge dans HDInsight :

* Au moment de l’approvisionnement, vous pouvez spécifier le nombre de nœuds Worker à l’aide du portail Azure, d’Azure PowerShell ou d’une interface de ligne de commande multiplateforme.  Pour plus d’informations, consultez la rubrique [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). La capture d’écran suivante montre la configuration du nœud Worker sur le portail Azure :
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* Au moment de l’exécution, vous pouvez également monter en charge un cluster sans en recréer un autre :

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Pour plus d’informations sur les différentes machines virtuelles prises en charge par HDInsight, consultez la [tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="enable-tez"></a>Activation de Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) est un moteur d’exécution représentant une alternative au moteur MapReduce :

![tez_1][image-hdi-optimize-hive-tez_1]

Tez est plus rapide pour les raisons suivantes :

* **Il exécute un graphe orienté acyclique (DAG) en tant que tâche unique dans le moteur MapReduce**. Le DAG requiert que chaque ensemble de mappeurs soit suivi par un ensemble de raccords de réduction. Cela provoque la préparation de plusieurs tâches MapReduce pour chaque requête Hive. Tez n’a pas de telles contraintes et peut traiter un graphique orienté acyclique comme une tâche unique, ce qui réduit la surcharge de démarrage de tâche.
* **Il évite les écritures inutiles**. Lorsque plusieurs tâches sont préparées pour la même requête Hive dans le moteur MapReduce, la sortie de chacune d’elles est écrite vers HDFS pour les données intermédiaires. Comme Tez réduit le nombre de tâches de chaque requête Hive, il est possible d’éviter de telles écritures inutiles.
* **Il réduit les délais de démarrage**. Tez peut réduire davantage le délai de démarrage en réduisant le nombre de mappeurs nécessaires au démarrage tout en améliorant l’optimisation tout au long du processus.
* **Il réutilise les conteneurs**. Dès que possible, Tez peut réutiliser les conteneurs pour réduire la latence provoquée par le nombre de conteneurs au démarrage.
* **Il utilise des techniques d’optimisation continue**. Généralement, l’optimisation est effectuée lors de la compilation. Cependant, des informations supplémentaires sur les entrées sont disponibles pour améliorer l’optimisation durant le démarrage. Tez utilise des techniques d’optimisation continue qui permettent d’améliorer le plan lors de la phase de runtime.

Pour plus d’informations sur ces concepts, consultez [Apache TEZ](http://hortonworks.com/hadoop/tez/).

Vous pouvez activer n’importe quelle requête Hive pour Tez en faisant précéder la requête du paramètre ci-dessous :

    set hive.execution.engine=tez;

Tez est activé par défaut pour les clusters HDInsight basés sur Linux.


## <a name="hive-partitioning"></a>Partitionnement Hive

Les opérations d’E/S constituent le principal goulot d’étranglement des performances pour l’exécution de requêtes Hive. Il est possible d’améliorer les performances en réduisant la quantité de données à lire. Par défaut, les requêtes Hive analysent l’ensemble des tables Hive. Cela est très utile pour les requêtes telles que les analyses de tables. Cependant, ce comportement crée une surcharge inutile pour les requêtes analysant seulement une petite quantité de données (par exemple, les requêtes avec filtrage). Le partitionnement Hive permet aux requêtes Hive d’accéder uniquement à la quantité de données nécessaire dans les tables Hive.

Le partitionnement Hive est implémenté en réorganisant les données brutes en nouveaux répertoires où chaque partition a son propre répertoire, comme défini par l’utilisateur. Le schéma suivant illustre le partitionnement d’une table Hive selon la colonne *Année*. Un nouveau répertoire est créé pour chaque année.

![partitionnement][image-hdi-optimize-hive-partitioning_1]

Considérations relatives au partitionnement :

* **Évitez les sous-partitionnements** : les partitionnements appliqués à des colonnes contenant uniquement quelques valeurs peuvent entraîner quelques partitions. Par exemple, un partitionnement de genre crée uniquement deux partitions (masculin et féminin), ce qui réduit la latence de moitié seulement.
* **Évitez les sur-partitionnements** : l’autre extrême, le partitionnement appliqué à une colonne avec une valeur unique (par exemple, userid) entraîne de nombreuses partitions. Le sur-partitionnement communique un stress important au cluster namenode, car ce dernier doit gérer de grandes quantités de répertoires.
* **Évitez le décalage de données** : choisissez votre clé de partitionnement avec soin, pour que toutes les partitions soient de taille égale. Par exemple, le partitionnement sur *Région* peut entraîner un nombre d’enregistrements sous Île-de-France 30 fois supérieur à celui sous Franche-Comté, en raison de la différence de population.

Pour créer une table de partition, utilisez la clause *Partitioned By* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Lorsque la table partitionnée est créée, vous pouvez créer un partitionnement statique ou dynamique.

* **Partitionnement statique** signifie que vous avez déjà partagé des données dans des répertoires appropriés et que vous pouvez demander des partitions Hive manuellement en fonction de l’emplacement du répertoire. L’extrait de code suivant est un exemple.
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **Partitionnement dynamique** signifie que vous voulez que Hive crée automatiquement des partitions pour vous. Étant donné que nous avons déjà créé la table de partitionnement à partir de la table intermédiaire, il nous suffit d’insérer des données dans la table partitionnée :
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Pour plus d’informations, consultez [Tables partitionnées](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>Utilisation du format ORCFile
Hive prend en charge différents formats de fichier. Par exemple : 

* **Texte**: il s’agit du format de fichier par défaut, qui fonctionne avec la plupart des scénarios
* **Avro**: fonctionne correctement avec les scénarios d’interopérabilité
* **ORC/Parquet**: adapté pour les performances

Le format ORC (Optimized Row Columnar) est un moyen très efficace pour stocker des données Hive. Par rapport aux autres formats, ORC présente les avantages suivants :

* prise en charge des types complexes, notamment les types DateTime, ainsi que les types semi-structurés ;
* jusqu’à 70 % de compression ;
* création d’index toutes les 10 000 lignes, ce qui permet d’ignorer des lignes ;
* baisse significative de l’exécution du démarrage.

Pour activer le format ORC, vous devez commencer par créer une table avec la clause *Stored as ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Ensuite, vous devez insérer des données dans la table ORC à partir de la table de mise en lots. Par exemple : 

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Vous pouvez en savoir plus sur le format ORC [ici](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorisation

La vectorisation permet à Hive de traiter un lot de 1024 lignes simultanément, au lieu d’une ligne à la fois. Cela signifie que les opérations simples sont effectuées plus rapidement, car elles requièrent moins d’exécution de code interne.

Pour activer la vectorisation, faites précéder vos requêtes Hive par le paramètre suivant :

    set hive.vectorized.execution.enabled = true;

Pour plus d’informations, consultez la page [Exécution de requêtes vectorisées](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Autres méthodes d’optimisation
Vous pouvez envisager plusieurs autres méthodes d’optimisation, par exemple :

* **Création de compartiments Hive** : cette technique permet de mettre en cluster ou de segmenter des jeux de données volumineux pour optimiser les performances des requêtes.
* **Optimisation des jointures** : une optimisation de la planification de l’exécution des requêtes Hive pour améliorer l’efficacité des jointures et réduire le besoin d’indicateurs utilisateur. Pour plus d’informations, consultez la page [Optimisation des jointures](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Augmentez les raccords de réduction**.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris plusieurs méthodes d’optimisation courantes des requêtes. Pour en savoir plus, consultez les articles suivants :

* [Utilisation d’Apache Hive dans HDInsight](hadoop/hdinsight-use-hive.md)
* [Analyse des données sur les retards de vol avec Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)
* [Analyse des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md)
* [Analyse des données de capteur à l’aide de la console de requête Hive sur Hadoop dans HDInsight](hadoop/apache-hive-analyze-sensor-data.md)
* [Utilisation de Hive avec HDInsight pour analyser les journaux de site web](hadoop/apache-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
