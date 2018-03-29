---
title: Plateformes et outils pour les projets d’équipe de science des données - Azure | Microsoft Docs
description: Détaille et décrit les données et les ressources analytiques à la disposition des entreprises souhaitant standardiser le processus de science des données pour leurs équipes.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 404e3dd106edf82f4f22e4c6a17987bd0bc51f65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>Plateformes et outils pour les projets d’équipe de science des données

Microsoft fournit une gamme complète de services et ressources de données et d’analytique pour les plateformes cloud ou locales. Vous pouvez déployer ces services et ressources pour faciliter et faire évoluer l’exécution de vos projets de science des données. [Team Data Science Process](overview.md) (TDSP) fournit aux équipes des conseils pour implémenter les projets de science des données de façon collaborative, en garantissant la traçabilité et la gestion des versions.  Pour obtenir une description des rôles des membres de l’équipe de science des données et des tâches qui leur incombent dans le cadre de ce processus, consultez [Rôles et tâches du processus TDSP](roles-tasks.md).

Les équipes de science des données qui utilisent le processus TDSP ont accès aux services de données et d’analytique suivants :

- Instances Data Science Virtual Machine (Windows et CentOS Linux)
- Clusters HDInsight Spark
- SQL Data Warehouse
- Azure Data Lake
- Clusters HDInsight Hive
- Stockage Fichier Azure
- SQL Server 2016 R Services

Dans ce document, nous allons brièvement décrire les ressources disponibles, en indiquant les liens vers les didacticiels et les procédures pas à pas fournis par les équipes TDSP. Ces didacticiels et procédures pas à pas vous aideront à apprendre à utiliser les ressources étape par étape et à commencer à les utiliser pour créer des applications intelligentes. Vous trouverez des informations complètes sur ces ressources dans les pages produit correspondantes. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

L’instance DSVM fournie sur Windows et Linux par Microsoft contient des outils couramment utilisés dans le cadre des activités de modélisation et de développement de la science des données. Il s’agit notamment des outils suivants :

- Microsoft R Server Developer Edition 
- Distribution Anaconda Python
- Notebooks Jupyter pour Python et R 
- Visual Studio Community Edition avec les outils Python et R sur Windows / Eclipse sur Linux
- Power BI Desktop pour Windows
- SQL Server 2016 Developer Edition sur Windows / Postgres sur Linux

Des **outils ML et IA**, tels que CNTK (kit de ressources d’apprentissage profond open source de Microsoft), xgboost, mxnet et Vowpal Wabbit sont également fournis.

La machine virtuelle DSVM est disponible sur les systèmes d’exploitation **Windows** et **CentOS Linux**. Déterminez la taille de votre machine virtuelle DSVM (nombre de cœurs de processeur et quantité de mémoire) en fonction des exigences des projets de science des données que vous prévoyez d’exécuter sur cette instance. 

