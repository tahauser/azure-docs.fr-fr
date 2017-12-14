---
title: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store Spark | Microsoft Docs"
description: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store Spark"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 3c8ef6fd200f67ebc216c967bb1a6250ddcc15d4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Recommandations en matière d’optimisation des performances pour Spark sur HDInsight et Azure Data Lake Store

Lors du réglage des performances sur Spark, vous devez prendre en compte le nombre d’applications qui s’exécuteront sur votre cluster.  Par défaut, vous pouvez exécuter 4 applications simultanément sur votre cluster HDI (Remarque : le paramètre par défaut est susceptible de changer).  Vous pouvez décider d’utiliser moins d’applications, vous pouvez donc remplacer les paramètres par défaut et utiliser le cluster davantage pour ces applications.  

## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster.
* **Exécution d’un cluster Spark sur Azure Data Lake Store**.  Pour plus d’informations à ce sujet, consultez [Utiliser le cluster HDInsight Spark pour analyser les données dans Data Lake Store](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Instructions d’optimisation des performances sur ADLS**.  Pour les concepts généraux sur les performances, consultez [Data Lake Store Performance Tuning Guidance (Recommandations en matière d’optimisation des performances de Data Lake Store)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>parameters

Lors de l’exécution de travaux Spark, voici les principaux paramètres qui peuvent être paramétrés pour améliorer les performances sur ADLS :

* **Num-executors** : le nombre de tâches simultanées qui peuvent être exécutées.

* **Executor-memory** : la quantité de mémoire allouée à chaque exécuteur.

* **Executor-cores** : le nombre de cœurs alloués à chaque exécuteur.                     

**Num-executors** Num-executors définit le nombre maximal de tâches pouvant s’exécuter en parallèle.  Le nombre réel de tâches qui peuvent s’exécuter en parallèle est limité par la mémoire et les ressources processeur disponibles dans votre cluster.

**Executor-memory** : la quantité de mémoire allouée à chaque exécuteur.  La mémoire nécessaire pour chaque exécuteur dépend de la tâche.  Pour les opérations complexes, la mémoire doit être plus élevée.  Pour les opérations simples telles que la lecture et l’écriture, les besoins en mémoire seront inférieurs.  La quantité de mémoire pour chaque exécuteur peut être consultée dans Ambari.  Dans Ambari, accédez à Spark et affichez l’onglet Configurations.  

**Executor-cores** : définit la quantité de cœurs utilisés par exécuteur, ce qui détermine le nombre de threads parallèles qui peuvent être exécutés par l’exécuteur.  Par exemple, si Executor-cores = 2, alors chaque exécuteur peut exécuter des 2 tâches parallèles dans l’exécuteur.  La valeur à utiliser pour Executor-cores dépendra de la tâche.  Les travaux intensifs en E/S ne nécessitent pas une grande quantité de mémoire par la tâche, ainsi chaque exécuteur peut gérer davantage de tâches parallèles.

Par défaut, deux cœurs YARN virtuels sont définis pour chaque cœur physique lors de l’exécution de Spark sur HDInsight.  Ce nombre offre un bon équilibre de simultanéité et de quantité de changement de contexte à partir de plusieurs threads.  

## <a name="guidance"></a>Assistance

Lorsque vous exécutez des charges de travail analytiques Spark pour l’utilisation de données dans Data Lake Store, nous vous recommandons d’utiliser la version la plus récente de HDInsight pour bénéficier des meilleures performances avec Data Lake Store. Lorsque votre travail nécessite une grande quantité d’E/S, vous pouvez configurer certains paramètres pour améliorer les performances.  Azure Data Lake Store est une plateforme de stockage hautement évolutive capable de gérer un débit élevé.  Si le travail consiste principalement en une série de lectures ou d’écritures, l’augmentation de la concurrence pour les E/S à destination et en provenance d’Azure Data Lake Store peut accroître les performances.

Vous disposez de plusieurs méthodes générales pour augmenter la concurrence des travaux qui nécessitent une grande quantité d’E/S.

**Étape 1 : Déterminer le nombre d’applications s’exécutant sur votre cluster** : vous devez connaître le nombre d’applications en cours d’exécution sur le cluster, y compris celle en cours.  Les valeurs par défaut pour chaque paramètre Spark supposent que 4 applications s’exécutent simultanément.  Par conséquent, vous avez seulement 25 % du cluster disponible pour chaque application.  Pour obtenir de meilleures performances, vous pouvez remplacer les valeurs par défaut en modifiant le nombre d’exécuteurs.  

**Étape 2 : Configurer Executor-memory** : la première chose à définir est Executor-memory.  La mémoire sera dépendante de la tâche que vous exécutez.  Vous pouvez augmenter la simultanéité en allouant de moins de mémoire par exécuteur.  Si vous voyez des exceptions de mémoire insuffisante lors de l’exécution de votre tâche, vous devez augmenter la valeur de ce paramètre.  Une solution consiste à obtenir davantage de mémoire à l’aide d’un cluster qui possède une quantité supérieure de mémoire ou en augmentant la taille de votre cluster.  Une mémoire plus importante permettra d’utiliser plus d’exécuteurs, pour plus de simultanéité.

**Étape 3 : Définir Executor-cores** : pour les charges de travail intensives qui n’ont pas d’opérations complexes, il est conseillé de commencer avec une valeur Executor-cores élevée pour augmenter le nombre de tâches parallèles par exécuteur.  Régler Executor-cores sur 4 est un bon point de départ.   

    executor-cores = 4
Augmenter la valeur d’Executor-cores vous donne plus de parallélisme, vous pouvez donc expérimenter différentes valeurs pour ce paramètre.  Pour les tâches qui ont des opérations plus complexes, vous devez réduire le nombre de cœurs par exécuteur.  Si la valeur d’Executor-cores est supérieure à 4, puis le garbage collection peut devenir inefficace et dégrader les performances.

**Étape 4 : Déterminer la quantité de mémoire YARN du cluster** : cette information est disponible dans Ambari.  Accédez à YARN et affichez l’onglet Configurations.  La mémoire YARN s’affiche dans cette fenêtre.  
Remarque : lorsque vous êtes dans la fenêtre, vous pouvez également voir la taille de conteneur YARN par défaut.  La taille du conteneur YARN est identique au paramètre de mémoire par exécuteur.

    Total YARN memory = nodes * YARN memory per node
**Étape 5 : Calculer num-executors**

**Calculer la contrainte de mémoire** : le paramètre num-executors est limité par la mémoire ou par le processeur.  La contrainte de mémoire est déterminée par la quantité de mémoire YARN disponible pour votre application.  Vous devez prendre la mémoire YARN totale et la diviser par executor-memory.  La mise à l’échelle de la contrainte doit être adaptée pour le nombre d’applications, nous la divisons donc par le nombre d’applications.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Calculer la contrainte de processeur** : la contrainte de processeur est calculée comme le nombre total de cœurs virtuels divisé par le nombre de cœurs par exécuteur.  Il existe 2 cœurs virtuels pour chaque noyau physique.  Comme pour la contrainte de mémoire, nous divisons par le nombre d’applications.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Définir num-executors** : le paramètre num-executors est déterminé par la valeur minimale entre la contrainte de mémoire et la contrainte de processeur. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Définir un nombre plus élevé pour num-executors’augmente pas nécessairement les performances.  Vous devez prendre en compte le fait que l’ajout d’exécuteurs ajoute une charge pour chaque exécuteur supplémentaire, ce qui peut dégrader les performances.  Num-executors est limité par les ressources de cluster.    

## <a name="example-calculation"></a>Exemple de calcul

Supposons que vous possédez un cluster composé de 8 nœuds D4v2 exécutant 2 applications, dont celle que vous souhaitez exécuter.  

**Étape 1 : Déterminer le nombre d’applications s’exécutant sur votre cluster** : vous savez que vous avez 2 applications sur votre cluster, y compris celle que vous souhaitez exécuter.  

**Étape 2 : Configurer executor-memory** : pour cet exemple, nous déterminons que 6 Go pour executor-memory sera suffisant pour les travaux intensifs en E/S.  

    executor-memory = 6GB
**Étape 3 : Définir executor-cores** : dans la mesure où il s’agit d’un travail intensif en E/S, nous pouvons définir le nombre de cœurs pour chaque exécuteur sur 4.  Définir le nombre de cœurs par exécuteur sur une valeur supérieure à 4 peut provoquer des problèmes de garbage collection.  

    executor-cores = 4
**Étape 4 : Déterminer la quantité de mémoire YARN du cluster** : nous accédons à Ambari pour déterminer que chaque D4v2 possède 25 Go de mémoire YARN.  Étant donné qu’il y a 8 nœuds, la mémoire YARN disponible est multipliée par 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Étape 5 : Calculer num-executors** : le paramètre num-executors est déterminé par la valeur minimale entre la contrainte de mémoire et la contrainte de processeur, divisée par le nombre d’applications exécutées sur Spark.    

**Calculer la contrainte de mémoire** : la contrainte de mémoire est calculée comme la mémoire YARN totale divisée par la mémoire par exécuteur.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Calculer la contrainte de processeur** : la contrainte de processeur est calculée comme le nombre total de cœurs YARN divisé par le nombre de cœurs par exécuteur.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Définir num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

