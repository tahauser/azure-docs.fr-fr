---
title: Options de contexte de calcul pour R Server sur HDInsight - Azure | Microsoft Docs
description: Découvrez les différentes options de contexte de calcul disponibles pour les utilisateurs avec R Server sur HDInsight
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: 7d10f58c345eff334f40c0ec64d7b9427d6a70e0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)

Microsoft R Server sur Azure HDInsight contrôle le mode d’exécution des appels en définissant le contexte de calcul. Cet article présente les options disponibles pour spécifier si l’exécution est mise en parallèle, et comment, sur les différents cœurs du nœud périphérique ou du cluster HDInsight.

Le nœud de périmètre d’un cluster fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud périphérique permet d’exécuter des fonctions distribuées parallélisées de RevoScaleR sur les différents cœurs du serveur associé. Vous pouvez également les exécuter sur les différents nœuds du cluster à l’aide des contextes de calcul Hadoop Map Reduce ou Spark de RevoScaleR.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R Server dans Azure HDInsight
[Microsoft R Server sur Azure HDInsight](r-server-overview.md) propose les dernières fonctionnalités d’analyse sur R. Il peut utiliser les données stockées dans un conteneur HDFS de votre compte de stockage [Blob Azure](../../storage/common/storage-introduction.md "stockage Blob Azure"), un magasin Data Lake ou le système de fichiers Linux local. Dans la mesure où R Server repose sur la version open source de R, les applications R créées peuvent appliquer n’importe quels packages R open source parmi plus de 8 000. Elles peuvent également utiliser les routines de [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), le package d’analyse du Big Data de Microsoft fourni avec R Server.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextes de calcul pour un nœud de périmètre
En général, un script R exécuté dans R Server sur le nœud de périmètre s’exécute au sein de l’interpréteur R sur ce nœud. L’exception concerne ces étapes qui appellent une fonction RevoScaleR. Les appels RevoScaleR s’exécutent dans un environnement de calcul déterminé par la façon dont est défini le contexte de calcul RevoScaleR.  Lorsque le script R est exécuté à partir d’un nœud périphérique, les valeurs possibles du contexte de calcul sont les suivantes :

- local séquentiel (*local*)
- local parallèle (*localpar*)
- Map Reduce
- Spark

Les options *local* et *localpar* diffèrent uniquement par la façon dont les appels **rxExec** sont exécutés. Elles exécutent toutes les deux d’autres appels de fonction rx de manière parallèle entre tous les cœurs disponibles, sauf indication contraire par le biais de l’option **numCoresToUse** RevoScaleR, par exemple, `rxOptions(numCoresToUse=6)`. Les options d’exécution parallèle offrent des performances optimales.

Le tableau suivant récapitule les différentes options de contexte de calcul permettant de définir le mode d’exécution des appels :

| Contexte de calcul  | Définition                      | Contexte d’exécution                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Local sequential | rxSetComputeContext('local')    | Exécution parallélisée sur les différents cœurs du serveur de nœud périphérique, à l’exception des appels rxExec qui sont exécutés en série |
| Local parallel   | rxSetComputeContext('localpar') | Exécution parallélisée sur les cœurs du serveur de nœud de périmètre |
| Spark            | RxSpark()                       | Exécution distribuée parallélisée via Spark sur les nœuds du cluster HDI |
| Map Reduce       | RxHadoopMR()                    | Exécution distribuée parallélisée via Map Reduce sur les nœuds du cluster HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Conseils pour le choix d’un contexte de calcul

L’option à choisir parmi les trois qui offrent l’exécution parallélisée dépend de la nature du travail d’analyse, de la taille et de l’emplacement des données. Il n’existe aucune formule simple indiquant quel contexte de calcul utiliser. Toutefois, certains principes directeurs peuvent vous aider à faire le bon choix ou, au moins, à affiner vos choix avant d’effectuer un test d’évaluation. Ces principes sont les suivants :

- Le système de fichiers Linux local est plus rapide que HDFS.
- Les analyses répétées sont plus rapides si les données sont locales et au format XDF.
- Il est préférable de transmettre de petites quantités de données à partir d’une source de données textuelles. Si les volumes sont supérieurs, convertissez-les en XDF avant analyse.
- La surcharge de copie ou de diffusion des données vers le nœud de périmètre pour l’analyse devient ingérable pour les quantités de données très importantes.
- Spark est plus rapide que Map Reduce pour l’analyse dans Hadoop.

Compte tenu de ces principes, la section suivante propose quelques règles générales pour sélectionner un contexte de calcul.

### <a name="local"></a>Local
* Si les données à analyser sont peu volumineuses et ne nécessitent pas d’analyses répétées, transmettez-les directement à la routine d’analyse avec *local* ou *localpar*.
* Si les données à analyser sont peu ou moyennement volumineuses et nécessitent des analyses répétées, copiez-les dans le système de fichiers local, importez-les au format XDF et analysez-les avec *local* ou *localpar*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Si les données à analyser sont volumineuses, importez-les dans un DataFrame Spark à l’aide de **RxHiveData** ou de **RxParquetData**, ou au format XDF dans HDFS (sauf si le stockage est problématique), puis analysez-les selon le contexte de calcul Spark.

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* N’utilisez le contexte de calcul MapReduce que si vous rencontrez un problème insurmontable avec le contexte de calcul Spark, car il est en général plus lent.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Aide en ligne sur rxSetComputeContext
Pour obtenir plus d’informations et des exemples de contextes de calcul RevoScaleR, consultez l’aide en ligne dans R sur la méthode rxSetComputeContext, par exemple :

    > ?rxSetComputeContext

Vous pouvez également consulter la section [Distributed computing overview](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) (Vue d’ensemble de l’informatique distribuée) dans la [documentation Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert les options disponibles pour spécifier si l’exécution est mise en parallèle, et comment, sur les différents cœurs du nœud périphérique ou du cluster HDInsight. Pour plus d’informations sur l’utilisation de R Server avec des clusters HDInsight, consultez les rubriques suivantes :

* [Vue d’ensemble : R Server sur HDInsight (version préliminaire)](r-server-overview.md)
* [Commencer à utiliser R Server sur HDInsight (version préliminaire)](r-server-get-started.md)
* [Options d’Azure Storage pour R Server sur HDInsight](r-server-storage.md)

