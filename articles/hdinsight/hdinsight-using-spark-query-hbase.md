---
title: "Utiliser Spark pour lire et écrire des données HBase - Azure HDInsight | Microsoft Docs"
description: "Utilisez le connecteur HBase Spark pour lire et écrire des données d’un cluster Spark sur un cluster HBase."
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
ms.openlocfilehash: ccbcd1d9cb45da7076d73f71a2ed692e71816650
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-spark-to-read-and-write-hbase-data"></a>Utiliser Spark pour lire et écrire des données HBase

Apache HBase est généralement interrogé soit avec son API de bas niveau (analyses, obtentions et insertions), soit avec une syntaxe SQL à l’aide de Phoenix. Apache fournit également le connecteur HBase Spark, qui constitue une alternative pratique et performante pour interroger et modifier des données stockées par HBase.

## <a name="prerequisites"></a>Prérequis

* Deux clusters HDInsight distincts, un HBase et un Spark avec Spark 2.1 (HDInsight 3.6).
* Le cluster Spark doit communiquer directement avec le cluster HBase avec une latence minimale. La configuration recommandée consiste donc à déployer les deux clusters sur le même réseau virtuel. Pour plus d’informations, consultez [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).
* Accès SSH à chaque cluster.
* Accès au stockage par défaut de chaque cluster.

## <a name="overall-process"></a>Procédure générale

La procédure à suivre pour permettre à votre cluster Spark d’interroger votre cluster HDInsight est la suivante :

1. Préparer des exemples de données dans HBase.
2. Obtenir le fichier hbase-site.xml à partir de votre dossier de configuration de cluster HBase (/etc/hbase/conf).
3. Placer une copie de hbase-site.xml dans votre dossier de configuration Spark 2 (/etc/spark2/conf).
4. Exécuter `spark-shell` en référençant le connecteur HBase Spark par ses coordonnées Maven dans l’option `packages`.
5. Définir un catalogue qui mappe le schéma de Spark vers HBase.
6. Interagir avec les données HBase à l’aide des API RDD ou DataFrame.

## <a name="prepare-sample-data-in-hbase"></a>Préparer des exemples de données dans HBase

Lors de cette étape, vous allez créer et remplir un tableau simple dans HBase, que vous pouvez ensuite interroger à l’aide de Spark.

