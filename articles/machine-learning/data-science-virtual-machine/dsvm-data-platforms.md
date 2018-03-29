---
title: Plateformes de données pour la machine virtuelle DSVM - Azure | Microsoft Docs
description: Plateformes de données pour la machine virtuelle DSVM.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
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
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: c9c85ebed6382a4188db028fbfb35675751bce76
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="data-platforms"></a>Plateformes de données

La machine virtuelle DSVM (Data Science Virtual Machine) vous permet de générer votre analytique sur un large éventail de plateformes de données. En plus des interfaces sur les plateformes de données distantes, la machine virtuelle DSVM fournit une instance locale pour le développement et le prototypage rapides. 

Les outils de plateforme de données pris en charge sur la machine virtuelle DSVM sont les suivants. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance de base de données relationnelle locale      |
| Éditions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Développement rapide localement avec le plus petit jeu de données <br/> Exécution de R en base de données   |
| Liens vers des exemples      |    Un petit échantillon du jeu de données New York City est chargé dans la base de données SQL `nyctaxi`. <br/> Vous trouverez un exemple Jupyter illustrant l’analytique Microsoft R et en base de données à l’emplacement suivant :<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Outils connexes sur la machine virtuelle DSVM       | SQL Server Management Studio <br/> Pilotes ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> L’édition développeur de SQL Server 2016 peut uniquement être utilisée à des fins de test et de développement. Vous avez besoin d’une licence ou de l’une des machines virtuelles SQL Server pour l’exécuter en production. 


### <a name="setup"></a>Paramétrage

Le serveur de base de données est déjà préconfiguré et les services Windows associés à SQL Server (comme `SQL Server (MSSQLSERVER)`) sont définis pour s’exécuter automatiquement. La seule étape manuelle à exécuter consiste à activer l’analytique en base de données à l’aide de Microsoft R. Vous pouvez pour cela exécuter la commande suivante dans SQL Server Management Studio (SSMS) après vous être connecté  en tant qu’administrateur de l’ordinateur, ouvrir une « Nouvelle requête » dans SSMS, vérifier que la base de données sélectionnée est `master`, puis exécuter : 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Pour exécuter SQL Server Management Studio, vous pouvez rechercher « SQL Server Management Studio » dans la liste des programmes ou utiliser la Recherche Windows pour le rechercher et l’exécuter. Quand vous êtes invité à fournir des informations d’identification, choisissez « Authentification Windows » et utilisez le nom d’ordinateur ou ```localhost``` dans Nom du serveur SQL. 

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?  

Le serveur de base de données avec l’instance de base de données par défaut s’exécute automatiquement par défaut. Vous pouvez utiliser des outils tels que SQL Server Management Studio sur la machine virtuelle pour accéder à la base de données SQL Server localement. Les comptes Administrateurs local ont un accès administrateur à la base de données. 

De plus, la machine virtuelle DSVM est fournie avec des pilotes ODBC et JDBC pour communiquer avec SQL Server, les bases de données SQL Azure et Azure SQL Data Warehouse à partir d’applications écrites dans plusieurs langages, notamment Python et R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Comment est-il configuré / installé sur la machine virtuelle DSVM ? 

Le serveur SQL est installé de manière standard. Il se trouve dans `C:\Program Files\Microsoft SQL Server`. L’instance de R en base de données se trouve dans `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. DSVM a également une instance de R Server autonome installée dans `C:\Program Files\Microsoft\R Server\R_SERVER`. Ces deux instances de R ne partagent pas les bibliothèques.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonome)

| | |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Une instance autonome (nœud unique in-process) de la plateforme populaire Apache Spark, un système pour le traitement des données et l’apprentissage automatique rapides et à grande échelle     |
| Éditions DSVM prises en charge      | Linux <br /> Windows (Expérimental)      |
| Utilisations classiques      | * Développement rapide d’applications Spark/PySpark localement avec un jeu de données plus petit et un déploiement ultérieur sur des clusters Spark volumineux tels qu’Azure HDInsight<br/> * Test du contexte Spark Microsoft R Server <br />* Utiliser la bibliothèque [MMLSpark](https://github.com/Azure/mmlspark) Open Source de SparkML ou de Microsoft pour créer des applications ML  |
| Liens vers des exemples      |    Exemple Jupyter : <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (contexte Spark) : /dsvm/samples/MRS/MRSSparkContextSample.R |
| Outils connexes sur la machine virtuelle DSVM       | PySpark, Scala<br/>Jupyter (noyaux Spark/PySpark)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Comment l’utiliser ?
Vous pouvez exécuter Spark en soumettant des travaux Spark sur la ligne de commande avec les commandes `spark-submit` ou `pyspark`. Vous pouvez également créer un bloc-notes Jupyter en créant un bloc-notes avec le noyau Spark. 

Vous pouvez utiliser Spark à partir de R à l’aide de bibliothèques telles que SparkR, Sparklyr ou Microsoft R Server qui sont disponibles sur la machine virtuelle DSVM. Consultez les pointeurs vers les exemples dans le tableau précédent. 

> [!NOTE]
> L’exécution de Microsoft R Server dans le contexte Spark de la machine virtuelle DSVM est prise en charge uniquement sur l’édition DSVM Ubuntu Linux. 



### <a name="setup"></a>Paramétrage
Avant toute exécution dans le contexte Spark dans Microsoft R Server sur l’édition DSVM Ubuntu Linux, vous devez effectuer une opération de configuration unique pour activer une instance Yarn et HDFS Hadoop à nœud unique locale. Par défaut, les services Hadoop sont installés mais désactivés sur la DSVM. Pour les activer, vous devez exécuter les commandes suivantes en tant que racine la première fois :

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Vous pouvez arrêter les services liés à Hadoop quand vous n’en avez pas besoin en exécutant ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````. Vous trouverez dans le répertoire `/dsvm/samples/MRS` un exemple montrant comment développer et tester MRS dans un contexte Spark distant (l’instance Spark autonome sur la machine virtuelle DSVM). 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Comment est-il configuré / installé sur la machine virtuelle DSVM ? 
|Plateforme|Emplacement d’installation ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Les bibliothèques pour l’accès aux données à partir du Stockage Blob Azure ou Azure Data Lake Storage (ADLS) et l’utilisation des bibliothèques d’apprentissage automatique MMLSpark de Microsoft sont préinstallées dans $SPARK_HOME/jars. Ces fichiers JAR sont automatiquement chargés au démarrage de Spark. Par défaut, Spark utilise des données sur le disque local. Pour que l’instance de Spark sur la machine virtuelle DSVM puisse accéder aux données stockées dans le Stockage Blob Azure ou dans ADLS, vous devez créer/configurer le fichier `core-site.xml` basé sur le modèle qui se trouve dans $SPARK_HOME/conf/core-site.xml.template (où il existe des espaces réservés pour les configurations du Stockage Blob Azure et ADLS) avec les informations d’identification appropriées pour le Stockage Blob Azure et Azure Data Lake Storage. Vous trouverez des étapes de création d’informations d’identification de service ADLS plus détaillées [ici](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Une fois que les informations d’identification pour le Stockage Blob Azure ou ADLS ont été entrées dans le fichier core-site.XML, vous pouvez référencer les données stockées dans ces sources, avec le préfixe d’URI wasb:// ou adl://. 

