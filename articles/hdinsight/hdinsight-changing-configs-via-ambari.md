---
title: Optimiser les configurations de cluster avec Ambari - Azure HDInsight | Microsoft Docs
description: "Utilisez l’interface utilisateur Web d’Ambari pour configurer et optimiser les clusters HDInsight."
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: ashish
ms.openlocfilehash: 74c1b3298cd7b6ffd5b4a60e2fa78ed733232f92
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-ambari-to-optimize-hdinsight-cluster-configurations"></a>Utiliser Ambari pour optimiser les configurations de cluster HDInsight

HDInsight fournit des clusters Apache Hadoop pour les applications de traitement de données à grande échelle. La gestion, la surveillance et l’optimisation de ces clusters complexes à nœuds multiples peuvent être difficiles. [Apache Ambari](http://ambari.apache.org/) est une interface Web qui permet de gérer et de surveiller les clusters Linux HDInsight.  Pour les clusters Windows, utilisez l’[API REST](hdinsight-hadoop-manage-ambari-rest-api.md) d’Ambari.

Pour une présentation de l’interface utilisateur web d’Ambari, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)

Connectez-vous à Ambari à l’adresse `https://CLUSTERNAME.azurehdidnsight.net` avec les informations d’identification du cluster. L’écran initial affiche un tableau de bord de présentation.

