---
title: Solutions de stockage Azure pour R Server sur HDInsight - Azure | Microsoft Docs
description: Découvrez les différentes options de stockage disponibles avec R Server sur HDInsight
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: f0333252e404c52f123da916df01d198234ef5af
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Solutions de stockage Azure pour R Server sur Azure HDInsight

R Server sur HDInsight comprend différentes solutions de stockage permettant de conserver les données, le code ou les objets qui contiennent des résultats d’analyse. Il s’agit notamment des options suivantes :

- [Blob Azure](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)
- [Stockage Fichier Azure](https://azure.microsoft.com/services/storage/files/)

Vous avez également la possibilité d’accéder à plusieurs conteneurs ou comptes de stockage Azure avec votre cluster HDInsight. Le Stockage Fichier Azure est une option de stockage de données pratique, que l’on peut utiliser sur le nœud périphérique pour monter d’un partage de fichiers de stockage Azure sur, par exemple, le système de fichiers Linux. Mais les partages de fichiers Azure peuvent être montés et utilisés par tous les systèmes qui fonctionnent sur un système d’exploitation pris en charge, par exemple, Windows ou Linux. 

Lors de la création d’un cluster Hadoop dans HDInsight, on peut spécifier un compte de **stockage Azure** ou un **magasin Data Lake**. Un conteneur de stockage spécifique de ce compte contient le système de fichiers du cluster créé (par exemple, le système de fichiers DFS Hadoop). Pour plus d’informations et pour obtenir de l’aide, consultez les pages :

- [Utiliser le stockage Azure avec HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Utiliser Data Lake Store avec des clusters Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Utiliser des comptes de stockage Blob Azure avec un cluster R Server

Si vous avez spécifié plusieurs comptes de stockage durant la création de votre cluster R Server, les instructions suivantes expliquent comment utiliser un compte secondaire pour l’accès aux données et les opérations liées à celles-ci sur un cluster R Server. Imaginons les éléments suivants : un compte de stockage **storage1** avec un conteneur par défaut appelé **container1** et un compte de stockage **storage2** avec un conteneur **container2**.

> [!WARNING]
> Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données que le compte de stockage principal que vous spécifiez. L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Utiliser le stockage par défaut avec R Server sur HDInsight

1. À l’aide d’un client SSH, connectez-vous au nœud de périphérie de votre cluster. Pour en savoir plus sur l’utilisation de SSH avec des clusters HDInsight, consultez la page [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copiez un exemple de fichier, mysamplefile.csv, dans le répertoire /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

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
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Toutes les références de fichiers et de répertoires pointent vers le compte de stockage `wasb://container1@storage1.blob.core.windows.net`. Il s’agit du **compte de stockage par défaut** associé au cluster HDInsight.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Utiliser le stockage supplémentaire avec R Server sur HDInsight

Supposons maintenant que vous souhaitiez traiter un fichier appelé mysamplefile1.csv, qui se trouve dans le répertoire /private du conteneur **container2**, dans le compte de stockage **storage2**.

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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Toutes les références de fichier et de répertoire pointent désormais vers le compte de stockage `wasb://container2@storage2.blob.core.windows.net`. Il s’agit du **nom de nœud** que vous avez spécifié.

Vous devez configurer ainsi le répertoire /user/RevoShare/<SSH username> sur **storage2** :


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Utiliser Azure Data Lake Store avec un cluster R Server 

Pour pouvoir utiliser Data Lake Store avec votre cluster HDInsight, vous devez permettre à ce dernier d’accéder à tous les magasins Azure Data Lake Store que vous souhaitez utiliser. Pour obtenir des instructions sur l’utilisation du Portail Azure pour créer un cluster HDInsight avec un compte Azure Data Lake Store comme stockage par défaut ou comme magasin supplémentaire, consultez la page [Créer un cluster HDInsight avec Data Lake Store à l’aide du Portail Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Vous utilisez alors le magasin de votre script R à peu près de la même manière qu’un compte de stockage Azure secondaire, comme le décrit la procédure précédente.

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Ajouter un accès de cluster à vos magasins Azure Data Lake
Vous accédez à un magasin Data Lake en utilisant un principal du service Azure Active Directory (Azure AD) associé à votre cluster HDInsight.

1. Lorsque vous créez votre cluster HDInsight, sélectionnez **Identité AAD de cluster** à partir de l’onglet **Source de données**.

2. Dans la boîte de dialogue **Identité AAD de cluster**, sous **Sélectionner un principal de service AD**, sélectionnez **Créer**.

Après avoir attribué un nom et un mot de passe au principal du service, cliquez sur **Gérer l’accès à ADLS** pour l’associer à votre magasin Data Lake Store.

Il est également possible d’ajouter un accès à un ou plusieurs comptes Data Lake Store pour le cluster après sa création. Ouvrez l’entrée d’un magasin Data Lake Store sur le Portail Azure et accédez à **Explorateur de données > Accès > Ajouter**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Accéder au magasin Data Lake à partir de R Server sur HDInsight

Maintenant que vous avez créé un accès au magasin Data Lake Store, vous pouvez utiliser ce dernier dans un cluster R Server sur HDInsight de la même façon qu’un compte de stockage Azure secondaire. La seule différence est que le préfixe **wasb://** devient **adl://**, comme suit :


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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Les commandes suivantes permettent de configurer le compte Data Lake Store avec le répertoire RevoShare et d’ajouter l’exemple de fichier .csv de l’exemple précédent :


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Utiliser le Stockage Fichier Azure avec R Server sur HDInsight

Il existe également une option de stockage de données pratique pour le nœud de périmètre, nommée [Azure Files]((https://azure.microsoft.com/services/storage/files/). Elle vous permet de monter un partage de fichiers Azure Storage sur le système de fichiers Linux. Cette option peut être utile pour stocker des fichiers de données, des scripts R et des objets de résultats qui pourront se révéler nécessaires par la suite, lorsqu’il sera judicieux d’utiliser le système de fichiers natif sur le nœud périphérique plutôt que HDFS. 

Le principal avantage des fichiers Azure est que les partages de fichiers peuvent être montés et utilisés par tout système disposant d’un système d’exploitation pris en charge, tel que Windows ou Linux. Par exemple, ils peuvent être utilisés par un autre cluster HDInsight que vous ou un membre de votre équipe avez, par une machine virtuelle Azure ou même par un système local. Pour plus d'informations, consultez les pages suivantes :

- [Guide pratique pour utiliser le Stockage Fichier Azure avec Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Guide pratique pour utiliser le Stockage Fichier Azure sous Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble du cluster R Server sur HDInsight](r-server-overview.md)
* [Bien démarrer avec un cluster R Server sur Hadoop](r-server-get-started.md)
* [Options de contexte de calcul d’un cluster R Server sur HDInsight](r-server-compute-contexts.md)

