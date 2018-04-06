---
title: Écriture Apache Storm dans le stockage/Data Lake Store - Azure HDInsight | Microsoft Docs
description: Découvrez comment utiliser Apache Storm pour écrire dans le stockage compatible HDFS pour HDInsight. Le stockage Azure ou Azure Data Lake Store fournissent un stockage compatible HDFS pour HDInsight. Ce document et l’exemple associé illustrent la manière dont le composant HdfsBolt peut être utilisé pour écrire dans le stockage par défaut d’un cluster Storm dans un cluster HDInsight.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 2310894e7257d0ddb919406a8f297089189a9484
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Écrire dans un stockage HDFS à partir d’Apache Storm sur HDInsight

Découvrez comment utiliser Storm pour écrire des données dans le stockage compatible HDFS utilisé par Apache Storm sur HDInsight. HDInsight peut utiliser à la fois le stockage Azure et Azure Data Lake comme stockage compatible HDFS. Storm fournit un composant [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) qui écrit des données dans le stockage HDFS. Ce document fournit des informations sur l’écriture dans ces deux types de stockages à partir du composant HdfsBolt. 

> [!IMPORTANT]
> L’exemple de topologie utilisé dans ce document s’appuie sur les composants inclus avec Storm sur HDInsight. Il faudra peut-être le modifier pour qu’il fonctionne avec Azure Data Lake Store en cas d’utilisation avec d’autres clusters Apache Storm.

## <a name="get-the-code"></a>Obtenir le code

Le projet contenant cette topologie est disponible en téléchargement à partir de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Pour compiler ce projet, la configuration de votre environnement de développement doit être la suivante :

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure. HDInsight 3.5 ou les versions ultérieures requièrent Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* `JAVA_HOME` : doit pointer vers le répertoire d’installation du Kit de développeur Java (JDK).
* `PATH` : doit contenir les chemins d’accès suivants :
  
    * `JAVA_HOME` (ou le chemin équivalent).
    * `JAVA_HOME\bin` (ou le chemin équivalent).
    * Le répertoire d’installation de Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Comment utiliser le composant HdfsBolt avec HDInsight

