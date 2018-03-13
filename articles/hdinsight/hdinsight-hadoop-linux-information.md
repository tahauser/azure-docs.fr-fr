---
title: "Conseils sur l’utilisation de Hadoop sur un cluster HDInsight basé sur Linux - Azure | Microsoft Docs"
description: "Obtenez des conseils d’implémentation concernant l’utilisation de clusters HDInsight (Hadoop) basés sur Linux dans un environnement Linux familier, exécuté dans le cloud Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 4449dfa1b189f51292d24af884ba9d2addf1fe24
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation de HDInsight sous Linux

Les clusters Azure HDInsight fournissent Hadoop dans un environnement Linux familier, exécuté dans le cloud Azure. En principe, il fonctionne comme tout autre Hadoop sur une installation Linux. Ce document présente des différences spécifiques que vous devriez connaître.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prérequis

La plupart des étapes décrites dans ce document utilisent les utilitaires ci-après, que vous pouvez avoir besoin d’installer sur votre système.

* [cURL](https://curl.haxx.se/) : permet de communiquer avec les services Web.
* [jq](https://stedolan.github.io/jq/) : permet d’analyser des documents JSON.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) : permet de gérer les services Azure à distance

## <a name="users"></a>Utilisateurs

Sauf s’il est [joint à un domaine](./domain-joined/apache-domain-joined-introduction.md), HDInsight doit être considéré comme un système **mono-utilisateur**. Un compte d’utilisateur SSH unique est créé avec le cluster, avec les autorisations de niveau administrateur. Des comptes SSH supplémentaires peuvent être créés, mais ils auront également l’accès administrateur au cluster.

HDInsight joint à un domaine prend en charge la présence de plusieurs utilisateurs et des paramètres d’autorisation et de rôles plus précis. Pour plus d’informations, consultez la section [Gestion des clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Noms de domaine

Le nom de domaine complet (FQDN) à utiliser pour se connecter au cluster depuis Internet est **&lt;clustername>.azurehdinsight.net** ou (pour SSH exclusivement) **&lt;clustername-ssh>.azurehdinsight.net**.

En interne, chaque nœud du cluster porte un nom qui est attribué pendant la configuration du cluster. Pour rechercher les noms de cluster, consultez la page **Hôtes** sur l’interface Web Ambari. Vous pouvez également utiliser les éléments suivants pour renvoyer la liste des hôtes à partir de l’API REST Ambari :

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe du compte Administrateur. Cette commande renvoie un document JSON qui contient une liste des hôtes du cluster. Jq est utilisé pour extraire la valeur d’élément `host_name` pour chaque hôte.

Si vous avez besoin de trouver le nom du nœud d’un service spécifique, vous pouvez interroger Ambari pour obtenir ce composant. Par exemple, pour trouver les hôtes du nœud de nom HDFS, utilisez la commande suivante :

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Cette commande renvoie un document JSON qui décrit le service. Ensuite, jq extrait uniquement la valeur `host_name` des hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services

* **Ambari (Web)** : https://&lt;clustername&gt;.azurehdinsight.net

    Authentifiez-vous à l’aide du nom d’utilisateur et du mot de passe de l’administrateur du cluster, puis connectez-vous à Ambari.

    L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

    > [!IMPORTANT]
    > Certaines interfaces utilisateur web disponibles via Ambari accèdent aux nœuds à l’aide d’un nom de domaine interne. Les noms de domaine internes ne sont pas accessibles publiquement sur internet. Des erreurs vous indiquant que le serveur est introuvable sont susceptibles d’apparaître lorsque vous essayerez d’accéder à certaines fonctionnalités sur Internet.
    >
    > Pour bénéficier de toutes les fonctionnalités de l’interface utilisateur Web Ambari, vous devez utiliser un tunnel SSH pour assurer l’acheminement proxy vers le nœud principal cluster. Consultez [Utilisation de SSH Tunneling pour accéder à l’interface Web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces Web](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** : https://&lt;clustername&gt;.azurehdinsight.net/ambari

    > [!NOTE]
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://&lt;clustername&gt;.azurehdinsight.net/templeton

    > [!NOTE]
    > Authentifiez-vous avec le nom d’utilisateur et le mot de passe de l’administrateur du cluster.
    >
    > L’authentification est en clair. Utilisez toujours HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** - &lt;clustername&gt;-ssh.azurehdinsight.net sur le port 22 ou 23. Le port 22 est utilisé pour se connecter au nœud principal primaire tandis que le port 23 est utilisé pour se connecter au nœud principal secondaire. Pour plus d’informations sur les nœuds principaux, consultez [Disponibilité et fiabilité des clusters Hadoop dans HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]
    > Vous pouvez accéder aux nœuds principaux du cluster uniquement via SSH depuis une machine cliente. Une fois connecté, vous pouvez ensuite accéder aux nœuds Worker à l’aide de SSH depuis un nœud principal.

Pour plus d’informations, consultez le document [Ports utilisés par les services Hadoop sur HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="file-locations"></a>Emplacements des fichiers

Les fichiers relatifs à Hadoop se trouvent sur les nœuds du cluster dans `/usr/hdp`. Le répertoire contient les sous-répertoires suivants :

* **2.2.4.9-1** : le nom du répertoire correspond à la version de la plateforme Hortonworks Data utilisée par HDInsight. Le numéro qui figure sur votre cluster peut être différent de celui indiqué ici.
* **current** : ce répertoire contient des liens vers des sous-répertoires sous le répertoire **2.2.4.9-1**. Ce répertoire vous évite d’avoir à mémoriser le numéro de version.

Vous trouverez des exemples de données et de fichiers JAR sur le système HDSF (Hadoop Distributed File System) dans `/example` et `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, stockage Azure et Data Lake Store

Dans la plupart des distributions Hadoop, le système HDFS est sauvegardé par un stockage local sur les machines dans le cluster. L’utilisation du stockage peut être coûteuse pour une solution basée sur le cloud où vous êtes facturé à l’heure ou à la minute pour les ressources de calcul.

HDInsight utilise des objets Blob dans le stockage Azure ou Azure Data Lake Store comme magasin par défaut. Ces services offrent les avantages suivants :

* Un stockage à long terme peu coûteux
* Un accès à partir de services externes tels que les sites Web, les utilitaires de téléchargement de fichier, les kits de développement logiciel (SDK) en différentes langues et les navigateurs Web

Un compte de stockage Azure peut contenir jusqu’à 4,75 To, même si les objets blob individuels (ou les fichiers du point de vue de HDInsight) ne peuvent pas dépasser 195 Go. Azure Data Lake Store peut évoluer de manière dynamique pour contenir des milliers de milliards de fichiers, avec des fichiers individuels d’une taille supérieure à un pétaoctet. Pour plus d’informations, voir [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Présentation des objets blob) et [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Lorsque vous utilisez le stockage Azure ou Data Lake Store, vous n’avez aucune opération particulière à effectuer à partir de HDInsight pour accéder aux données. Par exemple, la commande suivante liste les fichiers dans le dossier `/example/data`, qu’il soit stocké sur le stockage Azure ou sur Data Lake Store :

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI et schéma

Certaines commandes peuvent vous imposer de spécifier le schéma dans l’URI lorsque vous accédez à un fichier. Par exemple, le composant Storm-HDFS requiert la spécification du schéma. Lorsque vous n’utilisez pas le stockage par défaut (stockage ajouté en tant que stockage « supplémentaire » au cluster), vous devez toujours indiquer le schéma dans l’URI.

Lorsque vous utilisez __Stockage Azure__, utilisez l’un des schémas d’URI suivants :

* `wasb:///` : accès au stockage par défaut via une communication non chiffrée.

* `wasbs:///` : accès au stockage par défaut via une communication chiffrée.  Le schéma wasbs est pris en charge uniquement à partir de HDInsight version 3.6 et versions ultérieures.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/` : utilisé pour communiquer avec un compte de stockage autre que celui par défaut. Par exemple, si vous avez un compte de stockage supplémentaire ou si vous accédez à des données stockées dans un compte de stockage accessible au public.

Lorsque vous utilisez __Data Lake Store__, utilisez l’un des schémas d’URI suivants :

* `adl:///` : accès au Data Lake Store par défaut pour le cluster.

* `adl://<storage-name>.azuredatalakestore.net/` : utilisé pour communiquer avec un Data Lake Store autre que celui par défaut. Également utilisé pour accéder aux données en dehors du répertoire racine de votre cluster HDInsight.

> [!IMPORTANT]
> Lorsque vous utilisez Data Lake Store en tant que magasin par défaut pour HDInsight, vous devez spécifier un chemin d’accès dans le magasin à utiliser comme racine de stockage HDInsight. Le chemin d’accès par défaut est : `/clusters/<cluster-name>/`.
>
> Lorsque vous utilisez `/` ou `adl:///` pour accéder aux données, vous pouvez uniquement accéder à des données stockées à la racine (par exemple, `/clusters/<cluster-name>/`) du cluster. Pour accéder à des données n’importe où dans le magasin, utilisez le format `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Quel stockage le cluster utilise-t-il ?

Vous pouvez utiliser Ambari pour récupérer la configuration de stockage par défaut du cluster. Utilisez la commande suivante pour récupérer les informations de configuration HDFS à l’aide de curl, puis filtrez ces informations avec [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Cette commande renvoie la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Vous devrez peut-être répertorier toutes les versions de configuration pour rechercher la version la plus récente.

Cette commande retourne une valeur semblable aux URI suivants :

* `wasb://<container-name>@<account-name>.blob.core.windows.net` si vous utilisez un compte de stockage Azure.

    Le nom du compte correspond au nom du compte Stockage Azure. Le nom du conteneur est le conteneur d’objets blob qui constitue la racine de l’espace de stockage en cluster.

* `adl://home` si vous utilisez Azure Data Lake Store. Pour obtenir le nom du magasin Data Lake Store, utilisez l’appel REST suivant :

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Cette commande renvoie le nom d’hôte suivant : `<data-lake-store-account-name>.azuredatalakestore.net`.

    Pour obtenir le répertoire du magasin qui correspond à la racine de HDInsight, utilisez l’appel REST suivant :

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Cette commande renvoie un chemin d’accès semblable à ce qui suit : `/clusters/<hdinsight-cluster-name>/`.

Vous pouvez également rechercher les informations de stockage à l’aide du portail Azure en suivant les étapes ci-dessous :

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre cluster HDInsight.

2. Dans la section **Propriétés**, sélectionnez **Comptes de stockage**. Les informations de stockage du cluster s’affichent.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Comment accéder à des fichiers situés en dehors de HDInsight ?

Il existe plusieurs façons d’accéder à des données à l’extérieur du cluster HDInsight. Voici quelques liens vers des utilitaires et Kits de développement logiciel (SDK) qui peuvent être utilisés pour exploiter vos données :

Si vous utilisez le __stockage Azure__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): commandes de l’interface de ligne de commande fonctionnant avec Azure. Après l’installation, utilisez la commande `az storage` pour obtenir de l’aide sur l’utilisation du stockage ou la commande `az storage blob` pour obtenir les commandes spécifiques aux objets blob.
* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): script python pour travailler avec des objets blob dans Azure Storage.
* Divers Kits de développement logiciel (SDK) :

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.JS](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [API REST d’Azure Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Si vous utilisez __Azure Data Lake Store__, consultez les liens suivants pour découvrir les méthodes permettant d’accéder à vos données :

* [Navigateur Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI 2.0](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Mise à l’échelle de votre cluster

La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier de manière dynamique le nombre de nœuds de données utilisés par un cluster. Vous pouvez effectuer des opérations de mise à l’échelle pendant que d’autres travaux ou processus s’exécutent sur un cluster.

Les différents types de cluster sont affectés par la mise à l’échelle comme suit :

* **Hadoop** : quand vous réduisez le nombre de nœuds dans un cluster, les services du cluster sont redémarrés. Les opérations de mise à l’échelle peuvent provoquer l’échec des tâches en cours d’exécution ou en attente à la fin de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l’opération terminée.
* **HBase** : les serveurs régionaux sont automatiquement équilibrés en l’espace de quelques minutes, une fois l’opération de mise à l’échelle terminée. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

    1. Connectez-vous au cluster HDInsight à l’aide de SSH : Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Pour démarrer le shell HBAse, utilisez la commande suivante :

            hbase shell

    3. Une fois le shell HBase chargé, utilisez ce qui suit pour équilibrer manuellement les serveurs régionaux :

            balancer

* **Storm** : vous devez rééquilibrer les topologies Storm en cours d’exécution après l’exécution d’une opération de mise à l’échelle. Le rééquilibrage permet à la topologie de réajuster les paramètres de parallélisme basés sur le nouveau nombre de nœuds du cluster. Pour rééquilibrer les topologies en cours d’exécution, utilisez l’une des options suivantes :

    * **SSH** : connectez-vous au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

            storm rebalance TOPOLOGYNAME

        Vous pouvez également spécifier des paramètres pour remplacer les indicateurs de parallélisme initialement fournis par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` permet de reconfigurer la topologie sur 5 processus Worker, 3 exécuteurs pour le composant blue-spout et 10 exécuteurs pour le composant yellow-bolt.

    * **Interface storm** : utilisez les étapes suivantes pour rééquilibrer une topologie avec l’interface utilisateur Storm.

        1. Ouvrez **https://CLUSTERNAME.azurehdinsight.net/stormui** dans un navigateur web, CLUSTERNAME désignant le nom de votre cluster Storm. Si vous y êtes invité, entrez le nom et le mot de passe de l’administrateur (admin) du cluster HDInsight spécifiés lors de la création du cluster.
        2. Sélectionnez la topologie que vous souhaitez rééquilibrer, puis le bouton **Rééquilibrer** . Saisissez le délai avant l’opération de rééquilibrage.

* **Kafka** : vous devez rééquilibrer les réplicas de partition après les opérations de mise à l’échelle. Pour plus d’informations, consultez le document [Haute disponibilité des données avec Kafka dans HDInsight](./kafka/apache-kafka-high-availability.md).

Pour obtenir des informations spécifiques sur la mise à l’échelle de votre cluster HDInsight, consultez :

* [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestion des clusters Hadoop dans HDInsight au moyen d’Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Comment installer Hue (ou un autre composant Hadoop) ?

HDInsight est un service géré. Si Azure détecte un problème avec le cluster, il peut supprimer le nœud défaillant et créer un nœud pour le remplacer. Si vous installez manuellement des éléments sur le cluster, ils ne sont pas conservés lorsque cette opération se produit. Utilisez plutôt [Actions de script HDInsight](hdinsight-hadoop-customize-cluster.md). Une action de script peut être utilisée pour effectuer les modifications suivantes :

* Installer et configurer un service ou un site web.
* Installer et configurer un composant qui nécessite des modifications de configuration sur plusieurs nœuds du cluster.

Les actions de script sont des scripts Bash. Les scripts s’exécutent pendant la création du cluster et sont utilisés pour installer et configurer des composants supplémentaires. Des exemples de scripts sont fournis pour l’installation des composants suivants :

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Pour plus d’informations sur le développement de vos propres actions de script, consultez [Développement d’actions de Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>Fichiers Jar

Certaines technologies Hadoop sont fournies dans des fichiers jar autonomes, qui contiennent des fonctions utilisées dans le cadre d’un travail MapReduce ou à partir de Pig ou Hive. Il ne nécessitent généralement aucune configuration et peuvent être chargés dans le cluster après la création et être utilisés directement. Si vous souhaitez que le composant survive à la réinitialisation du cluster, vous pouvez stocker le fichier jar dans le stockage par défaut de votre cluster (WASB ou ADL).

Par exemple, si vous souhaitez utiliser la dernière version de [DataFu](http://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet, puis le télécharger vers le cluster HDInsight. Suivez ensuite la documentation DataFu pour savoir comment l’utiliser à partir de Pig ou Hive.

> [!IMPORTANT]
> Certains composants qui sont des fichiers jar autonomes sont fournis avec HDInsight, mais ne se trouvent pas dans le chemin d’accès. Pour rechercher un composant spécifique sur votre cluster, vous pouvez utiliser la commande suivante :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Cette commande renvoie le chemin d’accès de tous les fichiers jar correspondants.

Pour utiliser une version différente d’un composant, chargez la version dont vous avez besoin et utilisez-la dans vos tâches.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aide à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>étapes suivantes

* [Effectuer la migration de HDInsight Windows vers HDInsight Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utilisation de Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)
