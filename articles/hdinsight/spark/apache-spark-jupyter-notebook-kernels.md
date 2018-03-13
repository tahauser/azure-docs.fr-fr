---
title: Noyaux pour bloc-notes Jupyter sur les clusters Spark dans Azure HDInsight | Documents Microsoft
description: "Découvrez les noyaux PySpark, PySpark3 et Spark pour bloc-notes Jupyter qui sont disponibles avec les clusters Spark sur Azure HDInsight."
keywords: bloc-notes jupyter sur spark,jupyter spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0719e503-ee6d-41ac-b37e-3d77db8b121b
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: nitinme
ms.openlocfilehash: 87e60bcc097157c733c1e08356b7cd9ea48bb868
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="kernels-for-jupyter-notebook-on-spark-clusters-in-azure-hdinsight"></a>Noyaux pour bloc-notes Jupyter sur les clusters Spark dans Azure HDInsight 

Les clusters Spark HDInsight fournissent des noyaux que vous pouvez utiliser avec le bloc-notes Jupyter sur Spark pour tester vos applications. Un noyau est un programme qui exécute et interprète votre code. Les trois noyaux sont les suivants :

- **PySpark** : pour les applications écrites en Python2
- **PySpark3** : pour les applications écrites en Python3
- **Spark** : pour les applications écrites en Scala

Dans cet article, vous allez apprendre à utiliser ces noyaux et découvrir les avantages de leur utilisation.

## <a name="prerequisites"></a>Prérequis

* Un cluster Apache Spark dans HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Créer un bloc-notes Jupyter sur Spark HDInsight