> [!IMPORTANT]
> Avant de vous servir du composant HdfsBolt avec Storm sur HDInsight, vous devez utiliser une action de script pour copier les fichiers jar requis à l’emplacement `extpath` de Storm. Pour plus d’informations, consultez la section [Configurer le cluster](#configure).

Le composant HdfsBolt utilise le schéma de fichier que vous fournissez pour savoir comment écrire dans le stockage HDFS. Avec HDInsight, utilisez l’un des schémas suivants :

* `wasb://` : avec un compte de stockage Azure.
* `adl://` : avec Azure Data Lake Store.

Le tableau suivant fournit des exemples d’utilisation du schéma de fichier dans différents scénarios :

| Schéma | Notes |
| ----- | ----- |
| `wasb:///` | Le compte de stockage par défaut est un conteneur d’objets blob dans un compte de stockage Azure. |
| `adl:///` | Le compte de stockage par défaut est un répertoire dans Azure Data Lake Store. Lors de la création du cluster, vous spécifiez le répertoire Data Lake Store qui sera la racine du stockage HDFS du cluster. Par exemple, le répertoire `/clusters/myclustername/`. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Un compte de stockage Azure différent du compte par défaut (compte supplémentaire) associé au cluster. |
| `adl://STORENAME/` | La racine du compte Data Lake Store utilisé par le cluster. Ce schéma vous permet d’accéder aux données qui se trouvent en dehors du répertoire qui contient le système de fichiers du cluster. |

Pour plus d’informations, consultez la documentation de référence [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) sur le site Apache.org.

### <a name="example-configuration"></a>Exemple de configuration

Le code YAML suivant est un extrait du fichier `resources/writetohdfs.yaml` inclus dans cet exemple. Ce fichier définit la topologie de Storm à l’aide du framework [Flux](https://storm.apache.org/releases/1.1.0/flux.html) pour Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Ce code YAML définit les éléments suivants :

* `syncPolicy` : définit quand les fichiers sont synchronisés/vidés sur le système de fichiers. Dans cet exemple, tous les 1 000 tuples.
* `fileNameFormat` : définit le modèle de chemin d’accès et de nom de fichier à utiliser lors de l’écriture de fichiers. Dans cet exemple, le chemin d’accès est fourni au moment de l’exécution à l’aide d’un filtre, avec l’extension de fichier `.txt`.
* `recordFormat` : définit le format interne des fichiers écrits. Dans cet exemple, les champs sont délimités par le caractère `|`.
* `rotationPolicy` : définit le moment de rotation des fichiers. Dans cet exemple, aucune rotation n’est effectuée.
* `hdfs-bolt` : utilise les composants précédents comme paramètres de configuration pour la classe `HdfsBolt`.

Pour plus d’informations sur le framework Flux, voir [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Configurer le cluster

Par défaut, Storm sur HDInsight n’inclut pas les composants utilisés par HdfsBolt pour communiquer avec le stockage Azure ou Data Lake Store dans le chemin de classe du cluster Storm. Utilisez l’action de script suivante pour ajouter ces composants au répertoire `extlib` de Storm sur votre cluster :

* URI du script : `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Nœuds auxquels appliquer : Nimbus, Supervisor
* Paramètres : aucun

Pour plus d’informations sur l’utilisation de ce script avec votre cluster, consultez le document [Personnalisation de clusters HDInsight basés sur Linux à l’aide d'une action de script](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Génération et empaquetage de la topologie

1. Téléchargez l’exemple de projet à partir de [ https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) dans votre environnement de développement.

2. À partir d’une invite de commandes, d’un terminal ou d’une session shell, modifiez les répertoires à la racine du projet téléchargé. Pour générer et empaqueter la topologie, utilisez la commande suivante :
   
        mvn compile package
   
    Une fois la génération et l’empaquetage terminés, vous obtenez un répertoire nommé `target`, qui contient un fichier `StormToHdfs-1.0-SNAPSHOT.jar`. Ce fichier contient la topologie compilée.

## <a name="deploy-and-run-the-topology"></a>Déployer et exécuter la topologie

1. Utilisez la commande suivante pour copier la topologie sur le cluster HDInsight. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   > [!NOTE]
   > Pour en savoir plus sur l’utilisation de `scp` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Une fois le téléchargement terminé, utilisez les éléments suivants pour vous connecter au cluster HDInsight à l’aide de SSH. Remplacez **USER** par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    À l’invite, saisissez le mot de passe que vous avez utilisé lors de la création de l’utilisateur SSH du cluster. Si vous utilisez une clé publique au lieu d’un mot de passe, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès à la clé privée correspondante.
   
   Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Une fois connecté, utilisez la commande suivante pour créer un fichier nommé `dev.properties` :

        nano dev.properties

4. Utilisez les données suivantes comme contenu du fichier `dev.properties` :

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > Cet exemple suppose que votre cluster utilise un compte de stockage Azure pour le stockage par défaut. Si votre cluster utilise Azure Data Lake Store, utilisez `hdfs.url: adl:///` à la place.
    
    Pour enregistrer le fichier, use __Ctrl + X__, puis __Y__, et enfin __Entrée__. Les valeurs dans ce fichier définissent l’URL de Data Lake Store et le nom du répertoire où sont écrites les données.

3. Utilisez la commande suivante pour démarrer la topologie :
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Cette commande démarre la topologie à partir du framework Flux en le soumettant au nœud Nimbus du cluster. La topologie est définie par le fichier `writetohdfs.yaml` inclus dans le fichier jar. Le fichier `dev.properties` est transféré en tant que filtre et les valeurs qu’il contient sont lues par la topologie.

## <a name="view-output-data"></a>Affichage des données de sortie

Pour afficher les données, utilisez la commande suivante :

    hdfs dfs -ls /stormdata/

Une liste des fichiers créés par cette topologie s’affiche.

La liste suivante est un exemple des données retournées par les commandes précédentes :

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Arrêt de la topologie

Les topologies Storm s’exécutent jusqu’à ce qu’elles soient arrêtées ou que le cluster soit supprimé. Pour arrêter la topologie, utilisez la commande suivante :

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Storm pour écrire dans le stockage Azure et Azure Data Lake Store, découvrez d’autres [exemples Storm pour HDInsight](apache-storm-example-topology.md).

