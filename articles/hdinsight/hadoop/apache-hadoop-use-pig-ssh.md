---
title: Utiliser Hadoop Pig avec SSH sur un cluster HDInsight - Azure | Documents Microsoft
description: "Découvrez comment se connecter à un cluster Hadoop Linux avec SSH, avant d’utiliser la commande Pig pour exécuter interactivement des instructions Pig Latin ou avec le traitement par lots."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: larryfr
ms.openlocfilehash: fa19913928bad8b91777c0904324ff5983f6472c
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Exécution de tâches Pig sur un cluster Linux avec la commande Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Découvrez comment exécuter interactivement des tâches Pig à partir d’une connexion SSH sur votre cluster HDInsight. Le langage de programmation Pig Latin permet de décrire les transformations appliquées aux données d’entrée pour produire le résultat souhaité.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Connexion avec SSH

Utilisez SSH pour vous connecter à votre cluster HDInsight. L’exemple suivant se connecte à un cluster nommé **myhdinsight** à l’aide du nom de compte **sshuser** :

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Utilisation de la commande Pig

1. Une fois connecté, lancez l’interface de ligne de commande Pig à l’aide de la commande suivante :

    ```bash
    pig
    ```

    Après quelques instants, l’invite devient `grunt>`.

2. Entrez l’instruction suivante :

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Cette commande charge le contenu du fichier sample.log dans les JOURNAUX. Vous pouvez afficher le contenu du fichier à l’aide de l’instruction suivante :

    ```piglatin
    DUMP LOGS;
    ```

3. Transformez ensuite les données en appliquant une expression régulière pour extraire uniquement le niveau de journalisation de chaque enregistrement à l’aide de l’instruction suivante :

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Vous pouvez utiliser **DUMP** pour afficher les données après la transformation. Dans ce cas, utilisez `DUMP LEVELS;`.

4. Continuez à appliquer des transformations à l’aide des instructions dans le tableau suivant :

    | Instruction Pig Latin | Descriptif de l’instruction |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Supprime les lignes contenant une valeur null pour le niveau de journal et stocke les résultats dans `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Regroupe les lignes par niveau de journal et stocke les résultats dans `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Crée un jeu de données qui contient chaque valeur unique au niveau journal et le nombre de fois où elle se produit. Le jeu de données est stocké dans `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Trie les niveaux du journal par décompte (décroissant) et stocke ces informations dans `RESULT`. |

    > [!TIP]
    > Utilisez `DUMP` pour afficher le résultat de la transformation après chaque étape.

5. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de l’instruction `STORE` . Par exemple, l’instruction qui suit enregistre `RESULT` dans le répertoire `/example/data/pigout` sur le stockage par défaut de votre cluster :

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]
   > Les données sont stockées dans le répertoire spécifié dans des fichiers nommés `part-nnnnn`. Si le répertoire existe déjà, vous recevez un message d’erreur.

6. Pour quitter l’invite Grunt, entrez l’instruction suivante :

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Fichiers de commandes Pig Latin

Vous pouvez également utiliser la commande Pig pour exécuter le Pig Latin contenu dans un fichier.

1. Après avoir quitté l’invite Grunt, utilisez la commande suivante pour créer un fichier nommé `pigbatch.pig` :

    ```bash
    nano ~/pigbatch.pig
    ```

2. Saisissez ou collez les lignes suivantes :

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Lorsque vous avez terminé, utilisez __Ctrl__ + __X__, __Y__, puis appuyez sur __Entrée__ pour enregistrer le fichier.

3. Utilisez la commande suivante pour exécuter le fichier `pigbatch.pig` à l’aide de la commande Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Une fois le traitement par lots terminé, vous observez la sortie suivante :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig sur HDInsight, consultez le document suivant :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres façons de travailler avec Hadoop sur HDInsight, consultez les documents suivants :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
