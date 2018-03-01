---
title: Python UDF avec Apache Hive et Pig Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser des fonctions définies par l’utilisateur Python à partir de Hive et Pig dans HDInsight, la pile de technologies Hadoop sur Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/27/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f98fe82a9637cfdddf7af1dcb6aaf979bffcad6f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Utiliser des fonctions définies par l’utilisateur (UDF) Python avec Hive et Pig dans HDInsight

Découvrez comment utiliser des fonctions définies par l’utilisateur Python avec Apache Hive et Pig dans Hadoop sur Azure HDInsight.

## <a name="python"></a>Python sur HDInsight

Python 2.7 est installé par défaut sur HDInsight 3.0 et versions ultérieures. Apache Hive peut être utilisé avec cette version de Python pour traiter les flux de données. Le traitement de flux de données utilise STDOUT et STDIN pour transmettre des données entre Hive et les fonctions définies par l’utilisateur.

HDInsight inclut également Jython, une implémentation de Python écrite en Java. Jython s’exécute directement sur la Machine virtuelle Java et n’utilise pas le streaming. Jython est l’interpréteur Python recommandé lorsque vous utilisez Python avec Pig.

> [!WARNING]
> Les étapes de ce document partent des hypothèses suivantes : 
>
> * Vous créez les scripts Python sur votre environnement de développement local.
> * Vous chargez les scripts sur HDInsight à l’aide de la commande `scp` de votre session Bash locale ou à l’aide du script PowerShell fourni.
>
> Si vous souhaitez obtenir l’aperçu [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) pour utiliser HDInsight, vous devez :
>
> * Créez les scripts à l’intérieur de l’environnement de l’interpréteur de commandes cloud.
> * Utilisez `scp` pour charger les fichiers de l’interpréteur de commandes cloud vers HDInsight.
> * Utilisez `ssh` à partir de l’interpréteur de commandes pour vous connecter à HDInsight et exécuter les exemples.

## <a name="hivepython"></a>UDF Hive

Python peut être utilisé en tant que fonction définie par l'utilisateur à partir de Hive via l'instruction `TRANSFORM` HiveQL. Par exemple, le HiveQL suivant appelle le fichier `hiveudf.py` stocké dans le compte de stockage Azure par défaut du cluster.

**HDInsight Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Dans les clusters HDInsight Windows, la clause `USING` doit spécifier le chemin d’accès complet à python.exe.

Cet exemple effectue ce qui suit :

1. L’instruction `add file` au début du fichier ajoute le fichier `hiveudf.py` au cache distribué. Il devient alors accessible à tous les nœuds du cluster.
2. L’instruction `SELECT TRANSFORM ... USING` sélectionne les données de la table `hivesampletable`. Elle transmet également les valeurs clientid, devicemake et devicemodel au script `hiveudf.py`.
3. La clause `AS` décrit les champs renvoyés par `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Créer le fichier hiveudf.py


Sur votre environnement de développement, créez un fichier texte nommé `hiveudf.py`. Utilisez le code suivant comme contenu du fichier :

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Le script effectue les opérations suivantes :

1. Une ligne de données de STDIN est lue.
2. Le caractère de saut de ligne de fin est supprimé à l’aide de `string.strip(line, "\n ")`.
3. Lors du traitement par flux, une seule ligne contient toutes les valeurs séparées par un caractère de tabulation. `string.split(line, "\t")` peut donc être utilisé pour fractionner l’entrée à chaque tabulation et retourner uniquement les champs.
4. Une fois le traitement terminé, la sortie doit être écrite dans STDOUT sur une seule ligne, chaque champ étant séparé par une tabulation. Par exemple : `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. La boucle `while` se répète jusqu’à ce que plus aucun élément `line` ne soit lu.

La sortie du script est une concaténation des valeurs d’entrée pour `devicemake` et `devicemodel`, ainsi qu’un code de hachage de la valeur concaténée.

