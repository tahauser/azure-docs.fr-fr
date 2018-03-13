---
title: "Présentation de R Server sur Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser R Server sur HDInsight pour créer des applications pour l’analyse du Big Data."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 860099b2634765f3088199c89341f0b48b20b801
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introduction à R Server et aux fonctionnalités Open Source R sur HDInsight

Microsoft R Server est disponible en tant qu’option de déploiement lors de la création de clusters HDInsight dans Azure. Cette nouvelle fonctionnalité offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse extensibles et distribuées sur HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Les clusters peuvent être dimensionnés en fonction des projets et des tâches en cours, puis détruits lorsqu’ils ne sont plus nécessaires. Étant donné qu’ils font partie d’Azure HDInsight, ils bénéficient d’un support technique de niveau entreprise 24 heures sur 24, 7 jours sur 7, d’un contrat SLA garantissant 99,9 % de temps de fonctionnement et d’une capacité d’intégration avec d’autres composants de l’écosystème Azure.

R Server sur HDInsight fournit les dernières fonctionnalités d’analyse R sur des jeux de données de toutes tailles ou presque, chargés dans un stockage de type Blob Azure ou Data Lake. Puisque R Server repose sur une version R open source, les applications basées sur R que vous créez peuvent tirer parti des plus de 8 000 packages R open source. Les routines de ScaleR, le package d’analyse du Big Data de Microsoft fourni avec R Server, sont également disponibles.

Le nœud de périmètre d’un cluster fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R. Un nœud périphérique permet d’exécuter des fonctions distribuées parallélisées de ScaleR sur les différents cœurs du serveur associé. Vous pouvez également les exécuter sur les différents nœuds du cluster à l’aide des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR.

