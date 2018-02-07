---
title: Optimiser les performances des travaux Spark - Azure HDInsight | Microsoft Docs
description: "Présente des stratégies courantes permettant d’optimiser les performances des clusters Spark."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 64ddb70f071a9fadc6fef64dcd3506c6d6255481
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="optimize-spark-jobs"></a>Optimiser des travaux Spark

Découvrez comment optimiser la configuration de cluster Spark pour votre charge de travail particulière.  Le défi le plus courant est la sollicitation de la mémoire due à aux configurations incorrectes (en particulier les exécuteurs de taille incorrecte), aux longues opérations et aux tâches qui entraînent des opérations cartésiennes. Vous pouvez accélérer les travaux avec une mise en cache appropriée et en autorisant [l’asymétrie des données](#optimize-joins-and-shuffles). Pour des performances optimales, surveillez les exécutions de travaux Spark de longue durée et consommatrices de ressources.

Les sections suivantes décrivent des recommandations et des optimisations courantes de travaux Spark.

## <a name="choose-the-data-abstraction"></a>Choisir l’abstraction des données

Spark 1.x utilise des jeux de données RDD (Resilient Distributed Datasets) pour effectuer l’abstraction des données. Quant à Spark 2.x, il a introduit les DataFrames et les DataSets. Voici leurs avantages relatifs respectifs :

* **DataFrames**
    * Meilleur choix dans la plupart des cas
    * Fournit l’optimisation des requêtes par le biais de Catalyst
    * Génération de code à l’échelle globale
    * Accès direct à la mémoire
    * Faible surcharge de garbage collection
    * Convivialité inférieure à celles des DataSets pour les développeurs, car il n’y a aucune programmation d’objet de domaine ni vérification au moment de la compilation
* **DataSets**
    * Convient aux pipelines ETL complexes où l’impact sur les performances est acceptable
    * Ne convient pas dans les agrégations où l’impact sur les performances peut être considérable
    * Fournit l’optimisation des requêtes par le biais de Catalyst
    * Convivialité pour les développeurs, grâce à la programmation des objets de domaine et à la vérification au moment de la compilation
    * Ajoute une surcharge de sérialisation/désérialisation
    * Surcharge de garbage collection élevée
    * Interrompt la génération de code à l’échelle globale
* **RDD**
    * Dans Spark 2.x, vous n’avez pas besoin d’utiliser de RDD, sauf si vous devez générer un nouveau RDD personnalisé
    * Aucune optimisation de requête par le biais de Catalyst
    * Aucune génération de code à l’échelle globale
    * Surcharge de garbage collection élevée
    * Nécessité d’utiliser des API Spark 1.x héritées

## <a name="use-optimal-data-format"></a>Utiliser le format de données optimal

Spark prend en charge de nombreux formats, tels que csv, json, xml, parquet, orc et avro. Spark peut être étendu pour prendre en charge de nombreux autres formats avec des sources de données externes. Pour plus d’informations, consultez [Packages Spark](https://spark-packages.org).

Le meilleur format du point de vue des performances est parquet avec *compression Snappy*, qui est l’option par défaut dans Spark 2.x. Parquet stocke les données sous forme de colonnes et il est fortement optimisé dans Spark.

## <a name="select-default-storage"></a>Sélectionner le stockage par défaut

Quand vous créez un cluster Spark, vous pouvez sélectionner le Stockage Blob Azure ou Azure Data Lake Store comme stockage par défaut de votre cluster. Les deux options offrent l’avantage d’un stockage à long terme pour les clusters temporaires. Ainsi, vos données ne sont pas supprimées automatiquement quand vous supprimez votre cluster. Vous pouvez recréer un cluster temporaire et encore accéder à vos données.

| Type de magasin | Système de fichiers | Vitesse | Temporaire | Cas d'utilisation |
| --- | --- | --- | --- | --- |
| Stockage Blob Azure | **wasb:**//url/ | **Standard** | Oui | Cluster temporaire |
| Azure Data Lake Store | **adl:**//url/ | **Plus rapide** | Oui | Cluster temporaire |
| HDFS local | **hdfs:**//url/ | **Le plus rapide** | Non  | Cluster 24/7 interactif |

## <a name="use-the-cache"></a>Utiliser le cache

Spark fournit ses propres mécanismes de mise en cache native, que vous pouvez utiliser par le biais de différentes méthodes telles que `.persist()`, `.cache()` et `CACHE TABLE`. Cette mise en cache native est efficace avec les petits jeux de données, ainsi que dans les pipelines ETL où vous devez mettre en cache des résultats intermédiaires. Toutefois, à l’heure actuelle la mise en cache native de Spark ne fonctionne pas correctement avec le partitionnement, car une table mise en cache ne conserve pas les données de partitionnement. Une technique de mise en cache plus générique et plus fiable est *la mise en cache de la couche de stockage*.

* Mise en cache native de Spark (non recommandée)
    * Convient aux petits jeux de données
    * Ne fonctionne pas avec le partitionnement, mais ceci peut changer dans les versions ultérieures de Spark

* Mise en cache au niveau du stockage (recommandée)
    * Peut être implémentée à l’aide de [Alluxio](http://www.alluxio.org/)
    * Utilise la mise en cache en mémoire et SSD

* HDFS local (recommandé)
    * Chemin `hdfs://mycluster`
    * Utilise la mise en cache SSD
    * Les données mises en cache sont perdues quand vous supprimez le cluster, ce qui nécessite une reconstruction du cache

## <a name="use-memory-efficiently"></a>Utiliser efficacement la mémoire

Spark opère en plaçant les données en mémoire. La gestion des ressources en mémoire est donc un aspect clé de l’optimisation de l’exécution des travaux Spark.  Vous pouvez appliquer plusieurs techniques pour utiliser efficacement la mémoire de votre cluster.

* Privilégiez les partitions de données de petite taille, et prenez en compte la taille, les types et la distribution des données dans votre stratégie de partitionnement.
* Utilisez la [sérialisation des données Kryo](https://github.com/EsotericSoftware/kryo), plus récente et plus efficace, au lieu de la sérialisation Java par défaut.
* Privilégiez l’utilisation de YARN, car il sépare `spark-submit` en lot.
* Surveillez et affinez les paramètres de configuration de Spark.

Pour référence, la structure de la mémoire Spark et certains des principaux paramètres mémoire de l’exécuteur sont illustrés dans l’image suivante.

### <a name="spark-memory-considerations"></a>Considérations relatives à la mémoire Spark

Si vous utilisez YARN, celui-ci contrôle la somme maximale de mémoire utilisée par tous les conteneurs sur chaque nœud Spark.  Le diagramme suivant montre les objets clés et leurs relations.

![Gestion de la mémoire Spark avec YARN](./media/apache-spark-perf/yarn-spark-memory.png)

Pour éviter les messages « Mémoire insuffisante », essayez les solutions suivantes :

* Passez en revue les lectures aléatoires de gestion DAG. Réduisez par la réduction côté mappage, prépartitionnez (ou compartimentez) les données sources, optimisez les lectures aléatoires uniques, et réduisez la quantité de données envoyées.
* Privilégiez `ReduceByKey` (avec sa limite de mémoire fixe) à `GroupByKey`, qui fournit des agrégations, le fenêtrage et d’autres fonctions, mais qui n’a pas de limite de mémoire.
* Privilégiez `TreeReduce`, qui effectue plus de travail sur les partitions ou les exécuteurs, à `Reduce`, qui effectue tout le travail sur le pilote.
* Tirez parti des DataFrames plutôt que des objets RDD de niveau inférieur.
* Créez des ComplexTypes qui encapsulent des actions, telles que « N premiers », différentes agrégations ou opérations de fenêtrage.

## <a name="optimize-data-serialization"></a>Optimiser la sérialisation des données

Les travaux Spark étant distribués, une sérialisation des données appropriée est importante afin d’optimiser les performances.  Il existe deux options de sérialisation pour Spark :

* La sérialisation Java est l’option par défaut.
* La sérialisation Kryo est un format plus récent susceptible d’entraîner une sérialisation plus rapide et plus compacte que Java.  Kryo exige que vous inscriviez les classes dans votre programme, et il ne prend pas encore en charge tous les types sérialisables.

## <a name="use-bucketing"></a>Utiliser la création de compartiments

La création de compartiments est semblable au partitionnement des données, mais chaque compartiment peut contenir un ensemble de valeurs de colonne plutôt qu’une seule valeur. Elle fonctionne bien pour le partitionnement sur un grand nombre de valeurs (plusieurs millions, voire plus), telles que les identificateurs de produits. Un compartiment est déterminé en hachant la clé de compartiment de la ligne. Les tables compartimentées offrent des optimisations uniques, car elles stockent des métadonnées relatives à la façon dont elles ont été compartimentées et triées.

Voici quelques fonctionnalités avancées offertes par les compartiments :

* Optimisation des requêtes en fonction des méta-informations de compartiments
* Optimisation des agrégations
* Optimisation des jointures

Vous pouvez utiliser le partitionnement et la création de compartiments en même temps.

## <a name="optimize-joins-and-shuffles"></a>Optimiser les jointures et les lectures aléatoires

Si vous avez des travaux lents sur une jointure ou une lecture aléatoire, la cause est probablement *l’asymétrie des données* des travaux. Par exemple, un travail de mappage peut prendre 20 secondes, mais l’exécution d’un travail où les données sont jointes ou assignées de manière aléatoire prend des heures.   Pour corriger l’asymétrie des données, vous devez convertir l’intégralité de la clé en valeur salt, ou utiliser une *valeur salt isolée* pour uniquement un sous-ensemble de clés.  Si vous utilisez une valeur salt isolée, vous devez effectuer un filtrage supplémentaire afin d’isoler votre sous-ensemble de clés salt dans les jointures de mappage. Une autre option consiste à introduire une colonne de compartiment et à pré-agréger d’abord dans les compartiments.

Un autre facteur pouvant ralentir les jointures est le type de jointure. Par défaut, Spark utilise le type de jointure `SortMerge`. Ce type de jointure convient parfaitement aux grands jeux de données, mais il est coûteux en terme de calcul car il doit d’abord trier les côtés gauche et droit des données avant de les fusionner.

Une jointure `Broadcast` est particulièrement adaptée aux petits jeux de données, ou quand un côté de la jointure est beaucoup plus petit que l’autre. Ce type de jointure diffuse un côté à tous les exécuteurs. Il exige donc davantage de mémoire pour les diffusions en général.

Vous pouvez changer le type de jointure dans votre configuration en définissant `spark.sql.autoBroadcastJoinThreshold`, ou vous pouvez définir un indicateur de jointure à l’aide des API DataFrame (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")
sql("SELECT col1, col2 FROM V_JOIN")
```

Si vous utilisez des tables compartimentées, vous disposez d’un troisième type de jointure : `Merge`. Un jeu de données correctement prépartitionné et prétrié ignore la phase de tri coûteuse d’une jointure `SortMerge`.

L’ordre des jointures est important, en particulier dans les requêtes plus complexes. Commencez par les jointures les plus sélectives. Dans la mesure du possible, déplacez également les jointures qui augmentent le nombre de lignes après les agrégations.

Pour gérer le parallélisme, spécifiquement dans le cas des jointures cartésiennes, vous pouvez ajouter des structures imbriquées, le fenêtrage, et éventuellement ignorer une ou plusieurs étapes dans votre travail Spark.

## <a name="customize-cluster-configuration"></a>Personnaliser la configuration du cluster

En fonction de votre charge de travail de cluster Spark, vous pouvez déterminer qu’une configuration Spark autre que la configuration par défaut entraînerait une optimisation de l’exécution des travaux Spark.  Effectuez des tests d’évaluation avec des exemples de charges de travail afin de valider les configurations de cluster différentes des configurations par défaut.

Voici quelques paramètres courants que vous pouvez ajuster :

* `--num-executors` définit le nombre approprié d’exécuteurs.
* `--executor-cores` définit le nombre de cœurs pour chaque exécuteur. En règle générale, vous devez avoir des exécuteurs de taille moyenne, car d’autres processus consomment une partie de la mémoire disponible.
* `--executor-memory` définit la taille de la mémoire pour chaque exécuteur, ce qui contrôle la taille de segment de mémoire sur YARN. Vous devez conserver de la mémoire pour la surcharge d’exécution.

### <a name="select-the-correct-executor-size"></a>Sélectionner la taille d’exécuteur correcte

Lors du choix de votre configuration d’exécuteur, n’oubliez pas de prendre en compte la surcharge de garbage collection Java.

* Facteurs permettant de réduire la taille de l’exécuteur :
    1. Réduisez la taille de segment de mémoire jusqu’à une valeur inférieure à 32 Go afin de maintenir la surcharge de garbage collection à moins de 10 %.
    2. Réduisez le nombre de cœurs afin de maintenir la surcharge de garbage collection à moins de 10 %.

* Facteurs permettant d’augmenter la taille de l’exécuteur :
    1. Réduisez la surcharge de communication entre les exécuteurs.
    2. Réduisez le nombre de connexions ouvertes entre les exécuteurs (N2) sur les clusters de grande taille (> 100 exécuteurs).
    3. Augmentez la taille de segment de mémoire afin de prendre en compte les tâches utilisant beaucoup de mémoire.
    4. Facultatif : Réduisez la surcharge de mémoire par exécuteur.
    5. Facultatif : Augmentez l’utilisation et la concurrence en surabonnant l’UC.

En règle générale, lors de la sélection de la taille d’exécuteur :
    
1. Commencez avec 30 Go par exécuteur et distribuez les cœurs disponibles.
2. Augmentez le nombre de cœurs d’exécuteur pour les clusters de grande taille (> 100 exécuteurs).
3. Augmentez ou diminuez les tailles en fonction des tests d’évaluation et de facteurs tels que la surcharge de garbage collection.

Lors de l’exécution de requêtes simultanées, considérez les points suivants :

1. Commencez avec 30 Go par exécuteur et tous les cœurs.
2. Créez plusieurs applications Spark parallèles en surabonnant les UC (amélioration de la latence d’environ 30 %).
3. Distribuez les requêtes parmi les applications parallèles.
4. Augmentez ou diminuez les tailles en fonction des tests d’évaluation et de facteurs tels que la surcharge de garbage collection.

Surveillez les performances de vos requêtes afin de détecter les valeurs hors norme ou autres problèmes de performances, en examinant l’affichage de la chronologie, le graphique SQL, les statistiques des travaux, et ainsi de suite. Parfois, un ou plusieurs exécuteurs sont plus lents que les autres, et l’exécution des tâches prend beaucoup plus de temps. Cela se produit fréquemment sur les clusters de grande taille (> 30 nœuds). Dans ce cas, répartissez le travail parmi un plus grand nombre de tâches afin que le planificateur puisse compenser cette lenteur. Par exemple, faites en sorte d’avoir au moins deux fois plus de tâches que le nombre de cœurs d’exécuteur dans l’application. Vous pouvez également activer l’exécution spéculative des tâches avec `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Optimiser l’exécution des travaux

* Effectuez une mise en cache si nécessaire. Par exemple, si vous utilisez les données à deux reprises, mettez-les en cache.
* Diffusez les variables vers tous les exécuteurs. Les variables ne sont sérialisées qu’une seule fois, ce qui accélère les recherches.
* Utilisez le pool de threads sur le pilote. Cela permet d’accélérer de nombreuses tâches.

Surveillez régulièrement vos travaux en cours d’exécution afin de détecter tout problème de performances. Si vous avez besoin de plus de détails pour résoudre certains problèmes, utilisez l’un des outils de profilage de performances suivants :

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) surveille l’utilisation de la bande passante par le réseau, le stockage et l’UC.
* [Oracle Java 8 Mission Control](http://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profile le code d’exécuteur et Spark.

Le moteur Tungsten joue un rôle essentiel pour les performances des requêtes Spark 2.x. Il dépend de la génération de code à l’échelle globale. Dans certains cas, la génération de code à l’échelle globale peut être désactivée. Par exemple, si vous utilisez un type non mutable (`string`) dans l’expression d’agrégation, `SortAggregate` s’affiche à la place de `HashAggregate`. Par exemple, pour obtenir de meilleures performances, essayez ce qui suit puis réactivez la génération de code :

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Étapes suivantes

* [Déboguer des travaux Spark en cours d’exécution sur Azure HDInsight](apache-spark-job-debugging.md)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Utiliser l’API REST Spark pour envoyer des travaux à distance à un cluster Spark](apache-spark-livy-rest-interface.md)
* [Tuning Spark (Réglage de Spark)](https://spark.apache.org/docs/latest/tuning.html)
* [How to Actually Tune Your Spark Jobs So They Work (Guide pratique pour paramétrer vos travaux Spark afin qu’ils fonctionnent)](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Sérialisation Kryo](https://github.com/EsotericSoftware/kryo)
