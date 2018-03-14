---
title: Utiliser DataFu avec Pig dans HDInsight - Azure | Documents Microsoft
description: "DataFu est une collection de bibliothèques à utiliser avec Hadoop. Découvrez comment vous pouvez utiliser DataFu avec Pig sur votre cluster HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: d9aaef778f08500f45b6d1260f2a5d301fabeba8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Utilisation de DataFu avec Pig dans HDInsight

Découvrez comment utiliser DataFu avec HDInsight. DataFu est un ensemble de bibliothèques Open Source à utiliser avec Pig sur Hadoop.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure.

* Un cluster Azure HDInsight (Linux ou Windows)

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Des connaissances élémentaire de [l'utilisation de Pig dans HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Installation de DataFu dans HDInsight basé sur Linux

> [!IMPORTANT]
> DataFu est installé sur les clusters basés sur Linux 3.3 et versions ultérieures, ainsi que sur les clusters Windows. Il n’est pas installé sur les clusters Linux avant la version 3.3.
>
> Si vous utilisez un cluster basé sur Windows ou un cluster basé sur une version Linux ultérieure à la version 3.3, ignorez cette section.

DataFu peut être téléchargé et installé à partir du référentiel Maven. Procédez comme suit pour ajouter DataFu à votre cluster HDInsight :

1. Connectez-vous à votre cluster HDInsight Linux en utilisant SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour télécharger le fichier jar DataFu à l'aide de l'utilitaire wget, ou copiez et collez le lien dans votre navigateur pour commencer le téléchargement.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. Ensuite, chargez le fichier du stockage par défaut pour votre cluster HDInsight. Placer le fichier dans un stockage par défaut le rend accessible à tous les nœuds du cluster.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > La commande ci-dessus stocke le fichier jar dans `/example/jars`, car ce répertoire existe déjà sur le stockage en cluster. Vous pouvez utiliser n'importe quel emplacement dans le stockage en cluster HDInsight.

## <a name="use-datafu-with-pig"></a>Utilisation de DataFu avec Pig

Les étapes de cette section supposent que vous êtes familiarisé avec l’utilisation de Pig sur HDInsight. Pour plus d'informations sur l'utilisation de Pig avec HDInsight, consultez la rubrique [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Si vous avez installé DataFu manuellement en suivant les étapes de la section précédente, vous devez l’enregistrer avant de l’utiliser.
>
> * Si votre cluster utilise le stockage Azure, utilisez un chemin d’accès `wasb://`. Par exemple : `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> * Si votre cluster utilise Azure Data Lake Store, utilisez un chemin d’accès `adl://`. Par exemple : `register adl://home/example/jars/datafu-1.2.0.jar`.

Vous définissez souvent un alias pour les fonctions DataFu. L’exemple suivant définit un alias de `SHA` :

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Vous pouvez ensuite utiliser cet alias dans un script Pig Latin pour générer un hachage pour les données d’entrée. Par exemple, le code suivant remplace l’emplacement dans les données d’entrée avec une valeur de hachage :

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Voici la sortie qu’il génère :

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur DataFu ou Pig, consultez les documents suivants :

* [Guide Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
