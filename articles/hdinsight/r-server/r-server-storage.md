---
title: "Solutions de stockage Azure pour R Server sur HDInsight - Azure | Microsoft Docs"
description: "Découvrez les différentes options de stockage disponibles pour les utilisateurs avec R Server sur HDInsight"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 863277294fc0462e9221edffab1dd4e2001d7493
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Solutions de stockage Azure pour R Server sur HDInsight

Microsoft R Server sur HDInsight possède différentes solutions de stockage permettant de conserver les données, le code ou les objets qui contiennent des résultats d’analyse. Il s’agit notamment des options suivantes :

- [Blob Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Stockage Fichier Azure](https://azure.microsoft.com/services/storage/files/)

Vous avez également la possibilité d’accéder à plusieurs conteneurs ou comptes de stockage Azure avec votre cluster HDI. Le Stockage Fichier Azure est une option de stockage de données pratique, que l’on peut utiliser sur le nœud périphérique pour monter d’un partage de fichiers de stockage Azure sur, par exemple, le système de fichiers Linux. Mais les partages de fichiers Azure peuvent être montés et utilisés par tous les systèmes qui disposent d’un système d’exploitation pris en charge, par exemple Windows ou Linux. 

Lors de la création d’un cluster Hadoop dans HDInsight, vous spécifiez un compte de **stockage Azure** ou bien un **magasin Data Lake**. Un conteneur de stockage spécifique de ce compte contient le système de fichiers du cluster créé (par exemple, le système de fichiers DFS Hadoop). Pour plus d’informations et pour obtenir de l’aide, consultez les pages :

- [Utiliser le stockage Azure avec HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Utiliser Data Lake Store avec des clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md) 

Pour plus d’informations sur les solutions de stockage Azure, consultez la page [Introduction au Stockage Microsoft Azure](../../storage/common/storage-introduction.md). 

Pour obtenir de l’aide sur la sélection de l’option de stockage la plus adaptée à votre scénario, consultez la page [Choisir quand utiliser des objets Blob Azure, des fichiers Azure ou des disques de données Azure](../../storage/common/storage-decide-blobs-files-disks.md) 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Utiliser des comptes de stockage Blob Azure avec R Server

Si vous avez spécifié plusieurs comptes de stockage durant la création de votre cluster R Server, les instructions suivantes expliquent comment utiliser un compte secondaire pour l’accès aux données et les opérations liées à celles-ci sur R Server. Imaginons les éléments suivants : un compte de stockage **storage1**, un conteneur par défaut appelé **container1** et un compte de stockage **storage2**.

> [!WARNING]
> Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données que le compte de stockage principal que vous spécifiez. L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

1. À l’aide d’un client SSH, connectez-vous au nœud de périphérie de votre cluster en tant que remoteuser.  

  + Dans Portail Azure > Page de service de cluster HDI > Vue d’ensemble, cliquez sur **Secure Shell (SSH)**.
  + Dans Nom d’hôte, sélectionnez le nœud de périphérie (le nom contient *ed-ssh.azurehdinsight.net*).
  + Copiez le nom d’hôte.
  + Ouvrez un client SSH tel que PutTY ou SmartTY et entrez le nom d’hôte.
  + Entrez remoteuser comme nom d’utilisateur, suivi du mot de passe du cluster.
  
2. Copiez le fichier mycsv.csv dans le répertoire /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

3. Dans R Studio ou une autre console R, écrivez le code R pour nommer le nœud **default** et définir l’emplacement du fichier auquel vous souhaitez accéder.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Toutes les références de fichiers et de répertoires pointent vers le compte de stockage wasb://container1@storage1.blob.core.windows.net. Il s’agit du **compte de stockage par défaut** associé au cluster HDInsight.

Supposons maintenant que vous souhaitiez traiter un fichier appelé mySpecial.csv qui se trouve dans le répertoire /private du conteneur **container2** dans le compte de stockage **storage2**.

Dans votre code R, pointez la référence du nœud de nom vers le compte de stockage **storage2** .


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Toutes les références de fichier et de répertoire pointent désormais vers le compte de stockage wasb://container2@storage2.blob.core.windows.net. Il s’agit du **nom de nœud** que vous avez spécifié.

Vous devez configurer ainsi le répertoire /user/RevoShare/<SSH username> sur **storage2** :


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Utiliser un magasin Azure Data Lake avec R Server

Pour utiliser des magasins Data Lake avec votre compte HDInsight, vous devez permettre à votre cluster d’accéder à tous les magasins Azure Data Lake que vous souhaitez utiliser. Pour obtenir des instructions sur l’utilisation du Portail Azure pour créer un cluster HDInsight avec un compte Azure Data Lake Store comme stockage par défaut ou comme magasin supplémentaire, consultez la page [Créer un cluster HDInsight avec Data Lake Store à l’aide du Portail Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Vous utilisez alors le magasin de votre script R à peu près de la même manière qu’un compte de stockage Azure secondaire, comme le décrit la procédure précédente.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Ajouter un accès de cluster à vos magasins Azure Data Lake
Vous accédez à un magasin Data Lake en utilisant un principal du service Azure Active Directory (Azure AD) associé à votre cluster HDInsight.

Pour ajouter un principal de service Azure AD :

1. Lorsque vous créez votre cluster HDInsight, sélectionnez **Identité AAD de cluster** à partir de l’onglet **Source de données**.

2. Dans la boîte de dialogue **Identité AAD de cluster**, sous **Sélectionner un principal de service AD**, sélectionnez **Créer**.

Après avoir attribué un nom et un mot de passe au principal du service, cliquez sur **Gérer l’accès ADLS** pour l’associer à vos magasins Data Lake.

Il est également possible d’ajouter un accès à un ou plusieurs magasins Data Lake pour le cluster après sa création. Ouvrez l’entrée d’un magasin Data Lake sur le Portail Azure et accédez à **Explorateur de données > Accès > Ajouter**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Comment accéder au magasin Data Lake à partir de R Server

Après avoir créé un accès au magasin Data Lake, vous pouvez utiliser le magasin dans R Server sur HDInsight de la même façon qu’un compte de stockage Azure secondaire. La seule différence est que le préfixe **wasb://** devient **adl://**, comme suit :


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Les commandes suivantes sont utilisées pour configurer le compte de stockage Data Lake avec le répertoire RevoShare et pour ajouter l’exemple de fichier .csv de l’exemple précédent :


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Utiliser le Stockage Fichier Azure avec R Server

Il existe également une option de stockage de données pratique qui peut s’utiliser sur le nœud de périphérie ; elle se nomme [Fichiers Azure]((https://azure.microsoft.com/services/storage/files/). Elle vous permet de monter un partage de fichiers Azure Storage sur le système de fichiers Linux. Cette option peut être utile pour stocker des fichiers de données, des scripts R et des objets de résultats qui pourront se révéler nécessaires par la suite, lorsqu’il sera judicieux d’utiliser le système de fichiers natif sur le nœud de périphérie plutôt que HDFS. 

Le principal avantage des fichiers Azure est que les partages de fichiers peuvent être montés et utilisés par tout système disposant d’un système d’exploitation pris en charge, tel que Windows ou Linux. Par exemple, ils peuvent être utilisés par un autre cluster HDInsight que vous ou un membre de votre équipe avez, par une machine virtuelle Azure ou même par un système local. Pour plus d'informations, consultez les pages suivantes :

- [Guide pratique pour utiliser le Stockage Fichier Azure avec Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Guide pratique pour utiliser le Stockage Fichier Azure sous Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous maîtrisez les options de stockage Azure, utilisez les liens suivants pour découvrir différents moyens d’effectuer des tâches de science des données avec R Server sur HDInsight.

* [Vue d’ensemble : R Server sur HDInsight (version préliminaire)](r-server-overview.md)
* [Prise en main de R Server sur Hadoop](r-server-get-started.md)
* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](r-server-compute-contexts.md)