Pour savoir comment exécuter cet exemple sur votre cluster HDInsight, consultez la rubrique [Exécution des exemples](#running) .

## <a name="pigpython"></a>UDF Pig

Un script Python peut être utilisé comme une fonction définie par l'utilisateur à partir de Pig via l'instruction `GENERATE`. Vous pouvez exécuter le script à l’aide de Jython ou de C Python.

* Jython s’exécute sur la Machine virtuelle Java (JVM) et peut être appelé en mode natif à partir de Pig.
* C Python est un processus externe. Par conséquent, les données de Pig sur JVM sont envoyées au script s’exécutant dans un processus Python. La sortie du script Python est ensuite renvoyée dans Pig.

Pour spécifier l’interpréteur Python, utilisez `register` lorsque vous référencez le script Python. Les exemples suivants permettent d’enregistrer des scripts avec Pig en tant que `myfuncs` :

* **Pour utiliser Jython** : `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Pour utiliser C Python** : `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Lorsque vous utilisez Jython, le chemin d’accès au fichier pig_jython peut être un chemin d’accès local ou un chemin d’accès WASB://. Toutefois, lorsque vous utilisez C Python, vous devez référencer un fichier sur le système de fichiers local du nœud que vous utilisez pour envoyer le travail Pig.

Une fois l’inscription effectuée, le langage Pig Latin pour cet exemple est le même pour les deux :

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Cet exemple effectue ce qui suit :

1. La première ligne charge l’exemple de fichier de données `sample.log` dans `LOGS`. Elle définit également chaque enregistrement sous la forme d’un tableau de caractères `chararray`.
2. La ligne suivante élimine par filtrage les valeurs Null et stocke le résultat de l'opération dans `LOG`.
3. La ligne suivante itère sur les enregistrements dans `LOG` et utilise `GENERATE` pour appeler la méthode `create_structure` contenue dans le script Python/Jython chargé en tant que `myfuncs`. `LINE` est utilisé pour transmettre l'enregistrement actuel à la fonction.
4. Enfin, les sorties sont vidées dans STDOUT avec la commande `DUMP`. Cette commande affiche les résultats une fois l’opération terminée.

### <a name="create-the-pigudfpy-file"></a>Créer le fichier pigudf.py

Sur votre environnement de développement, créez un fichier texte nommé `pigudf.py`. Utilisez le code suivant comme contenu du fichier :

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Dans l’exemple Pig Latin, nous avons défini l’entrée `LINE` comme un tableau de caractères car il n’existait pas de schéma cohérent pour l’entrée. Le script Python transforme les données en un schéma cohérent pour la sortie.

1. L’instruction `@outputSchema` définit le format des données qui sont renvoyées à Pig. Dans le cas présent, il s'agit d'un **data bag**, qui est un type de données Pig. Ce conteneur contient les champs suivants, qui sont tous des tableaux de caractères (chaînes) :

   * date : date de création de l'entrée du journal
   * time : heure de création de l'entrée du journal
   * classname : nom de la classe pour laquelle l'entrée a été créée
   * level : niveau du journal
   * detail : détails pour l'entrée du journal

2. Ensuite, `def create_structure(input)` définit la fonction à laquelle Pig transmet les lignes.

3. L'exemple de données, `sample.log`, respecte pour l'essentiel le schéma basé sur les champs date, time, classname, level et detail. Toutefois, il contient quelques lignes qui commencent par `*java.lang.Exception*`. Ces lignes doivent être modifiées pour correspondre au schéma. L'instruction `if` recherche ces dernières, puis manipule les données d'entrée pour déplacer la chaîne `*java.lang.Exception*` à la fin, harmonisant les données avec le schéma de sortie prévu.

4. La commande `split` est ensuite utilisée pour fractionner les données aux quatre premiers caractères d'espacement. La sortie est assignée dans `date`, `time`, `classname`, `level`, et `detail`.

5. Enfin, les valeurs sont renvoyées à Pig.

Lorsque les données sont renvoyées à Pig, elles utilisent un schéma cohérent comme défini dans l’instruction `@outputSchema`.

## <a name="running"></a>Charger et exécuter les exemples

> [!IMPORTANT]
> Les étapes **SSH** fonctionnent uniquement avec un cluster HDInsight Linux. Les étapes **PowerShell** fonctionnent sur les clusters HDInsight Linux et Windows, mais nécessitent un client Windows.

### <a name="ssh"></a>SSH

Pour plus d’informations sur l’utilisation de SSH, voir [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Utilisez `scp` pour copier les fichiers sur votre cluster HDInsight. Par exemple, la commande ci-après copie les fichiers dans un cluster nommé **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Utilisez SSH pour la connexion au cluster.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. À partir de la session SSH, ajoutez les fichiers python téléchargés précédemment sur le stockage WASB pour le cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Après avoir téléchargé les fichiers, procédez comme suit pour exécuter les tâches Hive et Pig.

#### <a name="use-the-hive-udf"></a>Utiliser la fonction définie par l’utilisateur Hive

1. Pour vous connecter à Hive, utilisez la commande suivante :

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Cette commande démarre le client Beeline.

2. Entrez la requête suivante à l’invite de commandes `0: jdbc:hive2://headnodehost:10001/>` :

   ```hive
   add file wasb:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Après avoir entré la dernière ligne, la tâche doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable à l’exemple de sortie suivant :

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Pour quitter Beeline, utilisez la commande suivante :

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Utilisation de la commande Pig

1. Pour vous connecter à pig, utilisez la commande suivante :

    ```bash
    pig
    ```

2. Entrez les instructions ci-après au niveau de l’invite `grunt>` :

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Après l’entrée de la ligne suivante, le travail doit démarrer. Une fois le travail terminé, il renvoie une sortie comparable aux données suivantes :

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Utilisez `quit` pour quitter l’interpréteur de commandes Grunt, puis utilisez les éléments suivants pour modifier le fichier pigudf.py sur le système de fichiers local :

    ```bash
    nano pigudf.py
    ```

5. Une fois dans l’éditeur, annulez les marques de commentaire dans la ligne suivante en supprimant le caractère `#` initial :

    ```bash
    #from pig_util import outputSchema
    ```

    Cette ligne modifie le script Python pour le faire fonctionner avec C Python plutôt qu’avec Jython. Une fois la modification effectuée, utilisez **Ctrl+X** pour quitter l’éditeur. Sélectionnez **Y**, puis appuyez sur **Entrée** pour enregistrer les modifications.

6. Utilisez la commande `pig` pour relancer l’interpréteur de commandes. Une fois dans l’invite de commandes `grunt>` , utilisez les commandes suivantes pour exécuter le script Python à l’aide de l’interpréteur C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Une fois ce travail terminé, le résultat devrait être le même que lorsque vous avez exécuté le script à l’aide de Jython.

### <a name="powershell-upload-the-files"></a>PowerShell : chargement des fichiers

Vous pouvez utiliser PowerShell pour charger les fichiers sur le serveur HDInsight. Utilisez le script suivant pour charger les fichiers Python :

> [!IMPORTANT] 
> Les étapes de cette section utilisent Azure PowerShell. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Modifier la valeur `C:\path\to` du chemin d’accès aux fichiers sur votre environnement de développement.

Ce script récupère des informations concernant votre cluster HDInsight, puis extrait le compte et la clé pour le compte de stockage par défaut et télécharge les fichiers vers la racine du conteneur.

> [!NOTE]
> Pour plus d’informations sur le chargement des fichiers, consultez le document [Chargement de données pour les tâches Hadoop dans HDInsight](../hdinsight-upload-data.md).

#### <a name="powershell-use-the-hive-udf"></a>PowerShell : utiliser la fonction définie par l’utilisateur Hive

PowerShell peut également être utilisé pour exécuter des requêtes Hive à distance. Le script PowerShell suivant permet d’exécuter une requête Hive qui utilise le script **hiveudf.py** :

> [!IMPORTANT]
> Avant de s’exécuter, le script vous invite à fournir les informations de compte HTTPs/Admin pour votre cluster HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

La sortie du travail **Hive** doit ressembler à l’exemple de sortie suivant :

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell peut également être utilisé pour exécuter des tâches Pig Latin. Pour exécuter une tâche Pig Latin qui utilise le script **pigudf.py**, utilisez le script PowerShell suivant :

> [!NOTE]
> Lors de l’envoi à distance d’un travail à l’aide de PowerShell, il n’est pas possible d’utiliser C Python en tant qu’interpréteur .

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

Le résultat du travail **Pig** doit être comparable aux données suivantes :

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="errors-when-running-jobs"></a>Erreurs lors de l'exécution des travaux

Lorsque vous exécutez le travail hive, vous pouvez rencontrer une erreur similaire au texte suivant :

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Ce problème peut être causé par les fins de ligne dans le fichier Python. De nombreux éditeurs Windows utilisent par défaut CRLF comme fin de ligne, mais les applications Linux utilisent généralement LF.

Pour supprimer les caractères de retour chariot (CR) avant de charger le fichier dans HDInsight, vous pouvez utiliser les instructions PowerShell suivantes :

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Scripts PowerShell

Les deux exemples de script PowerShell utilisés pour l’exécution des exemples contiennent une ligne mise en commentaire qui affiche la sortie d’erreur pour le travail. Si la tâche ne donne pas le résultat prévu, supprimez les marques de commentaire sur la ligne suivante pour voir si les informations sur l'erreur signalent un problème.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Les informations sur l’erreur (STDERR) et le résultat du travail (STDOUT) sont également enregistrés dans votre stockage HDInsight.

| Pour ce travail... | Examinez ces fichiers dans le conteneur d'objets blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Étapes suivantes

Si vous devez charger des modules Python qui ne sont pas fournis par défaut, consultez l’article [How to deploy a Python module to Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Déploiement d’un module Python vers Azure HDInsight).

Pour connaître d’autres façons d’utiliser Pig et Hive et pour en savoir plus sur l’utilisation de MapReduce, consultez les documents suivants :

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