![Tableau de bord Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

L’interface utilisateur Web d’Ambari permet de gérer les hôtes, les services, les alertes, les configurations et les vues. Ambari ne peut pas être utilisé pour créer un cluster HDInsight, mettre à niveau des services, gérer les versions et les piles, mettre hors service ou remettre en service des hôtes, ou ajouter des services au cluster.

## <a name="manage-your-clusters-configuration"></a>Gérer la configuration de votre cluster

Les paramètres de configuration permettent de paramétrer un service particulier. Pour modifier les paramètres de configuration d’un service, sélectionnez le service dans la barre latérale **Services (Services)** (à gauche), puis accédez à l’onglet **Configs (Configurations)** dans la page de détails du service.

![Barre latérale Services (Services)](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modifier la taille du tas Java NameNode

La taille du tas Java NameNode dépend de nombreux facteurs tels que la charge du cluster, le nombre de fichiers et le nombre de blocs. La taille par défaut de 1 Go fonctionne bien pour la plupart des clusters, mais certaines charges de travail peuvent nécessiter plus ou moins de mémoire. 

Pour modifier la taille du tas Java NameNode :

1. Sélectionnez **HDFS (HDFS)** dans la barre latérale Services (Services) et accédez à l’onglet **Configs (Configurations)**.

    ![Configuration HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

2. Recherchez le paramètre **NameNode Java heap size (Taille du tas Java NameNode)**. Vous pouvez également utiliser la zone de texte **Filter (Filtrer)** pour saisir et trouver un paramètre. Sélectionnez l’icône de **crayon** en regard du nom de paramètre.

    ![Taille du tas Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

3. Tapez la nouvelle valeur dans la zone de texte, puis appuyez sur **Entrée** pour enregistrer la modification.

    ![Modifier la taille du tas Java NameNode](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

4. La taille du tas Java NameNode passe de 1 Go à 2 Go.

    ![Taille du tas Java NodeName modifiée](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

5. Enregistrez vos modifications en cliquant sur le bouton **Save (Enregistrer)** vert en haut de l’écran de configuration.

    ![Enregistrer les modifications](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="hive-optimization"></a>Optimisation de Hive

Les sections suivantes décrivent les options de configuration qui permettent d’optimiser les performances globales de Hive.

1. Pour modifier les paramètres de configuration de Hive, sélectionnez **Hive (Hive)** dans la barre latérale Services (Services).
2. Accédez à l’onglet **Configs (Configurations)**.

### <a name="set-the-hive-execution-engine"></a>Définir le moteur d’exécution de Hive

Hive fournit deux moteurs d’exécution : MapReduce et Tez. Tez est plus rapide que MapReduce. Les clusters Linux HDInsight utilisent Tez comme moteur d’exécution par défaut. Pour changer de moteur d’exécution :

1. Dans l’onglet **Configs (Configuration)** de Hive, tapez **execution engine** dans la zone de filtrage.

    ![Rechercher le moteur d’exécution](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

2. La valeur par défaut de la propriété **Optimization (Optimisation)** est **Tez**.

    ![Optimisation - Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Paramétrer les mappeurs

Hadoop tente de fractionner (*mapper*) un fichier en plusieurs fichiers et de traiter ceux-ci en parallèle. Le nombre de mappeurs dépend du nombre de fractionnements. Les deux paramètres de configuration suivants déterminent le nombre de fractionnements pour le moteur d’exécution Tez :

* `tez.grouping.min-size` : limite inférieure de la taille d’un fractionnement groupé, avec une valeur par défaut de 16 Mo (16 777 216 octets).
* `tez.grouping.max-size` : limite supérieure de la taille d’un fractionnement groupé, avec une valeur par défaut de 1 Go (1 073 741 824 octets).

En règle générale, diminuez ces deux paramètres pour améliorer la latence ou augmentez-les pour accroître le débit.

Par exemple, pour définir quatre mappeurs pour 128 Mo de données, réglez les deux paramètres sur 32 Mo (33 554 432 octets).

1. Pour modifier les limites, accédez à l’onglet **Configs (Configurations)** du service Tez. Développez le panneau **General (Général)** et recherchez les paramètres `tez.grouping.max-size` et `tez.grouping.min-size`.

2. Réglez ces deux paramètres sur **33 554 432** octets (32 Mo).

    ![Taille des regroupements dans tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Ces modifications affectent tous les travaux Tez sur le serveur. Pour obtenir un résultat optimal, choisissez les valeurs appropriées aux paramètres.

### <a name="tune-reducers"></a>Paramétrer les réducteurs

ORC et Snappy offrent tous les deux des performances élevées. Toutefois, Hive peut avoir trop peu de réducteurs par défaut et ainsi générer des goulots d’étranglement.

Par exemple, admettons que vous ayez 50 Go de données en entrée. Ces données au format ORC avec la compression Snappy occupent 1 Go. Hive évalue le nombre de réducteurs nécessaires selon la formule suivante : (nombre d’octets entrés dans les mappeurs/`hive.exec.reducers.bytes.per.reducer`).

Avec les paramètres par défaut, le résultat pour cet exemple est de 4 réducteurs.

Le paramètre `hive.exec.reducers.bytes.per.reducer` spécifie le nombre d’octets traités par réducteur. La valeur par défaut est 64 Mo. En abaissant cette valeur, vous renforcez le parallélisme, ce qui peut améliorer les performances. Mais une valeur trop faible peut également générer trop de réducteurs et donc nuire aux performances. Ce paramètre est fonction de vos besoins en données, des paramètres de compression et d’autres facteurs environnementaux.

1. Pour modifier ce paramètre, accédez à l’onglet **Configs (Configurations)** de Hive et recherchez le paramètre **Data per Reducer (Données par réducteur)** sur la page Settings (Paramètres).

    ![Données par réducteur](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
2. Sélectionnez **Edit (Modifier)** pour remplacer la valeur par 128 Mo (134 217 728 octets), puis appuyez sur **Entrée** pour enregistrer.

    ![Données par réducteur après modification](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Avec une taille d’entrée de 1 024 Mo et 128 Mo de données par réducteur, on obtient 8 réducteurs (1024/128).

3. Une valeur incorrecte du paramètre **Data per Reducer (Données par réducteur)** peut produire un grand nombre de réducteurs et nuire aux performances des requêtes. Pour limiter le nombre maximal de réducteurs, réglez `hive.exec.reducers.max` sur une valeur appropriée. La valeur par défaut est 1009.

### <a name="enable-parallel-execution"></a>Activer l’exécution en parallèle

Une requête Hive s’exécute en une ou plusieurs étapes. Si des étapes indépendantes peuvent être exécutées en parallèle, les performances de la requête s’en trouvent accrues.

1.  Pour activer l’exécution de requête en parallèle, accédez à l’onglet **Configs (Configuration)** de Hive et recherchez la propriété `hive.exec.parallel`. La valeur par défaut est false. Remplacez-la par true, puis appuyez sur **Entrée** pour enregistrer la valeur.
 
2.  Pour limiter le nombre de travaux à exécuter en parallèle, modifiez la propriété `hive.exec.parallel.thread.number`. La valeur par défaut est 8.

    ![Exécution en parallèle dans Hive](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Activer la vectorisation

Hive traite les données ligne par ligne. La vectorisation permet à Hive de traiter les données par blocs de 1 024 lignes plutôt qu’une ligne à la fois. La vectorisation n’est applicable qu’aux fichiers de format ORC.

1. Pour activer l’exécution vectorisée des requêtes, accédez à l’onglet **Configs (Configuration)** de Hive et recherchez le paramètre `hive.vectorized.execution.enabled`. La valeur par défaut est true pour Hive 0.13.0 ou version ultérieure.
 
2. Pour activer l’exécution vectorisée du volet réduit de la requête, réglez le paramètre `hive.vectorized.execution.reduce.enabled` sur true. La valeur par défaut est false.

    ![Exécution vectorisée dans Hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Activer l’optimisation des coûts (CBO)

Par défaut, Hive suit un ensemble de règles pour trouver le plan d’exécution optimal d’une requête. L’optimisation des coûts (CBO) évalue plusieurs plans d’exécution, attribue un coût à chaque plan, puis détermine le plan le moins coûteux pour exécuter une requête.

Pour activer CBO, accédez à l’onglet **Configs (Configurations)** de Hive, recherchez `parameter hive.cbo.enable`, puis réglez le bouton bascule sur **On (Activé)**.

![Configuration de CBO](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Les paramètres de configuration supplémentaires suivants augmentent les performances de traitement des requêtes dans Hive lorsque CBO est activé :

* `hive.compute.query.using.stats`

    Lorsque ce paramètre est réglé sur true, Hive utilise les statistiques stockées dans son metastore pour répondre aux requêtes simples, comme `count(*)`.

    ![Statistiques CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Des statistiques de colonne sont créées lorsque CBO est activé. Hive utilise ces statistiques de colonne, qui sont stockées dans son metastore, pour optimiser les requêtes. L’extraction des statistiques pour chaque colonne prend plus de temps lorsque le nombre de colonnes est élevé. Lorsque ce paramètre a la valeur false, il désactive l’extraction des statistiques de colonnes à partir du metastore.

    ![Les statistiques de Hive déterminent les statistiques de colonne](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Les statistiques de partition de base, comme le nombre de lignes, la taille des données et la taille du fichier, sont stockées dans le metastore. Lorsque ce paramètre a la valeur true, les statistiques de partition sont extraites du metastore. Lorsque ce paramètre a la valeur false, la taille du fichier est extraite du système de fichiers, et le nombre de lignes est extrait du schéma de ligne.

    ![Les statistiques de Hive définissent les statistiques de partition](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Activer la compression intermédiaire

Les tâches de mappage créent des fichiers intermédiaires qui sont utilisés par les tâches du réducteur. La compression intermédiaire réduit la taille des fichiers intermédiaires.

En général, les travaux Hadoop subissent des goulots d’étranglement au niveau des E/S. La compression des données peut accélérer les E/S et le transfert global sur le réseau.

Les types de compression disponibles sont :

| Format | Outil | Algorithme | Extension de fichier | Fractionnable ? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Non  |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | OUI |
| LZO | Lzop | LZO | .lzo | Oui, si indexé |
| Snappy | N/A | Snappy | Snappy | Non  |

En règle générale, il est important d’avoir une méthode de compression fractionnable. Sinon, le nombre de mappeurs créés est très faible. Si les données d’entrée sont textuelles, `bzip2` est la meilleure option. Pour le format ORC, l’option de compression la plus rapide est Snappy.

1. Pour activer la compression intermédiaire, accédez à l’onglet **Configs (Configurations)** de Hive, puis réglez le paramètre `hive.exec.compress.intermediate` sur true. La valeur par défaut est false.

    ![Hive compresse les fichiers intermédiaires](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]
    > Pour compresser les fichiers intermédiaires, choisissez un codec de compression ayant un coût d’UC faible, même s’il n’a pas une sortie de compression élevée.

2. Pour définir le codec de compression intermédiaire, ajoutez la propriété personnalisée `mapred.map.output.compression.codec` au fichier `hive-site.xml` ou `mapred-site.xml`.

3. Pour ajouter un paramètre personnalisé :

    a. Accédez à l’onglet **Configs (Configuration)** de Hive et sélectionnez l’onglet **Advanced (Avancé)**.

    b. Dans l’onglet **Advanced (Avancé)**, recherchez et développez le panneau **Custom hive-site (hive-site personnalisé)**.

    c. Cliquez sur le lien **Add Property (Ajouter une propriété)** en bas du volet Custom hive-site (hive-site personnalisé).

    d. Dans la fenêtre Add Property (Ajouter une propriété), entrez la clé `mapred.map.output.compression.codec` et la valeur `org.apache.hadoop.io.compress.SnappyCodec`.

    e. Cliquez sur **Add**.

    ![Propriété personnalisée de Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Cela compresse le fichier intermédiaire avec Snappy. Une fois la propriété ajoutée, elle apparaît dans le volet Custom Hive-site (hive-site personnalisé).

    > [!NOTE]
    > Cette procédure modifie le fichier `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Compresser la sortie finale

La sortie finale de Hive peut aussi être compressée.

1. Pour compresser la sortie finale de Hive, accédez à l’onglet **Configs (Configurations)** de Hive, puis réglez le paramètre `hive.exec.compress.output` sur true. La valeur par défaut est false.

2. Pour choisir le codec de compression de la sortie, ajoutez la propriété personnalisée `mapred.output.compression.codec` dans le volet Custom hive-site (hive-site personnalisé), comme indiqué à l’étape 3 de la section précédente.

    ![Propriété personnalisée de Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Activer l’exécution spéculative

L’exécution spéculative lance un certain nombre de tâches en double afin de détecter et de bloquer celle qui ralentit les autres, tout en améliorant l’exécution des travaux par l’optimisation des résultats de chaque tâche.

N’activez pas l’exécution spéculative pour les tâches MapReduce à exécution longue qui traitent beaucoup d’entrées.

* Pour activer l’exécution spéculative, accédez à l’onglet **Configs (Configurations)** de Hive, puis réglez le paramètre `hive.mapred.reduce.tasks.speculative.execution` sur true. La valeur par défaut est false.

    ![Exécution spéculative sur les tâches MapReduce dans Hive](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Paramétrer des partitions dynamiques

Hive permet de créer des partitions dynamiques lors de l’insertion d’enregistrements dans une table, sans prédéfinir chaque partition. Il s’agit d’une fonctionnalité puissante, mais elle peut entraîner la création d’un grand nombre de partitions et d’un grand nombre de fichiers pour chaque partition.

1. Pour que Hive crée des partitions dynamiques, la valeur du paramètre `hive.exec.dynamic.partition` doit être true (valeur par défaut).

2. Sélectionnez le mode de partition dynamique *strict (strict)*. Dans ce mode, au moins une partition doit être statique. Cela bloque les requêtes sans filtre de partition dans la clause WHERE. Autrement dit, le mode *strict* bloque les requêtes qui analysent toutes les partitions. Accédez à l’onglet **Configs (Configurations)** de Hive, puis réglez `hive.exec.dynamic.partition.mode` sur **strict (strict)**. La valeur par défaut est **nonstrict (non strict)**.
 
3. Pour limiter le nombre de partitions dynamiques à créer, modifiez le paramètre hive.exec.max.dynamic.partitions. La valeur par défaut est 5 000.
 
4. Pour limiter le nombre total de partitions dynamiques par nœud, modifiez `hive.exec.max.dynamic.partitions.pernode`. La valeur par défaut est 2 000.

### <a name="enable-local-mode"></a>Activer le mode local

Le mode local permet à Hive d’effectuer toutes les tâches d’un travail sur une seule machine ou parfois dans un seul processus. Cela améliore les performances des requêtes si les données d’entrée sont peu nombreuses et si la charge nécessaire au lancement des tâches des requêtes consomme un pourcentage important de l’exécution globale des requêtes.

Pour activer le mode local, ajoutez le paramètre `hive.exec.mode.local.auto` au panneau Custom hive-site (hive-site personnalisé), comme indiqué à l’étape 3 de la section [Activer la compression intermédiaire](#enable-intermediate-compression).

![Exécution automatique de Hive en mode local](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Génération d’un seul travail MapReduce par MultiGROUP BY

Lorsque cette propriété est réglée sur true, une requête MultiGROUP BY avec des clés group-by communes génère un seul travail MapReduce.  

Pour activer ce comportement, ajoutez le paramètre `hive.multigroupby.singlereducer` dans le volet Custom hive-site (hive-site personnalisé), comme indiqué à l’étape 3 de la section [Activer la compression intermédiaire](#enable-intermediate-compression).

![Hive définit un seul travail MapReduce avec MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Autres optimisations Hive

Les sections suivantes décrivent des autres optimisations Hive que vous pouvez définir.

#### <a name="join-optimizations"></a>Joindre des optimisations

Le type de jointure par défaut dans Hive est la *jointure de lecture aléatoire*. Dans Hive, les mappeurs spéciaux lisent l’entrée et envoient une paire clé/valeur de jointure à un fichier intermédiaire. Hadoop trie et fusionne ces paires dans une étape de lecture aléatoire. Cette étape de lecture aléatoire est coûteuse. La sélection de la jointure appropriée à vos données peut améliorer considérablement les performances.

| Type de jointure | Lorsque le répertoire | Comment | Paramètres Hive | Commentaires |
| -- | -- | -- | -- | -- |
| Jointure de lecture aléatoire | <ul><li>Choix par défaut</li><li>Fonctionne toujours</li></ul> | <ul><li>Lit une partie de l’une des tables</li><li>Compartimente et trie en fonction de la clé de jointure</li><li>Envoie un compartiment à chaque Reduce</li><li>Jointure effectuée côté Reduce</li></ul> | Aucun paramètre Hive important nécessaire | Fonctionne à chaque fois |
| Jointure de mappage | <ul><li>Une table peut tenir dans la mémoire</li></ul> | <ul><li>Lit la petite table dans la table de hachage mémoire</li><li>Achemine le flux via une partie du fichier volumineux</li><li>Joint chaque enregistrement de la table de hachage</li><li>Les jointures sont effectuées par le mappeur uniquement</li></ul> | `hive.auto.confvert.join=true` | Très rapide, mais limitée |
| Compartiment de fusion et tri | Si les deux tables sont : <ul><li>Triées de la même façon</li><li>Compartimentées de la même façon</li><li>Jointes sur la colonne triée/compartimentée</li></ul> | Chaque processus : <ul><li>Lit un compartiment dans chaque table</li><li>Traite la ligne avec la valeur la plus basse</li></ul> | `hive.auto.convert.sortmerge.join=true` | Très efficace |

#### <a name="execution-engine-optimizations"></a>Optimisations du moteur d’exécution

Recommandations supplémentaires pour optimiser le moteur d’exécution Hive :

| Paramètre | Recommandé | Valeur par défaut dans HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = plus sûr, plus lent ; false = plus rapide | false |
| `tez.am.resource.memory.mb` | Limite supérieure de 4 Go pour la plupart du temps | Réglée automatiquement |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="pig-optimization"></a>Optimisation de Pig

Vous pouvez modifier les propriétés de Pig dans l’interface utilisateur Web d’Ambari pour paramétrer les requêtes Pig. La modification directe des propriétés de Pig dans Ambari modifie les propriétés de Pig dans le fichier `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Pour modifier les propriétés de Pig, accédez à l’onglet **Configs (Configurations)** de Pig, puis développez le volet **Advanced pig-properties (Propriétés avancées de Pig)**.

2. Recherchez la propriété concernée, supprimez sa mise en commentaire et modifiez sa valeur.

3. Sélectionnez **Save (Enregistrer)** en haut à droite de la fenêtre pour enregistrer la nouvelle valeur. Certaines propriétés peuvent nécessiter un redémarrage du service.

    ![Propriétés avancées de Pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]
> Les paramètres de niveau session ont priorité sur les valeurs des propriétés dans le fichier `pig.properties`.

### <a name="tune-execution-engine"></a>Paramétrer le moteur d’exécution

Deux moteurs d’exécution sont disponibles pour exécuter des scripts Pig : MapReduce et Tez. Tez est un moteur optimisé, beaucoup plus rapide que MapReduce.

1. Pour changer de moteur d’exécution, dans le volet **Advanced pig-properties (Propriétés avancées de Pig)**, recherchez la propriété `exectype`.

2. La valeur par défaut est **MapReduce**. Remplacez-la par **Tez**.


### <a name="enable-local-mode"></a>Activer le mode local

Comme dans Hive, le mode local permet d’accélérer les tâches avec des quantités de données relativement faibles.

1. Pour activer le mode local, réglez `pig.auto.local.enabled` sur **true**. La valeur par défaut est false.

2. Les travaux avec une taille de données d’entrée inférieure à la valeur de propriété `pig.auto.local.input.maxbytes` sont considérés comme des petites tâches. La valeur par défaut est 1 Go.


### <a name="copy-user-jar-cache"></a>Copier le cache du fichier jar de l’utilisateur

Pig copie les fichiers JAR requis par les FDU dans un cache distribué pour les rendre disponibles aux nœuds de la tâche. Ces fichiers JAR ne changent pas souvent. S’il est activé, le paramètre `pig.user.cache.enabled` permet placer des fichiers JAR dans un cache afin de les réutiliser pour des travaux exécutés par le même utilisateur. Ce qui augmente légèrement les performances des travaux.

1. Pour l’activer, réglez `pig.user.cache.enabled` sur true. La valeur par défaut est false.

2. Pour définir le chemin de base des fichiers JAR mis en cache, définissez le chemin d’accès de base dans `pig.user.cache.location`. Par défaut, il s’agit de `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Optimiser les performances avec les paramètres de mémoire

Les paramètres de mémoire suivants peuvent aider à optimiser les performances des scripts Pig.

* `pig.cachedbag.memusage` : quantité de mémoire allouée à un conteneur. Un conteneur est une collection de tuples. Un tuple est un ensemble ordonné de champs, et un champ est une donnée. Si les données d’un conteneur dépassent la mémoire allouée, elles sont déversées sur le disque. La valeur par défaut est 0,2, soit 20 % de la mémoire disponible. Cette mémoire est partagée entre tous les conteneurs d’une application.

* `pig.spill.size.threshold` : les conteneurs supérieurs à ce seuil de dépassement de capacité (en octets) sont déversés sur le disque. La valeur par défaut est 5 Mo.


### <a name="compress-temporary-files"></a>Compresser les fichiers temporaires

Pig génère des fichiers temporaires lors de l’exécution du travail. La compression des fichiers temporaires entraîne une augmentation des performances lors de la lecture ou de l’écriture de fichiers sur le disque. Les paramètres suivants permettent de compresser les fichiers temporaires.

* `pig.tmpfilecompression` : lorsque ce paramètre a pour valeur true, il active la compression des fichiers temporaires. La valeur par défaut est false.

* `pig.tmpfilecompression.codec` : codec à utiliser pour compresser les fichiers temporaires. Les codecs de compression recommandés sont LZO et Snappy pour leur faible utilisation de l’UC.

### <a name="enable-split-combining"></a>Activer la combinaison de fractionnements

Lorsque ce paramètre est activé, les petits fichiers sont combinés pour réduire le nombre de tâches de mappage. Cela améliore l’efficacité des travaux comportant de nombreux petits fichiers. Pour l’activer, réglez `pig.noSplitCombination` sur true. La valeur par défaut est false.


### <a name="tune-mappers"></a>Paramétrer les mappeurs

Le nombre de mappeurs est contrôlé en modifiant la propriété `pig.maxCombinedSplitSize`. Cela spécifie la taille des données à traiter par une tâche de mappage unique. La valeur par défaut est la taille de bloc par défaut du système de fichiers. Augmenter cette valeur permet de diminuer le nombre de tâches du mappeur.


### <a name="tune-reducers"></a>Paramétrer les réducteurs

Le nombre de réducteurs est calculé en fonction du paramètre `pig.exec.reducers.bytes.per.reducer`. Le paramètre spécifie le nombre d’octets traités par réducteur (1 Go par défaut). Pour limiter le nombre maximal de réducteurs, réglez la propriété `pig.exec.reducers.max` (999 par défaut).


## <a name="hbase-optimization-with-the-ambari-web-ui"></a>Optimisation de HBase avec l’interface utilisateur Web d’Ambari

La configuration de HBase est modifiable dans l’onglet **HBase Configs (Configurations de HBase)**. Les sections suivantes décrivent certains paramètres de configuration importants qui affectent les performances de HBase.

### <a name="set-hbaseheapsize"></a>Définir HBASE_HEAPSIZE

La taille de tas HBase spécifie la quantité maximale de tas à utiliser en mégaoctets par les serveurs de *région* et *maîtres*. La valeur par défaut est 1 000 Mo. Elle doit être adaptée à la charge de travail du cluster.

1. Pour la modifier, accédez au volet **Advanced HBase-env (HBase-env avancé)** dans l’onglet **Configs (Configurations)** et recherchez le paramètre `HBASE_HEAPSIZE`.

2. Remplacez la valeur par défaut par 5 000 Mo.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Optimiser les charges de travail nécessitant beaucoup de lectures

Les configurations suivantes sont importantes pour améliorer les performances des charges de travail nécessitant beaucoup de lectures.

#### <a name="block-cache-size"></a>Taille du cache de blocs

Le cache de blocs est le cache de lecture. Sa taille est contrôlée par le paramètre `hfile.block.cache.size`. La valeur par défaut est de 0,4, soit 40 % de la mémoire totale du serveur de la région. Plus la taille du cache de blocs est importante, plus les lectures aléatoires sont rapides.

1. Pour modifier ce paramètre, accédez à l’onglet **Settings (Paramètres)** dans l’onglet **Configs (Configurations)** de HBase, puis recherchez **% of RegionServer Allocated to Read Buffers (% du serveur de région alloué aux mémoires tampons de lecture)**.

    ![Taille du cache de blocs de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
2. Pour modifier la valeur, sélectionnez l’icône **Edit (Modifier)**.


#### <a name="memstore-size"></a>Taille de memstore

Toutes les modifications sont stockées dans la mémoire tampon appelée *Memstore*. Cela augmente le volume total de données qui peuvent être écrites sur le disque lors d’une seule opération, et accélère l’accès postérieur aux modifications récentes. La taille de Memstore est définie par les deux paramètres suivants :

* `hbase.regionserver.global.memstore.UpperLimit` : définit le pourcentage maximal du serveur de région que Memstore peut utiliser.

* `hbase.regionserver.global.memstore.LowerLimit` : définit le pourcentage minimal du serveur de région que Memstore peut utiliser.

Pour optimiser les lectures aléatoires, vous pouvez réduire les limites supérieure et inférieure de Memstore.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Nombre de lignes extraites lors de l’analyse du disque

Le paramètre `hbase.client.scanner.caching` définit le nombre de lignes lues sur le disque lorsque la méthode `next` est appelée sur un scanneur.  La valeur par défaut est 100. Plus ce nombre est élevé, moins les appels distants effectués depuis le client vers le serveur de région sont nombreux, ce qui accélère l’analyse. Toutefois, cela augmente également la sollicitation de la mémoire sur le client.

![Nombre de lignes extraites dans HBase](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]
> Ne définissez pas une valeur qui rende le délai entre l’appel de la méthode suivante sur un scanneur supérieure au délai du scanneur. La durée d’expiration du scanneur est définie par la propriété `hbase.regionserver.lease.period`.


### <a name="optimize-write-heavy-workloads"></a>Optimiser les charges de travail nécessitant beaucoup d’écritures

Les configurations suivantes sont importantes pour améliorer les performances des charges de travail nécessitant beaucoup d’écritures.


#### <a name="maximum-region-file-size"></a>Taille maximale du fichier de région

HBase stocke les données dans un format de fichier interne, appelé *HFile*. La propriété `hbase.hregion.max.filesize` définit la taille d’un fichier HFile unique pour une région.  Une région est divisée en deux si la somme de tous les fichiers HFile d’une région est supérieure à ce paramètre.
 
![Taille maximale du fichier de région dans HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Plus la taille du fichier de région est importante, plus le nombre de fractionnements est réduit. Vous pouvez augmenter la taille du fichier pour déterminer une valeur qui optimise les performances d’écriture.


#### <a name="avoid-update-blocking"></a>Éviter le blocage des mises à jour

* La propriété `hbase.hregion.memstore.flush.size` définit la taille à laquelle Memstore est vidé sur le disque. La taille par défaut est de 128 Mo.

* Le multiplicateur de bloc de région Hbase est défini par `hbase.hregion.memstore.block.multiplier`. La valeur par défaut est 4. La valeur maximale autorisée est 8 Go.

* HBase bloque les mises à jour si le paramètre Memstore est de (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) octets.

    Avec les valeurs par défaut de la taille de vidage et du multiplicateur de bloc, les mises à jour sont bloquées lorsque Memstore a une taille égale à 128 * 4 = 512 Mo. Pour réduire le nombre de blocages des mises à jour, augmentez la valeur de `hbase.hregion.memstore.block.multiplier`.

![Multiplicateur de bloc de région de HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Définir la taille de Memstore

La taille de Memstore est définie par les paramètres `hbase.regionserver.global.memstore.UpperLimit` et `hbase.regionserver.global.memstore.LowerLimit`. En définissant deux valeurs égales, vous réduisez les pauses pendant les écritures (ce qui augmente la fréquence des vidages) et améliore les performances d’écriture.


### <a name="set-memstore-local-allocation-buffer"></a>Définir la mémoire tampon d’allocation locale de Memstore

L’utilisation de la mémoire tampon d’allocation locale de Memstore est déterminée par la propriété `hbase.hregion.memstore.mslab.enabled`. Lorsqu’elle est activée (true), elle empêche la fragmentation des tas pendant une opération nécessitant beaucoup d’écritures. La valeur par défaut est true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Étapes suivantes

* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)
* [API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