Pour plus d’informations sur l’édition Windows de DSVM, consultez [Microsoft Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) sur la Place de marché Azure. Pour l’édition Linux de DSVM, consultez [Linux Data Science Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Pour découvrir comment effectuer rapidement certaines tâches courantes de science des données sur la machine virtuelle DSVM, consultez [Dix tâches possibles sur une machine virtuelle DSVM](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Clusters Azure HDInsight Spark

Apache Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de Big Data. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans l’apprentissage automatique et les calculs de graphes. Spark est également compatible avec le Stockage Blob Azure (WASB), ce qui vous permet d’utiliser Spark pour traiter facilement vos données existantes stockées dans Azure.

Quand vous créez un cluster Spark dans HDInsight, vous créez des ressources de calcul Azure avec Spark installé et configuré. La création d’un cluster Spark dans HDInsight prend 10 minutes environ. Stockez les données à traiter dans le Stockage Blob Azure. Pour obtenir des informations sur l’utilisation du Stockage Blob Azure avec un cluster, consultez [Utiliser le Stockage Blob Azure compatible HDFS avec Hadoop dans HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

L’équipe TDSP de Microsoft a publié deux procédures pas à pas qui expliquent comment utiliser des clusters Azure HDInsight Spark pour créer deux solutions de science des données (l’une avec Python, l’autre avec Scala). Pour plus d’informations sur les **clusters Azure HDInsight Spark**, consultez [Présentation d’Apache Spark sur HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Pour découvrir comment créer une solution de science des données à l’aide de **Python** sur un cluster Azure HDInsight Spark, consultez [Vue d’ensemble de la science des données à l’aide de Spark sur Azure HDInsight](spark-overview.md). Pour découvrir comment créer une solution de science des données à l’aide de **Scala** sur un cluster Azure HDInsight Spark, consultez [Science des données à l’aide de Scala et Spark sur Azure](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL Data Warehouse vous permet de mettre à l’échelle vos ressources de calcul facilement et en quelques secondes, sans surprovisionnement ni surfacturation. Ce service offre également la possibilité de suspendre l’utilisation des ressources de calcul, ce qui vous permet de mieux gérer les coûts associés au cloud. La possibilité de déployer des ressources de calcul évolutives rend possible le stockage de toutes vos données dans Azure SQL Data Warehouse. Les coûts de stockage sont faibles, et vous pouvez exécuter le calcul uniquement sur les parties des jeux de données que vous souhaitez analyser. 

Pour plus d’informations sur Azure SQL Data Warehouse, consultez le site web [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse). Pour découvrir comment créer des solutions analytiques avancées complètes avec SQL Data Warehouse, consultez [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Data Warehouse](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake est un référentiel d’entreprise pour tous les types de données collectées dans un emplacement unique, avant l’application imposée de spécifications ou schémas formels. Grâce à cette flexibilité, toutes les données peuvent être conservées dans un lac de données (data lake), indépendamment de leur taille, de leur structure ou de leur vitesse d’ingestion. Les organisations utilisent ensuite Hadoop ou une analytique avancée pour identifier des modèles dans ces lacs de données. Les lacs de données peuvent également servir de référentiel où les données sont préparées à moindre coût avant d’être traitées et déplacées vers un entrepôt de données.

Pour plus d’informations sur Azure Data Lake, consultez [Présentation d’Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Pour découvrir comment créer une solution de science des données complète et évolutive avec Azure Data Lake, consultez [Science des données scalable avec Azure Data Lake : procédure complète](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Clusters Azure HDInsight Hive (Hadoop)

Apache Hive est un système d’entrepôt de données pour Hadoop qui permet de totaliser, d’interroger et d’analyser des données à l’aide du langage de requête HiveQL, un langage similaire à SQL. Hive peut être utilisé pour explorer vos données de manière interactive ou pour créer des tâches de traitement par lots réutilisables.

Hive vous permet de concevoir une structure sur des données largement non structurées. Une fois que vous avez défini la structure, vous pouvez utiliser Hive pour interroger ces données dans un cluster Hadoop, sans avoir besoin d’utiliser, ni même de connaître, Java ou MapReduce. HiveQL (le langage de requête Hive) vous permet d’écrire des requêtes avec des instructions semblables à T-SQL.

Les chercheurs de données peuvent exécuter des fonctions Python définies par l’utilisateur dans des requêtes Hive pour traiter les enregistrements. Cela améliore considérablement les performances des requêtes Hive dans l’analyse des données. En particulier, cela permet aux chercheurs de données d’effectuer une ingénierie des caractéristiques évolutive dans les principaux langages qu’ils connaissent, à savoir Python et le langage HiveQL similaire à SQL. 

Pour plus d’informations sur les clusters Azure HDInsight Hive, consultez [Utiliser Hive et HiveQL avec Hadoop dans HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Pour découvrir comment créer une solution de science des données complète et évolutive à l’aide des clusters Azure HDInsight Hive Clusters, consultez [Processus TDSP (Team Data Science Process) en action : utiliser des clusters Hadoop Azure HDInsight](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Stockage Fichier Azure 

Le Stockage Fichier Azure est un service qui propose des partages de fichiers dans le cloud en utilisant le protocole SMB (Server Message Block) standard. Les protocoles SMB 2.1 et SMB 3.0 sont pris en charge. Avec le stockage de fichiers Azure, vous pouvez migrer vers Azure des applications héritées qui s’appuient sur des partages de fichiers, rapidement et sans réécritures onéreuses. Les applications s’exécutant sur des machines virtuelles Azure, dans des services cloud ou à partir de clients locaux peuvent monter un partage de fichiers dans le cloud, tout comme une application de bureau monte un partage SMB standard. Un nombre illimité de composants d’application peuvent ensuite monter un partage de stockage de fichiers et y accéder simultanément.

Le fait de pouvoir créer un stockage de fichiers Azure est particulièrement utile pour les projets de science des données, car vous pouvez utiliser cet emplacement pour partager des données de projet avec les membres de votre équipe. Tous les membres ont ainsi accès à la même copie des données dans le stockage de fichiers Azure. Ils peuvent également se servir de ce stockage de fichiers pour y partager des ensembles de caractéristiques générés pendant l’exécution du projet. Si le projet est un engagement client, votre client peut créer un stockage de fichiers Azure sous son propre abonnement Azure et l’utiliser pour partager les données et caractéristiques du projet avec vous. De cette façon, le client garde un contrôle total sur les ressources de données du projet. Pour plus d’informations sur le Stockage Fichier Azure, consultez [Bien démarrer avec le Stockage Fichier Azure sur Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) et [Utiliser le Stockage Fichier Azure avec Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (en base de données) fournit une plateforme pour développer et déployer des applications intelligentes capables de découvrir de nouveaux insights. Pour créer des modèles et générer des prédictions à partir de vos données SQL Server, utilisez le langage R riche et puissant, avec les nombreux packages fournis par la Communauté R. Étant donné que R Services (en base de données) intègre le langage R avec SQL Server, l’analytique reste proche des données, ce qui évite les coûts et les risques de sécurité associés au déplacement des données.

R Services (en base de données) prend en charge le langage R open source avec un ensemble complet d’outils et de technologies SQL Server. Ils améliorent la gestion, la sécurité, la fiabilité et les performances. Vous pouvez déployer des solutions R à l’aide d’outils pratiques et familiers. Vos applications de production peuvent appeler le Runtime R, et récupérer des prédictions et des visuels à l’aide de Transact-SQL. Vous pouvez également utiliser les bibliothèques ScaleR pour rendre vos solutions R plus performantes et évolutives. Pour plus d’informations, consultez [SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx)

L’équipe TDSP de Microsoft a publié deux procédures pas à pas qui expliquent comment créer deux solutions de science des données dans SQL Server 2016 R Services (l’une pour les programmeurs R et l’autre pour les développeurs SQL). Pour les **programmeurs R**, consultez [Procédure pas à pas complète de science des données](https://msdn.microsoft.com/library/mt612857.aspx). Pour les **développeurs SQL**, consultez [Analytique avancée en base de données pour les développeurs SQL (didacticiel)](https://msdn.microsoft.com/library/mt683480.aspx).


## <a name="appendix"></a>Annexe : Outils pour configurer des projets de science des données

### <a name="install-git-credential-manager-on-windows"></a>Installer Git Credential Manager sur Windows

Si vous suivez le processus TDSP sur **Windows**, vous devez installer **Git Credential Manager (GCM)** pour permettre la communication avec les dépôts Git. Pour installer GCM, installez d’abord **Chocolaty**. Pour installer Chocolaty et GCM, exécutez les commandes suivantes dans Windows PowerShell en tant qu’**administrateur** :  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>Installer Git sur les machines Linux (CentOS)

Pour installer Git sur les machines Linux (CentOS), exécutez la commande bash suivante :

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Générer une clé SSH publique sur les machines Linux (CentOS)

Si vous exécutez les commandes Git sur des machines Linux (CentOS), vous devez ajouter la clé SSH publique de votre machine sur votre serveur VSTS pour que celui-ci reconnaisse la machine. Vous devez d’abord générer une clé SSH publique, puis l’ajouter aux clés publiques SSH dans la page de vos paramètres de sécurité VSTS. 

- Pour générer la clé SSH, exécutez les deux commandes suivantes : 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Copiez l’intégralité de la clé ssh, y compris *ssh-rsa*. 
- Connectez-vous à votre serveur VSTS. 
- Cliquez sur **<Votre nom\>** en haut à droite de la page et cliquez sur **Sécurité**. 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- Cliquez sur **Clés publiques SSH**, puis cliquez sur **+ Ajouter**. 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- Collez la clé ssh que vous venez de copier dans la zone de texte et enregistrez-la.


## <a name="next-steps"></a>Étapes suivantes

Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Exemples de procédures pas à pas](walkthroughs.md) les répertorie et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 

Pour obtenir des exemples d’exécution des étapes du processus TDSP qui utilisent Azure Machine Learning Studio, consultez le parcours d’apprentissage [Avec Azure ML](http://aka.ms/datascienceprocess).