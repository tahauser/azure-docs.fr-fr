---
title: Utiliser les vues Ambari pour travailler avec Hive sur HDInsight (Hadoop) - Azure | Documents Microsoft
description: "Découvrez comment utiliser la vue Hive à partir de votre navigateur web pour envoyer des requêtes Hive. La vue Hive fait partie de l’interface utilisateur web Ambari fournie avec votre cluster HDInsight sous Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 5f66e60249af489e695029cbb072f3cc881bb039
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Utiliser la vue Ambari Hive avec Hadoop dans HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Découvrez comment exécuter des requêtes Hive avec la vue Ambari Hive. Ambari est un utilitaire de gestion et de surveillance fourni avec les clusters HDInsight sous Linux. L’une des fonctionnalités offertes par Ambari est une interface utilisateur web qui peut être utilisée pour exécuter des requêtes Hive.

> [!NOTE]
> Ambari offre de nombreuses fonctionnalités qui ne sont pas traitées dans ce document. Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](../hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>configuration requise

* Un cluster HDInsight sous Linux Pour obtenir des informations sur la création de clusters, consultez [Bien démarrer avec Hadoop dans HDInsight](apache-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster Azure HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Ouvrir l’affichage Hive

Vous pouvez ouvrir les vues Ambari à partir du portail Azure. Sélectionnez votre cluster HDInsight, puis sélectionnez **Vues Ambari** dans la section **Liens rapides**.

![Section des liens rapides du portail](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

Dans la liste des vues, sélectionnez __Affichage Hive__.

![L’affichage Hive sélectionné](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> En accédant à Ambari, vous êtes invité à vous authentifier sur le site. Indiquez le nom du compte et le mot de passe de l’administrateur (`admin` par défaut) que vous avez utilisés lorsque vous avez créé le cluster.

Une page similaire à celle ci-dessous doit s’afficher :

![Image de la feuille de calcul de requête pour l’affichage Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>Exécution d’une requête

Pour exécuter une requête Hive, utilisez les étapes suivantes à partir de l’affichage Hive.

1. Dans l’onglet __Requête__ , collez les instructions HiveQL suivantes dans la feuille de calcul :

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

   * `DROP TABLE` : supprime la table et le fichier de données, si la table existe déjà.

   * `CREATE EXTERNAL TABLE` : crée une table « externe » dans Hive.
   Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

   * `ROW FORMAT` : montre la mise en forme des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

   * `STORED AS TEXTFILE LOCATION` : montre où sont stockées les données, et qu’elles sont stockées sous forme de texte.

   * `SELECT` : sélectionne toutes les lignes où la colonne t4 contient la valeur [ERROR].

     > [!NOTE]
     > Utilisez des tables externes lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, tel qu’un processus de chargement de données automatisé ou une autre opération MapReduce. La suppression d'une table externe ne supprime *pas* les données, mais seulement la définition de la table.

    > [!IMPORTANT]
    > Conservez la sélection __Base de données__ par __défaut__. Les exemples de ce document utilisent la base de données par défaut incluse avec HDInsight.

2. Pour démarrer la requête, utilisez le bouton **Exécuter** situé sous la feuille de calcul. Ce bouton devient orange et le texte affiche **Stop** (Arrêter).

3. Lorsque la requête est terminée, l’onglet **Results** (Résultats) affiche les résultats de l’opération. Le texte suivant est le résultat de la requête :

        sev       cnt
        [ERROR]   3

    Vous pouvez utiliser l’onglet **Logs** (Journaux) pour afficher les informations de journalisation que la tâche a créées.

   > [!TIP]
   > Téléchargez ou enregistrez les résultats à partir de la boîte de dialogue déroulante **Save results** (Enregistrer les résultats), dans le coin supérieur gauche de la section **Query Process Results** (Résultats du processus de requête).

4. Sélectionnez les quatre premières lignes de cette requête, puis sélectionnez **Execute** (Exécuter). Notez qu’aucun résultat n’est retourné à la fin de la tâche. Si vous utilisez le bouton **Exécuter** alors que vous avez sélectionné une partie de la requête, seules les instructions sélectionnées sont exécutées. Dans ce cas, la sélection n’inclut pas l’instruction finale qui consiste à extraire des lignes de la table. Si vous sélectionnez uniquement cette ligne et que vous utilisez le bouton **Exécuter**, vous devriez obtenir les résultats attendus.

5. Pour ajouter une feuille de calcul, utilisez le bouton **Nouvelle feuille de calcul** au bas de l’ **Éditeur de requêtes**. Dans la nouvelle feuille de calcul, entrez les instructions HiveQL suivantes :

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Ces instructions effectuent les opérations suivantes :

   * **CREATE TABLE IF NOT EXISTS** : crée une table s’il n’en existe pas déjà une. Étant donné que le mot clé **EXTERNAL** n’est pas utilisé, une table interne est créée. Une table interne est stockée dans l’entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

   * **STORED AS ORC**: stocke les données au format ORC (Optimized Row Columnar). ORC est un format particulièrement efficace et optimisé pour le stockage de données Hive.

   * **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent `[ERROR]`, puis insère les données dans la table **errorLogs**.

Utilisez le bouton **Exécuter** pour exécuter cette requête. L’onglet **Résultats** ne contient pas d’informations lorsque la requête ne retourne aucune ligne. L’état doit être **SUCCEEDED** lorsque la requête est terminée.

### <a name="visual-explain"></a>Visual Explain

Pour afficher une visualisation du plan de requête, sélectionnez l’onglet **Visual Explain** au-dessous de la feuille de calcul.

La vue **Visual Explain** de la requête peut être utile pour comprendre le déroulement de requêtes complexes. Vous pouvez afficher un équivalent textuel de cette vue à l’aide du bouton **Explain** de l’éditeur de requête.

### <a name="tez-ui"></a>Interface utilisateur Tez

Sélectionnez l’onglet **Tez** au-dessous de la feuille de calcul afin d’afficher l’interface utilisateur Tez pour la requête.

> [!IMPORTANT]
> Tez n’est pas utilisé pour résoudre toutes les requêtes. Vous pouvez résoudre de nombreuses requêtes sans utiliser Tez. 

Si Tez a été utilisé pour résoudre la requête, le graphe orienté acyclique (DAG) s’affiche. Si vous souhaitez afficher le DAG pour les requêtes que vous avez exécutées par le passé, ou déboguer le processus Tez, utilisez plutôt la [vue Tez](../hdinsight-debug-ambari-tez-view.md).

## <a name="view-job-history"></a>Afficher l’historique des tâches

L’onglet __Travaux__ affiche un historique des requêtes Hive.

![Image de l’historique des travaux](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tables de base de données

Vous pouvez utiliser l’onglet __Tables__ onglet pour travailler avec des tables dans une base de données Hive.

![Image de l’onglet tables](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Requêtes enregistrées

À partir de l’onglet **Query** (Requête), vous pouvez éventuellement enregistrer des requêtes. Après avoir enregistré une requête, vous pouvez la réutiliser à partir de l’onglet __Saved Queries__ (Requêtes enregistrées).

![Image de l’onglet de requêtes enregistrées](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Vous pouvez également étendre Hive par l’intermédiaire de fonctions définies par l’utilisateur (UDF). Utilisez une fonction définie par l’utilisateur pour implémenter une fonctionnalité ou une logique qui n’est pas facilement modélisée en HiveQL.

Déclarez et enregistrez un ensemble de fonctions définies par l’utilisateur en utilisant l’onglet **UDF** en haut de l’affichage Hive. Ces UDF peuvent être utilisés avec les **l’éditeur de requête**.

![Image de l’onglet UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Lorsque vous avez ajouté une fonction définie par l’utilisateur à l’affichage Hive, un bouton **Insert udfs** (Insérer des fonctions définies par l’utilisateur) s’affiche au bas de **Query Editor** (l’éditeur de requête). La sélection de cette entrée affiche une liste déroulante des UDF définis dans l’affichage Hive. La sélection d’une fonction UDF ajoute des instructions HiveQL à votre requête pour activer l’UDF.

Par exemple, si vous avez défini une fonction UDF avec les propriétés suivantes :

* Nom de ressource : myudfs

* Chemin d’accès à la ressource : /myudfs.jar

* Nom de la fonction UDF : myawesomeudf

* Nom de la classe UDF : com.myudfs.Awesome

L’utilisation du bouton **Insert udfs** (Insérer des fonctions définies par l’utilisateur) affiche une entrée nommée **myudfs** avec une autre liste déroulante pour chaque fonction définie par l’utilisateur qui est paramétrée pour cette ressource. Dans le cas présent, il s’agit de **myawesomeudf**. La sélection de cette entrée ajoute le code suivant au début de la requête :

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Vous pouvez ensuite utiliser la fonction UDF dans votre requête. Par exemple : `SELECT myawesomeudf(name) FROM people;`.

Pour plus d’informations sur l’utilisation des fonctions définies par l’utilisateur avec Hive sur HDInsight, consultez les articles suivants :

* [Utilisation de Python avec Hive et Pig dans HDInsight](python-udf-hdinsight.md)
* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Paramètres Hive

Vous pouvez modifier différents paramètres Hive, par exemple pour passer du moteur d’exécution Tez (par défaut) à MapReduce pour Hive.

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
