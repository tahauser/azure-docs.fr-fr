---
title: Effectuer la migration de HDInsight Windows vers HDInsight Linux - Azure | Microsoft Docs
description: "Apprenez à effectuer la migration d’un cluster HDInsight Windows vers un cluster HDInsight Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: 764a41dc9e890de85c3bfab3d2f78d5a07b39dff
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Effectuer la migration d’un cluster HDInsight Windows vers un cluster Linux

Ce document décrit les différences entre HDInsight sous Windows et sous Linux. Il aide également à migrer des charges de travail existantes sur un cluster Linux.

Bien que HDInsight basé sur Windows offre un moyen facile d’utiliser Hadoop dans le cloud, vous devrez peut-être migrer vers un cluster Linux. Par exemple, pour tirer parti des outils et des technologies Linux requis pour votre solution. De nombreux éléments de l’écosystème Hadoop sont développés sur des systèmes Linux et peuvent ne pas être utilisables avec HDInsight Windows. De nombreux livres, vidéos et autres documents de formation supposent que vous travaillez sur un système Linux quand vous utilisez Hadoop.

> [!NOTE]
> Les clusters HDInsight utilisent le support à long terme (long term support, LTS) Ubuntu du système d’exploitation pour les nœuds du cluster. Pour plus d’informations sur la version d’Ubuntu disponible avec HDInsight, ainsi que d’autres informations sur le contrôle de version des composants, consultez la page [Versions des composants HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tâches de migration

Le workflow général pour la migration se présente comme suit.

![Diagramme du workflow de migration](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Lisez chaque section de ce document pour comprendre les modifications qui pourraient se révéler nécessaires lors de la migration.

2. Créez un cluster Linux comme environnement de test ou d’assurance qualité. Pour plus d’informations sur la création d’un cluster Linux, consultez [Création de clusters Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Copiez les travaux, sources de données et récepteurs existants dans le nouvel environnement.

4. Effectuez des tests de validation pour vérifier que vos tâches fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifiez un temps d’arrêt pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes :

1. Sauvegardez toutes les données temporaires stockées localement sur les nœuds du cluster, par exemple si vous avez des données stockées directement sur un nœud principal.

2. Supprimez le cluster Windows.

3. Créez un cluster Linux à l’aide du même magasin de données par défaut que celui utilisé par le cluster Windows. Le cluster sous Linux peut continuer à fonctionner avec vos données de production existantes.

4. Importez toutes les données temporaires que vous avez sauvegardées.

5. Démarrez des tâches ou poursuivez le traitement avec le nouveau cluster.

### <a name="copy-data-to-the-test-environment"></a>Copier des données dans l’environnement de test

Il existe de nombreuses méthodes pour copier les données et les tâches, mais les deux présentées dans cette section représentent les méthodes les plus simples pour déplacer directement des fichiers vers un cluster de test.

#### <a name="hdfs-copy"></a>Copie HDFS

Utilisez les étapes suivantes pour copier des données à partir du cluster de production vers le cluster de test. Ces étapes utilisent l’utilitaire `hdfs dfs` inclus avec HDInsight.

1. Recherchez les informations sur le compte de stockage et le conteneur par défaut pour votre cluster existant. L’exemple suivant utilise PowerShell pour récupérer ces informations :

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Suivez les étapes du document Créer des clusters Hadoop basés sur Linux dans HDInsight pour créer un environnement de test. Arrêtez-vous avant de créer le cluster et sélectionnez à la place **Configuration facultative**.

3. Dans la section Configuration facultative, sélectionnez **Comptes de stockage liés**.

4. Sélectionnez **Ajouter une clé de stockage**puis sélectionnez le compte de stockage renvoyé par le script PowerShell à l’étape 1, quand vous y êtes invité. Cliquez sur **Sélectionner** dans chaque section. Enfin, créez le cluster.

5. Une fois le cluster créé, connectez-vous à celui-ci à l’aide de **SSH**. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

6. Dans la session SSH, utilisez la commande suivante pour copier les fichiers à partir du compte de stockage lié vers le nouveau compte de stockage par défaut. Remplacez CONTAINER par les informations du conteneur retournées par PowerShell. Remplacez __ACCOUNT__ par le nom du compte. Remplacez le chemin d’accès aux données par le chemin d’accès à un fichier de données.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Si la structure de répertoire qui contient les données n’existe pas dans l’environnement de test, vous pouvez la créer à l’aide de la commande suivante :

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    Le commutateur `-p` permet la création de tous les répertoires dans le chemin.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Copie directe entre les objets blob dans Stockage Azure

Vous pouvez également utiliser l’applet de commande `Start-AzureStorageBlobCopy` Azure PowerShell pour copier des blobs entre des comptes de stockage externes à HDInsight. Pour plus d’informations, consultez la section Gestion des objets blob Azure dans Utilisation d’Azure PowerShell avec Azure Storage.

## <a name="client-side-technologies"></a>Technologies côté client

Les technologies côté client telles que les [cmdlets PowerShell Azure](/powershell/azureps-cmdlets-docs), [Azure CLI](../cli-install-nodejs.md), ou le [kit de développement logiciel (SDK) .NET pour Hadoop](https://hadoopsdk.codeplex.com/) continuent à utiliser les clusters basés sur Linux. Ces technologies reposent sur les API REST qui sont les mêmes pour les deux types de clusters du système d’exploitation.

## <a name="server-side-technologies"></a>Technologies côté serveur

Le tableau suivant contient des informations utiles sur la migration des composants côté serveur qui sont spécifiques à Windows.

| Si vous utilisez cette technologie... | Procédez comme suit... |
| --- | --- |
| **PowerShell** (scripts côté serveur, notamment les actions de script utilisées lors de la création du cluster) |Réécrivez-les en tant que scripts Bash. En ce qui concerne les actions de script, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) et [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md). |
| **Interface de ligne de commande Azure** (scripts côté serveur) |Même si l’interface de ligne de commande Azure est disponible sous Linux, elle n’est pas préinstallée sur les nœuds principaux du cluster HDInsight. Pour plus d’informations sur l’installation d’Azure CLI, consultez [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Composants .NET** |.NET est pris en charge sur HDInsight sous Linux via [Mono](https://mono-project.com). Pour plus d’informations, consultez la section [Migrer des solutions .NET vers HDInsight sous Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Composants Win32 ou autre technologie propre à Windows** |Les conseils varient selon le composant ou la technologie. Vous trouverez peut-être une version compatible avec Linux. Sinon, vous devrez trouver une autre solution ou réécrire ce composant. |

> [!IMPORTANT]
> Le kit de développement logiciel (SDK) de gestion HDInsight n’est pas entièrement compatible avec Mono. Ne l’utilisez pas dans le cadre de solutions déployées sur le cluster HDInsight.

## <a name="cluster-creation"></a>Création du cluster

Cette section fournit des informations sur les différences dans la création du cluster.

### <a name="ssh-user"></a>Utilisateur SSH

Les clusters HDInsight Linux utilisent le protocole **SSH (Secure Shell)** pour permettre l’accès distant aux nœuds du cluster. Contrairement au Bureau à distance pour les clusters sous Windows, la plupart des clients SSH ne fournissent pas d’expérience utilisateur graphique. Au lieu de cela, les clients SSH fournissent une ligne de commande qui vous permet d’exécuter des commandes sur le cluster. Certains clients (tels que [MobaXterm](http://mobaxterm.mobatek.net/)) fournissent un navigateur de système de fichiers graphiques en plus d’une ligne de commande distante.

Lors de la création du cluster, vous devez fournir un utilisateur SSH et un **mot de passe** ou un **certificat de clé publique** pour l’authentification.

Nous recommandons d’utiliser un certificat de clé publique, qui est plus sécurisé qu’un mot de passe. L’authentification par certificat fonctionne en générant une paire de clés publique/privée signée, puis en fournissant la clé publique lors de la création du cluster. Lors de la connexion au serveur à l’aide de SSH, la clé privée sur le client fournit l’authentification pour la connexion.

Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Personnalisation des clusters

Les **actions de script** utilisées avec les clusters Linux doivent être écrites dans un script Bash. Les clusters Linux peuvent utiliser des actions de script pendant ou après la création du cluster. Pour plus d’informations, consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) et [Développement d’actions de script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

**Bootstrap**est une autre fonctionnalité de personnalisation. Pour les clusters Windows, cette fonctionnalité vous permet de spécifier l’emplacement de bibliothèques supplémentaires à utiliser avec Hive. Après la création du cluster, ces bibliothèques sont automatiquement disponibles pour une utilisation avec des requêtes Hive sans la nécessité d’utiliser `ADD JAR`.

La fonctionnalité Bootstrap pour les clusters Linux ne fournit pas cette fonction. Utilisez à la place l’action de script décrite dans [Ajouter les bibliothèques Hive lors de la création de cluster HDInsight](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Virtual Network

Les clusters HDInsight Windows fonctionnent uniquement avec les réseaux virtuels classiques tandis que les clusters HDInsight Linux nécessitent des réseaux virtuels Resource Manager. Si vous disposez de ressources dans un réseau virtuel classique auquel le cluster HDInsight Linux doit se connecter, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Pour plus d’informations sur la configuration, consultez le document [Étendre les capacités de HDInsight avec un réseau virtuel](hdinsight-extend-hadoop-virtual-network.md).

## <a name="management-and-monitoring"></a>Gestion et surveillance

La plupart des interfaces utilisateur web que vous avez peut-être utilisées avec HDInsight Windows, telles que l’historique de la tâche ou l’interface utilisateur Yarn, sont disponibles via Ambari. En outre, l’affichage Ambari Hive fournit un moyen d’exécuter des requêtes Hive à l’aide de votre navigateur web. L’interface utilisateur Web d’Ambari est disponible sur les clusters Linux à l’adresse https://CLUSTERNAME.azurehdinsight.net.

Pour plus d’informations sur l’utilisation d’Ambari, consultez les documents suivants :

* [Interface utilisateur web Ambari](hdinsight-hadoop-manage-ambari.md)
* [API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertes Ambari

Ambari dispose d’un système d’alerte pouvant vous indiquer des problèmes potentiels avec le cluster. Les alertes s’affichent en tant qu’entrées de couleur rouge ou jaune dans l’interface utilisateur web Ambari, mais vous pouvez également les récupérer via l’API REST.

> [!IMPORTANT]
> Les alertes Ambari indiquent qu’un problème se pose *peut-être* et non pas qu’un problème se pose *vraiment*. Par exemple, vous pouvez recevoir une alerte indiquant que HiveServer2 n’est pas accessible, même si vous pouvez y accéder normalement.
>
> De nombreuses alertes sont implémentées comme des requêtes basées sur un intervalle pour un service et attendent une réponse dans un intervalle de temps spécifique. Par conséquent, l’alerte ne signifie pas nécessairement que le service est arrêté, simplement qu’il n’a pas retourné de résultats dans l’intervalle de temps attendu.

## <a name="file-system-locations"></a>Emplacements du système de fichiers

Le système de fichiers des clusters Linux se présente différemment des clusters HDInsight Windows. Utilisez le tableau suivant pour trouver des fichiers couramment utilisés.

| Je dois trouver... | Il se trouve dans... |
| --- | --- |
| Configuration |`/etc`. Par exemple, `/etc/hadoop/conf/core-site.xml` |
| Fichiers journaux |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Deux répertoires figurent ici, l’un qui représente la version HDP actuelle et `current`. Le répertoire `current` contient des liens symboliques vers les fichiers et les répertoires situés dans le répertoire du numéro de version. Le répertoire `current` est fourni comme un moyen d’accès pratique aux fichiers HDP car le numéro de version change lorsque la version HDP est mise à jour. |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

En général, si vous connaissez le nom du fichier, vous pouvez utiliser la commande suivante à partir d’une session SSH pour rechercher le chemin du fichier :

    find / -name FILENAME 2>/dev/null

Vous pouvez également utiliser des caractères génériques avec le nom de fichier. Par exemple, `find / -name *streaming*.jar 2>/dev/null` retourne le chemin d’accès à tous les fichiers jar contenant le mot « streaming » dans le nom de fichier.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig et MapReduce

Les charges de travail Pig et MapReduce sont similaires sur les clusters Linux. Toutefois, les clusters HDInsight sous Linux peuvent être créés à l’aide de versions plus récentes de Hadoop, Hive et Pig. Ces différences de version peuvent introduire des modifications dans le fonctionnement de vos solutions existantes. Pour plus d’informations sur les versions des composants fournis avec HDInsight, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md).

HDInsight sous Linux ne fournit pas la fonctionnalité de bureau à distance. Au lieu de cela, vous pouvez utiliser SSH pour vous connecter à distance aux nœuds principaux du cluster. Pour plus d’informations, consultez les documents suivants :

* [Utilisation de Hive avec SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Utiliser Pig avec SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Utiliser MapReduce avec SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Si vous utilisez un metastore Hive externe, sauvegardez le metastore avant de l’utiliser avec HDInsight sous Linux. HDInsight sous Linux est disponible avec les versions plus récentes de Hive, qui peut se révéler incompatible avec les metastores créés par les versions antérieures.

Le tableau suivant fournit des conseils sur la migration de vos charges de travail Hive.

| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| **Éditeur Hive** |[Affichage Hive dans Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` pour activer Tez |Tez étant le moteur d’exécution par défaut pour les clusters Linux, l’instruction set n’est plus nécessaire. |
| Fonctions définies par l’utilisateur C# | Pour plus d’informations sur la validation des composants C# avec HDInsight sous Linux, consultez [Migrer les solutions .NET vers HDInsight sous Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts ou fichiers CMD sur le serveur appelés dans le cadre d’une tâche Hive |Scripts Bash |
| `hive` à partir du Bureau à distance |Utilisez [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) ou [Hive à partir d’une session SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| Fonctions définies par l’utilisateur C# | Pour plus d’informations sur la validation des composants C# avec HDInsight sous Linux, consultez [Migrer les solutions .NET vers HDInsight sous Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts ou fichiers CMD sur le serveur appelés dans le cadre d’une tâche Pig |Scripts Bash |

### <a name="mapreduce"></a>MapReduce

| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| Composants du mappeur et raccord de réduction C# | Pour plus d’informations sur la validation des composants C# avec HDInsight sous Linux, consultez [Migrer les solutions .NET vers HDInsight sous Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Scripts ou fichiers CMD sur le serveur appelés dans le cadre d’une tâche Hive |Scripts Bash |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Si vous utilisez un metastore Oozie externe, sauvegardez le metastore avant de l’utiliser avec HDInsight sous Linux. HDInsight sous Linux est disponible avec les versions plus récentes d’Oozie, qui peut se révéler incompatible avec les metastores créés par les versions antérieures.

Les flux de travail Oozie autorisent les actions de l’interpréteur de commandes. Les actions de l’interpréteur de commandes utilisent l’interpréteur de commandes par défaut pour permettre au système d’exploitation d’exécuter les commandes de la ligne de commande. Si vous avez des flux de travail Oozie qui s’appuient sur l’interpréteur de commandes Windows, vous devez réécrire les flux de travail de manière à ce qu’ils s’appuient sur l’environnement de l’interpréteur de commandes Linux (Bash). Pour plus d’informations sur l’utilisation des actions de l’interpréteur de commandes avec Oozie, consultez [Extension d’action d’interpréteur de commandes Oozie](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Si l’un de vos workflows utilise une application C#, validez ces applications dans un environnement Linux. Pour plus d’informations, consultez la section [Migrer des solutions .NET vers HDInsight sous Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Sur Windows, j’utilise... | Sur Linux, j’utilise... |
| --- | --- |
| Tableau de bord Storm |Le tableau de bord Storm n’est pas disponible. Pour connaître des façons de soumettre des topologies, consultez [Déploiement et gestion des topologies Apache Storm sur HDInsight Linux](storm/apache-storm-deploy-monitor-topology-linux.md) . |
| Interface utilisateur de Storm |L’interface utilisateur Storm est disponible à l’adresse https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio pour créer, déployer et gérer des topologies C# ou hybrides |Visual Studio permet de créer, déployer et gérer les topologies C# (SCP.NET) et hybrides sur Storm sous Linux sur HDInsight. Il ne peut être utilisé qu’avec des clusters créés après le 28/10/2016. |

## <a name="hbase"></a>HBase

Sur les clusters Linux, le parent ZNode pour HBase est `/hbase-unsecure`. Définissez cette valeur dans la configuration de toutes les applications clientes Java qui utilisent une API Java HBase native.

Pour obtenir un exemple de client qui définit cette valeur, consultez [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) .

## <a name="spark"></a>Spark

Les clusters Spark étaient disponibles sur les clusters Windows dans la version préliminaire. La version mise à la disponibilité générale (GA) de Spark est uniquement disponible avec les clusters Linux. Il n’existe aucun chemin de migration d’un cluster Spark Windows en version préliminaire vers un cluster Spark Linux en version finale.

## <a name="known-issues"></a>Problèmes connus

### <a name="azure-data-factory-custom-net-activities"></a>Activités .NET personnalisées Azure Data Factory

Les activités .NET personnalisées Azure Data Factory ne sont actuellement pas prises en charge sur les clusters HDInsight Linux. Au lieu de cela, vous devez utiliser l’une des méthodes suivantes pour implémenter les activités personnalisées dans le cadre de votre pipeline ADF.

* Exécutez les activités .NET sur le pool Azure Batch. Consultez la section Utilisation du service lié Azure Batch dans [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implémentez l’activité comme une activité MapReduce. Pour plus d’informations, consultez [Appeler des programmes MapReduce à partir de Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Fins de ligne

En règle générale, les fins de ligne sur les systèmes Windows utilisent CRLF, alors que les systèmes Linux utilisent LF. Vous devrez peut-être modifier les producteurs et consommateurs de données déjà présents pour assurer la compatibilité avec LF.

Par exemple, l’utilisation d’Azure PowerShell pour interroger HDInsight sur un cluster Windows retourne des données avec CRLF. La même requête avec un cluster Linux retourne LF. Procédez à un test pour voir si la fin de ligne pose problème avec votre solution avant de migrer vers un cluster Linux.

Utilisez toujours LF en fin de ligne pour les scripts qui s’exécutent sur les nœuds de cluster. Si vous utilisez CRLF, des erreurs peuvent apparaître lors de l’exécution des scripts sur un cluster Linux.

Si les scripts ne contiennent pas de chaînes comprenant des caractères CR intégrés, vous pouvez modifier en bloc les fins de ligne suivant l’une de ces méthodes :

* **Avant de charger sur le cluster** : utilisez les instructions PowerShell suivantes pour remplacer les fins de ligne CRLF par LF avant de charger le script sur le cluster.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Après avoir chargé sur le cluster** : utilisez la commande suivante à partir d’une session SSH pour le cluster basé sur Linux pour modifier le script.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Utiliser SSH pour se connecter à HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)
