---
title: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store MapReduce | Microsoft Docs"
description: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store MapReduce"
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
ms.openlocfilehash: 522e03769a8f09acd88d92d72c4658407b86bd0b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Recommandations en matière d’optimisation des performances pour MapReduce sur HDInsight et Azure Data Lake Store


## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster.
* **Utilisation de MapReduce sur HDInsight**.  Pour plus d’informations, consultez [Utilisation de MapReduce sur Hadoop sur HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)  
* **Instructions d’optimisation des performances sur ADLS**.  Pour les concepts généraux sur les performances, consultez [Data Lake Store Performance Tuning Guidance (Recommandations en matière d’optimisation des performances de Data Lake Store)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>Paramètres

Lors de l’exécution des travaux MapReduce, voici les paramètres les plus importants que vous pouvez configurer pour améliorer les performances sur ADLS :

* **Mapreduce.map.memory.mb** : la quantité de mémoire à allouer à chaque mappeur
* **Mapreduce.job.maps** : le nombre de tâches de mappage par travail
* **Mapreduce.reduce.memory.mb** : la quantité de mémoire à allouer à chaque réducteur
* **Mapreduce.job.reduces** : le nombre de tâches de réduction par travail

**Mapreduce.map.memory / Mapreduce.reduce.memory** : ce nombre doit être ajusté en fonction de la quantité de mémoire nécessaire pour la tâche de mappage/réduction.  Les valeurs par défaut de mapreduce.map.memory et mapreduce.reduce.memory peuvent être affichées dans Ambari via la configuration de YARN.  Dans Ambari, accédez à YARN et affichez l’onglet Configurations.  La mémoire YARN s’affiche.  

**Mapreduce.job.maps / Mapreduce.job.reduces** : détermine le nombre maximal de mappeurs ou de réducteurs à créer.  Le nombre de fractionnements détermine le nombre de mappeurs créés pour le travail MapReduce.  Par conséquent, vous pouvez obtenir moins mappeurs que demandé s’il existe moins de fractionnements que de mappeurs demandés.       

## <a name="guidance"></a>Assistance

**Étape 1 : Déterminer le nombre de travaux en cours d’exécution** : par défaut, MapReduce utilise l’ensemble du cluster pour votre travail.  Vous pouvez utiliser une partie moindre du cluster en utilisant moins de mappeurs que de conteneurs disponibles.  Les instructions de ce document supposent que votre application est la seule application en cours d’exécution sur votre cluster.      

**Étape 2 : Configurer mapreduce.map.memory/mapreduce.reduce.memory** : la taille de la mémoire pour les tâches de mappage et de réduction dépendra du travail en question.  Vous pouvez réduire la taille de la mémoire si vous souhaitez augmenter la simultanéité.  Le nombre de tâches exécutées simultanément varie selon le nombre de conteneurs.  En réduisant la quantité de mémoire par mappeur ou réducteur, il est possible de créer plus de conteneurs, pour permettre à plus de mappeurs ou réducteurs de s’exécuter simultanément.  Trop réduire la quantité de mémoire risque de causer des problèmes de mémoire insuffisante pour certains processus.  Si vous obtenez une erreur de segment de mémoire lors de l’exécution de votre travail, vous devez augmenter la mémoire par mappeur ou réducteur.  Vous devez prendre en compte le fait que l’ajout de conteneurs ajoute une charge pour chaque conteneur supplémentaire, ce qui peut dégrader les performances.  Une autre solution consiste à obtenir davantage de mémoire à l’aide d’un cluster qui possède une quantité supérieure de mémoire ou en augmentant le nombre de nœuds de votre cluster.  Une mémoire plus importante permettra d’utiliser plus de conteneurs, pour plus de simultanéité.  

**Étape 3 : Déterminer la mémoire YARN totale** : pour régler mapreduce.job.maps/mapreduce.job.reduces, vous devez prendre en compte la quantité de mémoire YARN totale disponible pour utilisation.  Ces informations sont disponibles dans Ambari.  Accédez à YARN et affichez l’onglet Configurations.  La mémoire YARN s’affiche dans cette fenêtre.  Vous devez multiplier la mémoire YARN par le nombre de nœuds dans votre cluster pour obtenir la mémoire YARN totale.

    Total YARN memory = nodes * YARN memory per node
Si vous utilisez un cluster vide, la mémoire peut être la mémoire YARN totale pour votre cluster.  Si d’autres applications utilisent de la mémoire, vous pouvez choisir d’utiliser uniquement une partie de la mémoire de votre cluster en réduisant le nombre de mappeurs ou réducteurs au nombre de conteneurs que vous souhaitez utiliser.  

**Étape 4 : Calculer le nombre de conteneurs YARN** : les conteneurs YARN déterminent la quantité de simultanéité disponible pour le travail.  Prenez la mémoire YARN totale et divisez-la par mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Étape 5 : Définir mapreduce.job.maps/mapreduce.job.reduces** Définissez mapreduce.job.maps/mapreduce.job.reduces sur le nombre de conteneurs disponibles, au minimum.  Vous pouvez expérimenter davantage en augmentant le nombre de mappeurs et de réducteurs pour voir si vous obtenez de meilleures performances.  N’oubliez pas que les mappeurs supplémentaires ajouteront une charge, ainsi un trop grand nombre de mappeurs peut dégrader les performances.  

La planification et l’isolation de processeur sont désactivées par défaut. Par conséquent, le nombre de conteneurs YARN est donc limité par la mémoire.

## <a name="example-calculation"></a>Exemple de calcul

Supposons que vous avez actuellement un cluster composé de 8 nœuds D14 et que vous souhaitez exécuter une tâche intensive en E/S.  Voici les calculs que vous devez faire :

**Étape 1 : Déterminer le nombre de travaux en cours d’exécution** : dans notre exemple, nous supposons que notre travail est le seul en cours d’exécution.  

**Étape 2 : Configurer mapreduce.map.memory/mapreduce.reduce.memory** : dans notre exemple, vous exécutez une tâche intensive en E/S et décidez que 3 Go de mémoire suffiront pour les tâches de mappage.

    mapreduce.map.memory = 3GB
**Étape 3 : Déterminer la mémoire YARN totale**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Étape 4 : Calculer le nombre de conteneurs YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Étape 5 : Définir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limites

**Limitation d’ADLS**

En tant que service mutualisé, ADLS définit des limites de bande passante de niveau de compte.  Si vous rencontrez ces limites, vous commencerez à observer des échecs pour vos tâches. Vous pouvez identifier le problème en consultant les erreurs de limitation dans les journaux des tâches.  Si vous avez besoin de davantage de bande passante pour votre travail, veuillez nous contacter.   

Pour vérifier si une limitation est appliquée, vous devez activer la journalisation du débogage côté client. Voici comment procéder :

1. Placez la propriété suivante dans les propriétés de log4j dans Ambari > YARN > Config > Avancé yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Redémarrez tous les nœuds/le service pour que la configuration prenne effet.

3. Si vous êtes limité, vous verrez le code d’erreur HTTP 429 dans le fichier journal YARN. Le fichier journal YARN se trouve dans /tmp/&lt;utilisateur&gt;/yarn.log

## <a name="examples-to-run"></a>Exemples à exécuter

Pour illustrer comment MapReduce s’exécute sur Azure Data Lake Store, voici un exemple de code qui a été exécuté sur un cluster avec les paramètres suivants :

* D14v2 16 nœuds
* Cluster Hadoop exécutant HDI 3.6

Pour commencer, voici quelques exemples de commandes pour exécuter MapReduce Teragen, Terasort et Teravalidate.  Vous pouvez ajuster ces commandes en fonction de vos ressources.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