Les modèles ou prévisions résultant des analyses peuvent être téléchargés pour une utilisation locale. Ils peuvent également être mis en œuvre ailleurs dans Azure, en particulier par le biais du [service web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Prise en main de R sur HDInsight
Pour inclure R Server dans un cluster HDInsight, vous devez sélectionner le type de cluster R Server lors de la création d’un cluster HDInsight via le portail Azure. Le type de cluster R Server inclut R Server sur les nœuds de données du cluster et sur un nœud périphérique, qui sert de zone d’accueil pour l’analyse R Server. Pour une description détaillée de la création du cluster, consultez la page [Bien démarrer avec R Server sur HDInsight](r-server-get-started.md).

## <a name="learn-about-data-storage-options"></a>En savoir plus sur les options de stockage de données
Le stockage par défaut du système de fichiers HDFS de clusters HDInsight peut être associé à un compte de stockage Azure ou bien à un magasin Azure Data Lake. Cette association garantit que toutes les données chargées dans l’espace de stockage en cluster au cours de l’analyse sont rendues persistantes. Il existe différents outils permettant de gérer le transfert de données vers l’option de stockage sélectionnée, notamment la fonctionnalité de chargement sur le portail du compte de stockage et l’utilitaire [AzCopy](../../storage/common/storage-use-azcopy.md).

Vous avez la possibilité d’ajouter un accès à des objets Blob et des magasins Data Lake supplémentaires pendant le processus d’approvisionnement du cluster, quelle que soit l’option de stockage principal utilisée. Consultez la page [Bien démarrer avec R Server sur HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) pour plus d’informations sur l’ajout d’accès à des comptes supplémentaires. Consultez l’article supplémentaire [Options de stockage Azure pour R Server sur HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) pour plus d’informations sur l’utilisation de plusieurs comptes de stockage.

Vous pouvez également utiliser [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) comme option de stockage pour une utilisation sur le nœud de périphérie. Le service Azure Files vous permet de monter un partage de fichiers qui a été créé dans un compte Azure Storage sur le système de fichiers Linux. Pour plus d’informations concernant ces options de stockage de données pour R Server sur un cluster HDInsight, consultez la page [Options de stockage Azure pour R Server sur des clusters HDInsight](r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Accéder à R Server sur le cluster
Vous pouvez vous connecter à R Server sur le nœud de périphérie à l’aide d’un navigateur. R Server est installé par défaut lors de la création du cluster. Pour plus d’informations, consultez [Commencer à utiliser R Server sur HDInsight](r-server-get-started.md).

Vous pouvez également vous connecter à R Server à l’aide de la ligne de commande, en utilisant SSH/PuTTY pour accéder à la console R. 

## <a name="develop-and-run-r-scripts"></a>Développer et exécuter des scripts R
Les scripts R que vous créez et exécutez peuvent utiliser n’importe quels packages R open source parmi plus de 8 000, en plus des routines distribuées et parallélisées de la bibliothèque ScaleR. En général, un script exécuté avec R Server sur le nœud périphérique s’exécute au sein de l’interpréteur R sur ce nœud, à l’exception des étapes qui doivent appeler une fonction ScaleR avec un contexte de calcul défini sur Hadoop MapReduce (RxHadoopMR) ou Spark (RxSpark). Dans ce cas, la fonction s’exécute en mode distribué sur les différents nœuds de données (tâches) du cluster qui sont associés aux données référencées. Pour plus d’informations concernant les différentes options de contexte de calcul, voir [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Faire fonctionner un modèle
Lorsque la modélisation de vos données est terminée, vous pouvez mettre en œuvre le modèle afin d’effectuer des prévisions pour de nouvelles données sur Azure et localement. Ce processus est appelé notation. La notation est possible dans HDInsight, dans Azure Machine Learning ou en local.

### <a name="score-in-hdinsight"></a>Noter dans HDInsight
Pour noter dans HDInsight, écrivez une fonction R qui appelle votre modèle pour effectuer des prévisions pour un nouveau fichier de données que vous avez chargé sur votre compte de stockage. Ensuite, enregistrez les prévisions dans le compte de stockage. Vous pouvez exécuter la routine à la demande sur le nœud de périmètre de votre cluster ou à l’aide d’une tâche planifiée.  

### <a name="score-in-azure-machine-learning-aml"></a>Noter dans Azure Machine Learning (AML)
Pour noter à l’aide d’un service web AML, utilisez le package R Azure Machine Learning open source nommé [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) pour publier votre modèle en tant que service web Azure. Pour plus de commodité, ce package est déjà installé sur le nœud de périphérie. Ensuite, utilisez les fonctionnalités d’Azure Machine Learning pour créer une interface utilisateur pour le service web, puis appelez le service web en fonction des besoins de notation.

Si vous choisissez cette option, il vous faut convertir tous les objets de modèle ScaleR en objets de modèle open source équivalents pour les utiliser avec le service web. Utilisez les fonctions de forçage de ScaleR, notamment `as.randomForest()` dans le cas des modèles basés sur un ensemble, pour cette conversion.

### <a name="score-on-premises"></a>Noter localement
Pour noter localement après la création de votre modèle, vous pouvez sérialiser le modèle dans R, le télécharger, le désérialiser, puis l’utiliser pour noter de nouvelles données. Vous pouvez noter les nouvelles données à l’aide de l’approche décrite précédemment dans [Noter dans HDInsight](#scoring-in-hdinsight) ou en utilisant [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Maintenance du cluster
### <a name="install-and-maintain-r-packages"></a>Installation et maintenance des packages R
La plupart des packages R utilisés seront requis sur le nœud périphérique, car la plupart des étapes des scripts R y sont exécutées. Pour installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser la méthode `install.packages()` habituelle dans R.

Si vous utilisez simplement des routines de la bibliothèque ScaleR au sein du cluster, il n’est généralement pas nécessaire d’installer des packages R supplémentaires sur les nœuds de données. Toutefois, il se peut que vous ayez besoin de packages supplémentaires pour prendre en charge l’utilisation de **rxExec** ou l’exécution de **RxDataStep** sur les nœuds de données.

Dans ce cas, les packages supplémentaires peuvent être installés à l’aide d’une action de script après la création du cluster. Pour plus d’informations, consultez la section [Création d’un cluster HDInsight avec R Server](r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modifier les paramètres de mémoire de Hadoop MapReduce
Vous pouvez modifier un cluster afin de changer la quantité de mémoire disponible pour R Server lors de l’exécution d’un travail MapReduce. Pour modifier un cluster, utilisez l’interface utilisateur Apache Ambari qui est disponible via le panneau du portail Azure pour votre cluster. Pour obtenir des instructions concernant l’accès à l’interface utilisateur d’Ambari pour votre cluster, consultez la section [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](../hdinsight-hadoop-manage-ambari.md).

Vous pouvez également modifier la quantité de mémoire disponible pour R Server en utilisant des commutateurs Hadoop dans l’appel à **RxHadoopMR** comme suit :

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Mettre à l’échelle votre cluster
Vous pouvez modifier l’échelle d’un cluster existant via le portail. Grâce à la mise à l’échelle, vous pouvez obtenir la capacité supplémentaire éventuellement nécessaire pour les tâches de traitement plus importantes, ou rétablir l’échelle d’un cluster quand il est inactif. Pour obtenir des instructions concernant la mise à l’échelle d’un cluster, consultez la section [Gérer des clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Maintenance du système
La maintenance, pour appliquer des correctifs de système d’exploitation ainsi que d’autres mises à jour, est effectuée sur les machines virtuelles Linux sous-jacentes dans un cluster HDInsight pendant les heures creuses. En règle générale, la maintenance s’effectue à 3 h 30 (en fonction de l’heure locale de la machine virtuelle) tous les lundis et jeudis. Les mises à jour sont effectuées de façon à ne pas avoir d’incidence sur plus d’un quart du cluster à la fois.  

Étant donné que les nœuds principaux sont redondants et que certains nœuds de données ne sont pas affectés, toutes les tâches en cours d’exécution pendant ce temps peuvent être ralenties. Elles doivent cependant être exécutées jusqu’à la fin. L’ensemble des logiciels personnalisés ou des données locales que vous détenez sont conservés durant ces événements de maintenance, sauf si une défaillance irrémédiable se produit, nécessitant une reconstruction du cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>En savoir plus sur les options IDE pour R Server sur un cluster HDInsight
Le nœud de périmètre Linux d’un cluster HDInsight est la zone d’accueil pour l’analyse basée sur R. Les versions récentes de HDInsight permettent une installation par défaut de RStudio Server sur le nœud de périphérie en tant qu’IDE basé sur navigateur. L'utilisation de RStudio Server comme IDE pour le développement et l’exécution de scripts R peuvent être considérablement plus productifs que la simple utilisation de la console R.

Une autre option d’IDE complet consiste à installer un IDE de bureau et à l’utiliser pour accéder au cluster au moyen d’un contexte de calcul MapReduce ou Spark distant. Parmi les options disponibles figurent les [Outils R pour Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) de Microsoft, RStudio et [StatET](http://www.walware.de/goto/statet) sur Eclipse de WalWare.

Enfin, vous pouvez accéder à la console R Server sur le nœud périphérique en tapant **R** à l’invite de commande Linux après vous être connecté via SSH ou PuTTY. Si vous utilisez l’interface de la console, il est pratique d’exécuter un éditeur de texte pour le développement de scripts R dans une autre fenêtre, et de couper-coller des sections de votre script dans la console R au fur et à mesure.

## <a name="learn-about-pricing"></a>En savoir plus sur la tarification
Les frais associés à un cluster HDInsight avec R Server sont structurés de manière similaire à ceux applicables à des clusters HDInsight standard. Ils sont basés sur le dimensionnement des machines virtuelles sous-jacentes pour le nom, les données et les nœuds de périmètre, avec l’ajout d’une extension pendant les heures normales. Pour plus d’informations sur la tarification de HDInsight, consultez la section [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de R Server avec des clusters HDInsight, consultez les rubriques suivantes :

* [Prise en main de R Server sur HDInsight](r-server-get-started.md)
* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](r-server-storage.md)
