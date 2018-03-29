---
title: Analyser et traiter des documents JSON avec Apache Hive dans Azure HDInsight | Microsoft Docs
description: Découvrir comment utiliser et analyser des documents JSON avec Apache Hive dans Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 04c3a8262e52a630012a0a70e4b1ccb0ade76449
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Traiter et analyser des documents JSON avec Apache Hive dans Azure HDInsight

Découvrez comment traiter et analyser des fichiers JSON (JavaScript Object Notation) avec Apache Hive dans Azure HDInsight. Ce didacticiel utilise le document JSON suivant :

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Vous trouverez le fichier à l’adresse **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Pour plus d’informations sur l’utilisation du stockage Blob Azure avec HDInsight, consultez [Utiliser le stockage Blob Azure compatible HDFS avec Hadoop dans HDInsight](../hdinsight-hadoop-use-blob-storage.md). Vous pouvez copier le fichier dans le conteneur par défaut de votre cluster.

Dans ce tutoriel, vous allez utiliser la console Hive. Pour obtenir des instructions sur l’ouverture de la console Hive, consultez [Utiliser Hive avec Hadoop sur HDInsight via le Bureau à distance](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Aplatir des documents JSON
Pour les méthodes listées dans la section suivante, le document JSON doit se composer d’une seule ligne. Par conséquent, vous devez aplatir le document JSON pour le réduire à une chaîne. Si votre document JSON est déjà aplati, vous pouvez ignorer cette étape et passer directement à la section suivante sur l’analyse des données JSON. Pour aplatir le document JSON, exécutez le script suivant :

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Le fichier JSON brut se trouve à l’emplacement suivant : **wasb://processjson@hditutorialdata.blob.core.windows.net/**. La table Hive **StudentsRaw** pointe vers le document JSON brut qui n’est pas aplati.

La table Hive **StudentsOneLine** stocke les données dans le système de fichiers HDInsight par défaut sous le chemin d’accès **/json/students/**.

L’instruction **INSERT** remplit la table **StudentOneLine** avec les données JSON aplaties.

L’instruction **SELECT** retourne une seule ligne.

Voici la sortie de l’instruction **SELECT** :

![Aplatir le document JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analyser les documents JSON dans Hive
Hive propose trois mécanismes différents pour exécuter des requêtes sur des documents JSON, mais vous pouvez aussi écrire les vôtres :

* Utiliser la fonction UDF get_json_object.
* Utiliser la fonction UDF json_tuple.
* Utiliser le sérialiseur/désérialiseur personnalisé (SerDe).
* Vous pouvez écrire votre propre fonction définie par l’utilisateur (UDF) avec Python ou d’autres langages. Pour plus d’informations sur l’exécution de votre propre code Python avec Hive, consultez [Utiliser des fonctions définies par l’utilisateur (UDF) Python avec Apache Hive et Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Utiliser la fonction définie par l’utilisateur get_json_object
Hive intègre une fonction UDF appelée [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) qui permet d’exécuter des requêtes sur un document JSON pendant l’exécution. Cette méthode accepte deux arguments : d’une part, le nom de la table et le nom de la méthode contenant le document JSON aplati et d’autre part, le champ JSON à analyser. Prenons un exemple pour examiner de plus près cette fonction UDF.

La requête suivante retourne le prénom et le nom de chaque élève :

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Voici la sortie quand vous exécutez cette requête dans la fenêtre de la console :

![Fonction UDF get_json_object][image-hdi-hivejson-getjsonobject]

La fonction UDF get_json_object présente des limitations :

* Étant donné que chaque champ de la requête implique une nouvelle analyse de la requête, cela affecte les performances.
* **GET\_JSON_OBJECT()** retourne une représentation sous forme de chaîne d’un tableau. Pour convertir cette dernière en tableau Hive, vous devez utiliser des expressions régulières pour remplacer les crochets « [ » et « ] », puis appeler split pour obtenir le tableau.

C’est pourquoi le wiki Hive vous recommande d’utiliser json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utiliser la fonction UDF json_tuple
L’autre fonction UDF fournie par Hive, intitulée [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), est plus performante que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode, qui accepte un ensemble de clés et une chaîne JSON, retourne un tuple de valeurs en utilisant une seule fonction. La requête suivante retourne l’ID et la classe de l’étudiant du document JSON :

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Sortie de ce script dans la console Hive :

![Fonction UDF json_tuple][image-hdi-hivejson-jsontuple]

La fonction UDF json_tuple utilise la syntaxe [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans Hive, ce qui permet à json\_tuple de créer une table virtuelle en appliquant la fonction UDT à chaque ligne de la table d’origine. Les documents JSON complexes deviennent trop lourds en raison de l’utilisation répétée de **LATERAL VIEW**. De plus, **JSON_TUPLE** ne peut pas gérer les documents JSON imbriqués.

### <a name="use-a-custom-serde"></a>Utiliser un SerDe personnalisé
SerDe constitue le meilleur moyen d’analyser des documents JSON imbriqués. Il vous permet de définir le schéma JSON qui vous servira à analyser les documents. Pour obtenir des instructions, consultez [Guide pratique pour utiliser un SerDe JSON personnalisé avec Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Résumé
En conclusion, le type d'opérateur JSON que vous choisissez dans Hive dépend de votre scénario. Si vous avez un document JSON simple avec un seul champ à rechercher, vous pouvez choisir d’utiliser la fonction UDF Hive get_json_object. Si la recherche porte sur plusieurs clés, vous pouvez utiliser json_tuple. Enfin, si vous disposez d'un document imbriqué, il est recommandé d'utiliser le SerDe JSON.

## <a name="next-steps"></a>Étapes suivantes

Articles associés :

* [Utilisation de Hive et HiveQL avec Hadoop dans HDInsight pour l’analyse d’un exemple de fichier Apache log4j](../hdinsight-use-hive.md)
* [Analyse des données sur les retards de vol avec Hive dans HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analyser des données Twitter avec Hive dans HDInsight](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