1. À partir du [portail Azure](https://portal.azure.com/), ouvrez votre cluster.  Pour obtenir des instructions, consultez la page [Énumération et affichage des clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters). Le cluster est ouvert dans un nouveau panneau du portail.

2. À partir de la section **Liens rapides** , cliquez sur **Tableaux de bord des clusters** pour ouvrir le panneau **Tableaux de bord des clusters**.  Si vous ne voyez pas **Liens rapides**, cliquez sur **Vue d’ensemble** dans le menu gauche du panneau.

    ![Bloc-notes Jupyter sur Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Bloc-notes Jupyter sur Spark") 

3. Cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.
   
   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter sur le cluster Spark en ouvrant l’URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

3. Cliquez sur **Nouveau**, puis sur **Pyspark**, **PySpark3** ou **Spark** pour créer un bloc-notes. Utilisez le noyau Spark pour les applications Scala, le noyau PySpark pour les applications Python2 et le noyau PySpark3 pour les applications Python3.
   
    ![Noyaux pour bloc-notes Jupyter sur Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Noyaux pour bloc-notes Jupyter sur Spark") 

4. Un bloc-notes s’ouvre avec le noyau que vous avez sélectionné.

## <a name="benefits-of-using-the-kernels"></a>Avantages de l’utilisation des noyaux

Voici quelques avantages liés à l’utilisation des nouveaux noyaux avec bloc-notes Jupyter sur des clusters Spark HDInsight.

- **Contextes prédéfinis**. Avec les noyaux **PySpark**, **PySpark3** ou **Spark**, vous n’avez pas besoin de définir les contextes Spark ou Hive explicitement avant de commencer à utiliser vos applications. Ils sont disponibles par défaut. Ces contextes sont les suivants :
   
   * **sc** : pour le contexte Spark
   * **sqlContext** : pour le contexte Hive
   
   Par conséquent, vous n’avez pas à exécuter d’instructions telles que les suivantes pour définir les contextes :
   
          sc = SparkContext('yarn-client')
          sqlContext = HiveContext(sc)
   
   En revanche, vous pouvez utiliser directement les contextes prédéfinis dans votre application.

- **Commandes magiques de cellule**. Le noyau PySpark fournit certaines « commandes magiques » prédéfinies, qui sont des commandes spéciales que vous pouvez appeler avec `%%` (par exemple, `%%MAGIC` <args>). La commande magique doit se trouver au tout début d’une cellule de code et autoriser plusieurs lignes de contenu. Le mot magic doit être le premier mot de la cellule. Ajouter quoi que ce soit avant la commande magique, même des commentaires, provoque une erreur.     Pour plus d’informations sur les commandes magiques, cliquez [ici](http://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    Le tableau suivant répertorie les différentes commandes magiques disponibles par le biais des noyaux.

   | Commande magique | exemples | Description |
   | --- | --- | --- |
   | help |`%%help` |Génère une table de toutes les commandes magiques disponibles, accompagnées d’un exemple et d’une description |
   | info |`%%info` |Génère des informations de session pour le point de terminaison Livy actuel |
   | CONFIGURER |`%%configure -f`<br>`{"executorMemory": "1000M"`<br>`"executorCores": 4`} |Configure les paramètres de création d’une session. L’indicateur de forçage (-f) est obligatoire si une session a déjà été créée, afin de garantir que la session est supprimée et recréée. Consultez la section [POST /sessions Request Body de Livy](https://github.com/cloudera/livy#request-body) pour obtenir la liste des paramètres valides. Les paramètres doivent être passés en tant que chaîne JSON et être spécifiés sur la ligne suivant la commande magique, comme indiqué dans l’exemple de colonne. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Exécute une requête Hive sur sqlContext. Si le paramètre `-o` est passé, le résultat de la requête est conservé dans le contexte Python %%local en tant que trame de données [Pandas](http://pandas.pydata.org/) . |
   | local |`%%local`<br>`a=1` |Tout le code dans les lignes suivantes est exécuté localement. Le code doit être un code Python2 valide, même s’il ne correspond pas au noyau que vous utilisez. Donc, même si vous avez sélectionné les noyaux **PySpark3** ou **Spark** en créant le bloc-notes, si vous utilisez la commande magique `%%local` dans une cellule, cette cellule doit uniquement comporter un code Python2 valide. |
   | journaux |`%%logs` |Génère les journaux de la session Livy en cours. |
   | delete |`%%delete -f -s <session number>` |Supprime une session spécifique du point de terminaison Livy actuel. Vous ne pouvez pas supprimer la session qui est lancée pour le noyau. |
   | cleanup |`%%cleanup -f` |Supprime toutes les sessions pour le point de terminaison Livy actuel, y compris la session de ce bloc-notes. L’indicateur de forçage -f est obligatoire. |

   > [!NOTE]
   > Outre les commandes magiques ajoutées par le noyau PySpark, vous pouvez également utiliser les [commandes magiques IPython intégrées](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), notamment `%%sh`. Vous pouvez utiliser la commande magique `%%sh` pour exécuter des scripts et des blocs de code sur le nœud principal du cluster.
   >
   >
2. **Visualisation automatique**. Le noyau **Pyspark** visualise automatiquement la sortie des requêtes Hive et SQL. Vous pouvez choisir entre plusieurs types de visualisations, notamment tableau, secteurs, courbes, aires et barres.

## <a name="parameters-supported-with-the-sql-magic"></a>Paramètres pris en charge avec la commande magique %%sql
La commande magique `%%sql` prend en charge différents paramètres qui vous permettent de contrôler le type de sortie que vous recevez quand vous exécutez des requêtes. Le tableau suivant répertorie les paramètres de sortie.

| Paramètre | exemples | Description |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Utilisez ce paramètre pour conserver le résultat de la requête dans le contexte Python %%local en tant que trame de données [Pandas](http://pandas.pydata.org/) . Le nom de la variable dataframe est le nom de variable que vous spécifiez. |
| -q |`-q` |Utilisez ce paramètre pour désactiver les visualisations pour la cellule. Si vous ne voulez pas visualiser automatiquement le contenu d’une cellule et préférez simplement capturer le contenu comme une trame de données, utilisez `-q -o <VARIABLE>`. Si vous souhaitez désactiver les visualisations sans capturer les résultats (par exemple, pour exécuter une requête SQL, comme une instruction `CREATE TABLE`), utilisez `-q` sans spécifier d’argument `-o`. |
| -m |`-m <METHOD>` |**METHOD** prend la valeur **take** ou **sample** (**take** est la valeur par défaut). Si la méthode est **take**, le noyau sélectionne des éléments à partir du haut du jeu de données de résultats spécifié par la valeur MAXROWS (décrite plus bas dans ce tableau). Si la méthode est **sample**, le noyau échantillonne de façon aléatoire les éléments du jeu de données en fonction du paramètre `-r` (décrit ci-après dans ce tableau). |
| -r |`-r <FRACTION>` |Ici, **FRACTION** est un nombre à virgule flottante compris entre 0,0 et 1,0. Si l’exemple de méthode de la requête SQL est `sample`, le noyau échantillonne de façon aléatoire la fraction spécifiée des éléments du jeu de résultats. Par exemple, si vous exécutez une requête SQL avec les arguments `-m sample -r 0.01`, 1 % des lignes de résultat sont échantillonnées de façon aléatoire. |
| -n |`-n <MAXROWS>` |**MAXROWS** est une valeur entière. Le noyau limite le nombre de lignes de la sortie au nombre défini par **MAXROWS**. Si **MAXROWS** est un nombre négatif comme **-1**, le nombre de lignes dans le jeu de résultats n’est pas limité. |

**Exemple :**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

L’instruction ci-dessus effectue les actions suivantes :

* Elle sélectionne tous les enregistrements présents dans **hivesampletable**.
* Comme nous utilisons le paramètre - q, elle désactive la visualisation automatique.
* Comme nous utilisons `-m sample -r 0.1 -n 500` , elle échantillonne de façon aléatoire 10 % des lignes présentes dans hivesampletable et limite la taille du jeu de résultats à 500 lignes.
* Enfin, comme nous avons utilisé `-o query2` , elle enregistre également la sortie dans une trame de données appelée **query2**.

## <a name="considerations-while-using-the-new-kernels"></a>Points à prendre en compte lors de l'utilisation des nouveaux noyaux

Quel que soit le noyau que vous utilisez, laisser les blocs-notes s’exécuter consomme vos ressources de cluster.  Avec ces noyaux, les contextes étant prédéfinis, le simple fait de quitter les blocs-notes n’arrête pas le contexte. Par conséquent, les ressources du cluster restent en cours d’utilisation. Une bonne pratique consiste à utiliser l’option **Fermer et arrêter** à partir du menu **Fichier** du bloc-notes menu lorsque vous avez fini de l’utiliser, ce qui supprime le contexte puis ferme le bloc-notes.     

## <a name="show-me-some-examples"></a>Voici quelques exemples :

Lorsque vous ouvrez un bloc-notes Jupyter, deux dossiers sont disponibles au niveau racine.

* Le dossier **PySpark** contient des exemples de Notebooks qui utilisent le nouveau noyau **Python**.
* Le dossier **Scala** comprend des exemples de blocs-notes qui utilisent le nouveau noyau **Spark**.

Vous pouvez ouvrir le Notebook **00 - [READ ME FIRST] Spark Magic Kernel Features** à partir du dossier **PySpark** ou **Spark** pour en savoir plus sur les différentes commandes magiques disponibles. Vous pouvez également recourir aux autres exemples de blocs-notes disponibles sous les deux dossiers pour savoir comment utiliser concrètement des blocs-notes Jupyter avec des clusters HDInsight Spark.

## <a name="where-are-the-notebooks-stored"></a>Où sont stockés les blocs-notes ?

Si votre cluster utilise le stockage Azure comme compte de stockage par défaut, les bloc-notes Jupyter sont enregistrés dans le compte de stockage sous le dossier **/HdiNotebooks**.  Les blocs-notes, les fichiers texte et les dossiers que vous créez dans Jupyter sont accessibles à partir du compte de stockage.  Par exemple, si vous utilisez Jupyter pour créer un dossier **myfolder** et un bloc-notes **myfolder/mynotebook.ipynb**, vous pouvez accéder à ce bloc-notes dans `/HdiNotebooks/myfolder/mynotebook.ipynb` au sein du compte de stockage.  L’inverse est également vrai : si vous chargez un bloc-notes directement dans votre compte de stockage dans `/HdiNotebooks/mynotebook1.ipynb`, le bloc-notes est également accessible à partir de Jupyter.  Les blocs-notes sont conservés dans le compte de stockage même après la suppression du cluster.

> [!NOTE]
> Les clusters HDInsight qui ont Azure Data Lake Store comme stockage par défaut ne stockent pas les bloc-notes dans le stockage associé.
>

Les blocs-notes sont enregistrés dans le compte de stockage dans un mode compatible avec HDFS. Si vous utilisez SSH dans le cluster, vous pouvez donc exécuter des commandes de gestion des fichiers telles que celles de l’extrait de code suivant :

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Que le cluster utilise le stockage Azure ou Azure Data Lake Store comme compte de stockage par défaut, les bloc-notes sont aussi enregistrés sur le nœud principal du cluster à l’emplacement `/var/lib/jupyter`.

## <a name="supported-browser"></a>Navigateur pris en charge

Les blocs-notes Jupyter sur clusters Spark HDInsight sont pris en charge uniquement sur Google Chrome.

## <a name="feedback"></a>Commentaires
Les nouveaux noyaux sont en phase d’évolution et gagneront en maturité avec le temps. Les API pourront également être amenés à évoluer au fur et à mesure des évolutions des noyaux. Nous aimerions recevoir vos commentaires concernant l'utilisation de ces nouveaux noyaux. Cela nous est utile pour préparer la version finale de ces noyaux. Vous pouvez laisser vos commentaires/remarques sous la section **Commentaires** en dessous de cet article.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