1. Connectez-vous au nœud principal de votre cluster HBase à l’aide de SSH. Pour plus d’informations, consultez [Se connecter à HDInsight à l’aide du protocole SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
2. Exécutez l’interpréteur de commandes HBase :

        hbase shell

3. Créez une table `Contacts` avec les familles de colonnes `Personal` et `Office` :

        create 'Contacts', 'Personal', 'Office'

4. Chargez quelques exemples de lignes de données :

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

## <a name="acquire-hbase-sitexml-from-your-hbase-cluster"></a>Obtenir hbase-site.xml à partir de votre cluster HBase

1. Connectez-vous au nœud principal de votre cluster HBase à l’aide de SSH.
2. Copiez le fichier hbase-site.xml du stockage local vers la racine du stockage par défaut de votre cluster HBase :

        hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml /

3. Accédez à votre cluster HBase avec le [portail Azure](https://portal.azure.com).
4. Sélectionnez Comptes de stockage. 

    ![Comptes de stockage](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

5. Sélectionnez dans la liste le compte de stockage qui a une coche dans la colonne Par défaut.

    ![Compte de stockage par défaut](./media/hdinsight-using-spark-query-hbase/default-storage.png)

6. Dans le volet Compte de stockage, sélectionnez la vignette Objets blob.

    ![Vignette Objets blob](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

7. Dans la liste des conteneurs, sélectionnez celui qui est utilisé par votre cluster HBase.
8. Dans la liste des fichiers, sélectionnez `hbase-site.xml`.

    ![HBase-site.xml](./media/hdinsight-using-spark-query-hbase/hbase-site-xml.png)

9. Dans le panneau Propriétés de l’objet blob, sélectionnez Télécharger et enregistrez `hbase-site.xml` sur votre ordinateur local.

    ![Télécharger](./media/hdinsight-using-spark-query-hbase/download.png)

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>Placer hbase-site.xml sur votre cluster Spark

1. Accédez à votre cluster Spark à l’aide du [portail Azure](https://portal.azure.com).
2. Sélectionnez Comptes de stockage.

    ![Comptes de stockage](./media/hdinsight-using-spark-query-hbase/storage-accounts.png)

3. Sélectionnez dans la liste le compte de stockage qui a une coche dans la colonne Par défaut.

    ![Compte de stockage par défaut](./media/hdinsight-using-spark-query-hbase/default-storage.png)

4. Dans le volet Compte de stockage, sélectionnez la vignette Objets blob.

    ![Vignette Objets blob](./media/hdinsight-using-spark-query-hbase/blobs-tile.png)

5. Dans la liste des conteneurs, sélectionnez celui qui est utilisé par votre cluster Spark.
6. Sélectionnez Télécharger.

    ![Télécharger](./media/hdinsight-using-spark-query-hbase/upload.png)

7. Sélectionnez le fichier `hbase-site.xml` que vous avez précédemment téléchargé sur votre ordinateur local.

    ![Charger hbase-site.xml](./media/hdinsight-using-spark-query-hbase/upload-selection.png)

8. Sélectionnez Charger.
9. Connectez-vous au nœud principal de votre cluster Spark à l’aide de SSH.
10. Copiez `hbase-site.xml` du stockage par défaut de votre cluster Spark vers le dossier de configuration Spark 2 sur le stockage local du cluster :

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Exécuter l’interpréteur de commandes Spark en référençant le connecteur HBase Spark

1. Connectez-vous au nœud principal de votre cluster Spark à l’aide de SSH.
2. Démarrez l’interpréteur de commandes Spark en spécifiant le package de connecteur HBase Spark :

        spark-shell --packages com.hortonworks:shc-core:1.1.0-2.1-s_2.11

3. Laissez cette instance de l’interpréteur de commandes Spark ouverte et passez à l’étape suivante.

## <a name="define-a-catalog-and-query"></a>Définir un catalogue et interroger

Lors de cette étape, vous allez définir un objet de catalogue qui mappe le schéma de Spark vers HBase. 

1. Dans votre interpréteur de commandes Spark ouvert, exécutez les instructions `import` suivantes :

        import org.apache.spark.sql.{SQLContext, _}
        import org.apache.spark.sql.execution.datasources.hbase._
        import org.apache.spark.{SparkConf, SparkContext}
        import spark.sqlContext.implicits._

2. Définissez un catalogue pour la table Contacts que vous avez créée dans HBase :
    1. Définissez un schéma de catalogue pour la table HBase nommée `Contacts`.
    2. Identifiez `key` comme RowKey et mappez les noms de colonnes utilisés dans Spark à la famille de colonne, au nom de colonne et au type de colonne utilisés dans HBase.
    3. RowKey doit également être défini en détail en tant que colonne nommée (`rowkey`), qui a une famille de colonne spécifique `cf` de `rowkey`.

            def catalog = s"""{
                |"table":{"namespace":"default", "name":"Contacts"},
                |"rowkey":"key",
                |"columns":{
                |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
                |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
                |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
                |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
                |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
                |}
            |}""".stripMargin

3. Définissez une méthode qui fournit un DataFrame autour de votre table `Contacts` dans HBase :

            def withCatalog(cat: String): DataFrame = {
                spark.sqlContext
                .read
                .options(Map(HBaseTableCatalog.tableCatalog->cat))
                .format("org.apache.spark.sql.execution.datasources.hbase")
                .load()
            }

4. Créez une instance du DataFrame :

        val df = withCatalog(catalog)

5. Interrogez le DataFrame :

        df.show()

6. Vous devez voir deux lignes de données :

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. Inscrivez une table temporaire afin de pouvoir interroger la table HBase à l’aide de Spark SQL :

        df.registerTempTable("contacts")

8. Émettez une requête SQL par rapport à la table `contacts` :

        val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
        query.show()

9. Les résultats doivent ressembler à ceci :

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>Insérer de nouvelles données

1. Pour insérer un nouvel enregistrement Contact, définissez une classe `ContactRecord` :

        case class ContactRecord(
            rowkey: String,
            officeAddress: String,
            officePhone: String,
            personalName: String,
            personalPhone: String
            )

2. Créez une instance de `ContactRecord` et placez-la dans un tableau :

        val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

        var newData = new Array[ContactRecord](1)
        newData(0) = newContact

3. Enregistrez le tableau de nouvelles données dans HBase :

        sc.parallelize(newData).toDF.write
        .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
        .format("org.apache.spark.sql.execution.datasources.hbase").save()

4. Examinez les résultats :
    
        df.show()

5. Un résultat similaire à ceci s’affiche normalement :

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>Étapes suivantes

* [Connecteur HBase Spark](https://github.com/hortonworks-spark/shc)
