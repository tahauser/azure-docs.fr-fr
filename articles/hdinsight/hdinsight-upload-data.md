---
title: Téléchargement de données pour les tâches Hadoop dans HDInsight | Microsoft Docs
description: Découvrez comment télécharger des données pour les tâches Hadoop et y accéder dans HDInsight avec l'interface CLI Azure, Azure Storage Explorer, Azure PowerShell, la ligne de commande Hadoop ou Sqoop.
keywords: etl hadoop, obtention de données dans hadoop, données de charge hadoop
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: ddb6291cdff7e2b65f54e89196c2b07dd6e4aaff
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Téléchargement de données pour les tâches Hadoop dans HDInsight

Azure HDInsight fournit un système HDFS (Hadoo Distributed File System) complet pour le Stockage Azure et Azure Data Lake Store. Le Stockage Azure et Data lake Store sont conçus en tant qu’extension HDFS pour fournir une expérience fluide aux clients. Ils permettent à l’ensemble des composants de l’écosystème Hadoop de fonctionner directement sur les données qu’il gère. Le Stockage Azure et Data Lake Store sont des systèmes de fichiers distincts qui sont optimisés pour le stockage de données et pour les calculs réalisés à partir de ces données. Pour connaître les avantages que constitue l’utilisation du Stockage Azure, consultez [Utiliser le Stockage Azure avec HDInsight][hdinsight-storage] et [Utiliser Data Lake Store avec HDInsight](hdinsight-hadoop-use-data-lake-store.md).

## <a name="prerequisites"></a>Prérequis


Notez les prérequis suivants avant de démarrer :

* Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d’Azure HDInsight][hdinsight-get-started] ou la rubrique [Créer des clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Connaissance des deux articles suivants :

    - [Utiliser le stockage Azure avec HDInsight][hdinsight-storage]
    - [Utiliser Data Lake Store avec HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="upload-data-to-azure-storage"></a>Charger des données sur le Stockage Azure

### <a name="command-line-utilities"></a>Utilitaires de ligne de commande
Microsoft fournit les utilitaires suivants pour utiliser le Stockage Azure :

| Outil | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interface de ligne de commande Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] |✔ | |✔ |
| [Commande Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Alors que l’interface de ligne de commande Azure, Azure PowerShell et AzCopy peuvent tous être utilisés en dehors d’Azure, la commande Hadoop est disponible uniquement sur le cluster HDInsight. En outre, la commande autorise uniquement le chargement de données du système de fichiers local sur le Stockage Azure.
>
>

#### <a id="xplatcli"></a>Interface CLI Azure
L'interface CLI Azure est un outil interplateforme qui vous permet de gérer les services Azure. Pour charger des données sur le Stockage Azure, effectuez les opérations suivantes :

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Installez et configurez l’interface de ligne de commande Azure pour Mac, Linux et Windows](../cli-install-nodejs.md).
2. Ouvrez une invite de commandes, un bash ou un autre shell et procédez comme suit pour vous authentifier auprès de votre abonnement Azure.

    ```cli
    azure login
    ```

    Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe de votre abonnement.
3. Entrez la commande suivante pour répertorier les comptes de stockage pour votre abonnement :

    ```cli
    azure storage account list
    ```

4. Sélectionnez le compte de stockage qui contient l'objet blob que vous souhaitez utiliser, puis entrez la commande suivante pour récupérer la clé de ce compte :

    ```cli
    azure storage account keys list <storage-account-name>
    ```

    Cette commande retourne les clés **primaire** et **secondaire**. Copiez la valeur de la clé **primaire** , car vous devrez l’utiliser lors des étapes suivantes.
5. Utilisez la commande suivante pour récupérer une liste de conteneurs d'objets blob dans le compte de stockage :

    ```cli
    azure storage container list -a <storage-account-name> -k <primary-key>
    ```

6. Utilisez les commandes suivantes pour charger et télécharger des fichiers vers l'objet blob :

   * Pour charger un fichier :

        ```cli
        azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
        ```

   * Pour télécharger un fichier :

        ```cli
        azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>
        ```
    
> [!NOTE]
> Si vous utilisez toujours le même compte de stockage, vous pouvez définir les variables d’environnement suivantes au lieu de spécifier le compte et la clé de chaque commande :
>
> * **AZURE\_STORAGE\_ACCOUNT** : le nom du compte de stockage
> * **AZURE\_STORAGE\_ACCESS\_KEY** : la clé du compte de stockage
>
>

#### <a id="powershell"></a>Azure PowerShell
Azure PowerShell est un environnement de création de scripts qui vous permet de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations sur la configuration de votre poste de travail pour exécuter Azure PowerShell, consultez l'article [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Pour charger un fichier local sur le Stockage Azure**

1. Ouvrez la fenêtre de la console Azure PowerShell, comme indiqué dans la section [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).
2. Définissez les valeurs des cinq premières variables dans le script suivant :

    ```powershell
    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $fileName ="<LocalFileName>"
    $blobName = "<BlobName>"

    # Get the storage account key
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    # Create the storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    # Copy the file from local workstation to the Blob container
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
    ```

3. Collez le script dans la console Azure PowerShell pour l'exécuter afin de copier le fichier.

Pour des exemples de scripts PowerShell créés pour fonctionner avec HDInsight, consultez les [outils HDInsight](https://github.com/blackmist/hdinsight-tools).

#### <a id="azcopy"></a>AzCopy
AzCopy est un utilitaire de ligne de commande conçu pour simplifier la tâche de transfert de données à destination et en provenance d'un compte Azure Storage. Vous pouvez l'utiliser comme un outil indépendant ou l'incorporer dans une application existante. [Téléchargement d’AzCopy][azure-azcopy-download].

La syntaxe d'AzCopy est :

```command
AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]
```

Pour plus d’informations, consultez la page [AzCopy : téléchargement de fichiers pour les objets blob Azure][azure-azcopy].

La préversion d’Azcopy sur Linux est disponible.  Consultez [Announcing AzCopy on Linux Preview](https://blogs.msdn.microsoft.com/windowsazurestorage/2017/05/16/announcing-azcopy-on-linux-preview/) (Annonce de la préversion d’AzCopy sur Linux).

#### <a id="commandline"></a>Ligne de commande Hadoop
La ligne de commande Hadoop est utile uniquement pour stocker les données dans le blob du stockage Azure quand celles-ci sont déjà présentes sur le nœud principal du cluster.

Pour utiliser la commande Hadoop, vous devez d'abord vous connecter au nœud principal à l'aide de l'une des méthodes suivantes :

* **HDInsight Windows**: [connexion à l'aide du Bureau à distance](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight Linux** : connexion à l’aide de [SSH ou PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)

Une fois connecté, vous pouvez utiliser la syntaxe suivante pour télécharger un fichier dans le stockage.

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

Par exemple, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Comme le système de fichiers par défaut pour HDInsight se trouve dans le Stockage Azure, /example/data.txt s’y trouve également. Vous pouvez également faire référence au fichier comme ceci :

    wasb:///example/data/data.txt

or

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Pour la liste des autres commandes Hadoop qui fonctionnent avec des fichiers, voir [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> Sur les clusters HBase, la taille de bloc par défaut durant l’écriture de données est de 256 Ko. Bien que cela fonctionne bien quand vous utilisez les API HBase ou les API REST, l’utilisation des commandes `hadoop` ou `hdfs dfs` pour écrire des données supérieures à ~12 Go génère une erreur. Pour plus d’informations, consultez la section [Exception de stockage pour l’écriture sur un objet blob](#storageexception) dans cet article.
>
>

### <a name="graphical-clients"></a>Clients graphiques
Plusieurs applications fournissent également une interface graphique pour utiliser Azure Storage. Le tableau suivant répertorie quelques-unes de ces applications :

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Outils Microsoft Visual Studio pour HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

#### <a name="visual-studio-tools-for-hdinsight"></a>Outils Visual Studio pour HDInsight
Pour plus d’informations, consultez [Accéder aux ressources liées](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources).

#### <a id="storageexplorer"></a>Azure Storage Explorer
*Azure Storage Explorer* est un outil utile pour examiner et modifier les données de l’objet blob. Il s’agit d’un outil open source gratuit qui peut être téléchargé à partir de [http://storageexplorer.com/](http://storageexplorer.com/). Le code source est également disponible à partir de ce lien.

Avant de l'utiliser, vous devez connaître le nom et la clé de votre compte Azure Storage. Pour obtenir des instructions permettant d’obtenir ces informations, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de l’article [Création, gestion et suppression d’un compte de stockage][azure-create-storage-account].

1. Exécutez Azure Storage Explorer. Si vous exécutez l’Explorateur de stockage pour la première fois, vous êtes invité à saisir le **_Nom du compte de stockage** et la **Clé du compte de stockage**. Si vous avez déjà exécuté l’Explorateur de stockage, utilisez le bouton **Ajouter** pour ajouter un nom et une clé de compte de stockage.

    Entrez le nom et la clé du compte de stockage utilisé par votre cluster HDinsight, puis sélectionnez **ENREGISTRER ET OUVRIR**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Dans la liste de conteneurs située à gauche de l’interface, cliquez sur le nom du conteneur associé à votre cluster HDInsight. Par défaut, il s’agit du nom du cluster HDInsight, mais il peut être différent si vous avez entré un nom spécifique au moment de la création du cluster.
3. Dans la barre d’outils, sélectionnez l’icône de téléchargement.

    ![Barre d’outils avec icône téléchargement mise en surbrillance](./media/hdinsight-upload-data/toolbar.png)
4. Indiquez un fichier à télécharger, puis cliquez sur **Ouvrir**. Lorsque vous y êtes invité, sélectionnez **Télécharger** pour télécharger le fichier vers la racine du conteneur de stockage. Si vous souhaitez télécharger le fichier vers un chemin d’accès spécifique, entrez-le dans le champ **Destination**, puis sélectionnez **Télécharger**.

    ![Boîte de dialogue de téléchargement de fichier](./media/hdinsight-upload-data/fileupload.png)

    Une fois le téléchargement du fichier terminé, vous pouvez l’utiliser à partir des tâches du cluster HDInsight.

### <a name="mount-azure-storage-as-local-drive"></a>Monter le stockage Azure comme un lecteur Local
Consultez [Monter le stockage Azure comme un lecteur Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

### <a name="upload-using-services"></a>Effectuer un chargement en utilisant des services
#### <a name="azure-data-factory"></a>Azure Data Factory
Le service Azure Data Factory est un service entièrement géré pour composer des services de stockage de données, de traitement de données et de déplacement de données dans des pipelines de production rationalisés, évolutifs et fiables.

Azure Data Factory permet de déplacer des données dans le Stockage Azure ou de créer des pipelines de données qui utilisent directement des fonctionnalités HDInsight telles que Hive et Pig.

Pour plus d'informations, consultez la [Documentation Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

#### <a id="sqoop"></a>Apache Sqoop
Sqoop est un outil conçu pour transférer des données entre Hadoop et des bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQ Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR.

Pour plus d’informations, consultez [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop].

### <a name="development-sdks"></a>Kits de développement logiciel (SDK) de développement
Le Stockage Azure est également accessible à l’aide d’un SDK Azure dans les langages de programmation suivants :

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Pour plus d'informations sur l'installation des kits de développement logiciel (SDK) Azure, consultez [Téléchargements Azure](https://azure.microsoft.com/downloads/)

### <a name="troubleshooting"></a>Résolution de problèmes
#### <a id="storageexception"></a>Exception de stockage pour l’écriture sur un objet blob
**Symptômes** : quand vous utilisez la commande `hadoop` ou `hdfs dfs` pour écrire des fichiers supérieurs à ~12 Go sur un cluster HBase, vous pouvez rencontrer l’erreur suivante :

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Cause** : les clusters HBase sur HDInsight ont par défaut une taille de bloc de 256 Ko lors de l’écriture dans le stockage Azure. Bien que cela fonctionne pour les API HBase ou les API REST, cela génère une erreur quand vous utilisez les utilitaires en ligne de commande `hadoop` ou `hdfs dfs`.

**Résolution** : utilisez `fs.azure.write.request.size` pour spécifier une plus grande taille de bloc. Vous pouvez le faire sur une base par utilisation à l’aide du paramètre `-D`. La commande suivante illustre l’utilisation de ce paramètre avec la commande `hadoop` :

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Vous pouvez également augmenter la valeur de `fs.azure.write.request.size` globalement à l’aide d’Ambari. Vous pouvez utiliser les étapes suivantes pour modifier la valeur dans l’interface utilisateur Web d’Ambari :

1. Dans votre navigateur, accédez à l’interface utilisateur Web d’Ambari pour votre cluster. Il s’agit de https://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom de votre cluster.

    Lorsque vous y êtes invité, entrez le nom et le mot de passe administrateur correspondant au cluster HDInsight.
2. Sur le côté gauche de l’écran, sélectionnez **HDFS**, puis l’onglet **Configurations**.
3. Dans le champ **Filtrer...**, entrez `fs.azure.write.request.size`. Le champ et la valeur actuelle s’affichent au milieu de la page.
4. Modifiez la valeur 262144 (256 Ko) sur la nouvelle valeur. Par exemple, 4194304 (4 Mo).

![Image de la modification de la valeur via l’interface utilisateur Web d’Ambari](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment obtenir des données avec HDInsight, consultez les articles suivants pour apprendre à effectuer des analyses :

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]

[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]:../storage/common/storage-create-storage-account.md
[azure-azcopy-download]:../storage/common/storage-use-azcopy.md
[azure-azcopy]:../storage/common/storage-use-azcopy.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
