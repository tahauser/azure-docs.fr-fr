---
title: Hue avec Hadoop sur les clusters HDInsight basés sur Linux - Azure | Documents Microsoft
description: Apprenez comment installer Hue sur les clusters HDInsight et utiliser le tunnel pour acheminer les demandes de Hue. Utilisez Hue pour parcourir le stockage et exécuter Hive ou Pig.
keywords: hue hadoop
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 5cef5e72af8a8b7c007b688b029f875e89d163ae
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installation et utilisation de Hue sur des clusters HDInsight Hadoop

Apprenez comment installer Hue sur les clusters HDInsight et utiliser le tunnel pour acheminer les demandes de Hue.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-is-hue"></a>Qu’est-ce que Hue ?
Hue est un ensemble d’applications web permettant d’interagir avec un cluster Hadoop. Vous pouvez utiliser Hue pour parcourir le stockage associé à un cluster Hadoop (WASB, dans le cas de clusters HDInsight), exécuter les travaux Hive et les scripts Pig, etc. Les composants suivants sont disponibles avec l’installation de Hue sur un cluster HDInsight Hadoop.

* Éditeur Beeswax Hive
* Pig
* Gestionnaire de Metastore
* Oozie
* FileBrowser (qui communique avec le conteneur WASB par défaut)
* Explorateur de travaux

> [!WARNING]
> Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Installer Hue à l’aide d’actions de script

Le script d’installation de Hue sur un cluster HDInsight basé sur Linux est disponible à l’adresse https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Vous pouvez utiliser ce script pour installer Hue sur des clusters avec Azure Storage Blobs (WASB) ou Azure Data Lake Store comme stockage par défaut.

Cette section explique comment utiliser le script lors de l’approvisionnement du cluster à l’aide du portail Azure.

> [!NOTE]
> Azure PowerShell, l'interface de ligne de commande Azure (CLI), le Kit de développement logiciel (SDK) .NET HDInsight ou les modèles Azure Resource Manager peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer les actions de script aux clusters qui sont déjà en cours d’exécution. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Démarrez l’approvisionnement d’un cluster à l’aide de la procédure décrite dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md), mais n’achevez pas l’approvisionnement.

   > [!NOTE]
   > Pour installer Hue sur des clusters HDInsight, il est recommandé d’utiliser une taille de nœud principal égale ou supérieure à A4 (8 cœurs, 14 Go de mémoire).
   >
   >
2. Dans le panneau **Configuration facultative**, sélectionnez **Actions de script**, puis indiquez les informations comme spécifié ci-dessous :

    ![Fournir des paramètres d’action de script pour Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "Fournir des paramètres d’action de script pour Hue")

   * **NAME**: saisissez un nom convivial pour l’action de script.
   * **URI DU SCRIPT** : https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: cochez cette option.
   * **WORKER** : laissez ce champ vide.
   * **ZOOKEEPER** : laissez ce champ vide.
   * **PARAMÈTRES** : laissez ce champ vide.
3. En bas de l’écran **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** au bas du panneau **Configuration facultative** pour enregistrer les informations de configuration facultatives.
4. Continuez l’approvisionnement du cluster, comme indiqué dans [Approvisionner des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Utilisez Hue avec les clusters HDInsight

Le tunneling SSH est le seul moyen d’accéder à Hue sur le cluster une fois qu’il est en cours d’exécution. Le tunneling via SSH autorise le trafic à atteindre directement le nœud principal du cluster exécutant Hue. Une fois que le cluster a terminé l’approvisionnement, procédez comme suit pour utiliser Hue sur un cluster HDInsight Linux.

> [!NOTE]
> Nous vous recommandons de suivre les instructions ci-dessous sur le navigateur web Firefox.
>
>

1. Utilisez les informations de la section [Utilisation de SSH Tunneling pour accéder à l’interface web Ambari, ResourceManager, JobHistory, NameNode, Oozie et d’autres interfaces web](hdinsight-linux-ambari-ssh-tunnel.md) pour créer un tunnel SSH entre votre système client et le cluster HDInsight, puis configurer votre navigateur web pour utiliser le tunnel SSH en tant que proxy.

2. Une fois que vous avez créé un tunnel SSH et configuré votre navigateur pour y rediriger le trafic par proxy, vous devez trouver le nom d’hôte du nœud principal primaire. Pour ce faire, connectez-vous au cluster à l’aide de SSH sur le port 22. Par exemple, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` où **USERNAME** est votre nom d’utilisateur SSH et **CLUSTERNAME** est le nom de votre cluster.

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Une fois connecté, utilisez la commande suivante pour obtenir le nom de domaine complet du nœud principal primaire :

        hostname -f

    Cette commande renvoie un nom similaire à ce qui suit :

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Il s’agit du nom d’hôte du nœud principal primaire sur lequel se trouve le site web Hue.
4. Utilisez le navigateur pour ouvrir le portail Hue à l’adresse http://HOSTNAME:8888. Remplacez HOSTNAME par le nom que vous avez obtenu à l’étape précédente.

   > [!NOTE]
   > Lorsque vous vous connectez pour la première fois, vous êtes invité à créer un compte pour vous connecter au portail Hue. Les informations d’identification que vous spécifiez ici seront limitées au portail et ne sont pas liées à l’administrateur ou aux informations d’identification de l’utilisateur SSH spécifié lors de la configuration du cluster.
   >
   >

    ![Se connecter au portail Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Spécifier les informations d’identification pour le portail Hue")

### <a name="run-a-hive-query"></a>Exécution d'une tâche Hive
1. À partir du portail Hue, cliquez sur **Éditeurs de requête**, puis cliquez sur **Hive** pour ouvrir l’éditeur Hive.

    ![Utiliser Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Utiliser Hive")
2. Sous l’onglet **Aide**, sous **Base de données**, vous devez voir **hivesampletable**. Il s’agit d’une table d’échantillon qui est livrée avec tous les clusters Hadoop sur HDInsight. Saisissez un exemple de requête dans le volet de droite et vérifiez la sortie sur l’onglet **Résultats** dans le volet inférieur ci-dessous, comme illustré dans la capture d’écran.

    ![Exécuter la requête Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Exécuter la requête Hive")

    Vous pouvez également utiliser l’onglet **Graphique** pour afficher une représentation visuelle du résultat.

### <a name="browse-the-cluster-storage"></a>Parcourir le stockage de cluster
1. À partir du portail Hue, cliquez sur **Explorateur de fichiers** dans l’angle supérieur droit de la barre de menus.
2. Par défaut, l’explorateur de fichiers s’ouvre sur le répertoire **/user/myuser** . Cliquez sur la barre oblique située avant le répertoire de l’utilisateur dans le chemin d’accès pour accéder à la racine du conteneur de stockage Azure associé au cluster.

    ![Utiliser l’explorateur de fichiers](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Utiliser l’explorateur de fichiers")
3. Cliquez avec le bouton droit sur un fichier ou un dossier pour afficher les opérations disponibles. Utilisez le bouton **Télécharger** situé dans le coin droit pour télécharger des fichiers dans le répertoire actif. Utilisez le bouton **Nouveau** pour créer des fichiers ou répertoires.

> [!NOTE]
> L’Explorateur de fichiers Hue peut afficher uniquement le contenu du conteneur par défaut associé au cluster HDInsight. Les comptes/conteneurs de stockage supplémentaires que vous avez peut-être associés au cluster ne seront pas accessibles via l’Explorateur de fichiers. Toutefois, les autres conteneurs associés au cluster seront toujours accessibles pour les travaux Hive. Par exemple, si vous saisissez la commande `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` dans l’éditeur Hive, vous pouvez voir le contenu des conteneurs supplémentaires. Dans cette commande, **newcontainer** n’est pas le conteneur par défaut associé à un cluster.
>
>

## <a name="important-considerations"></a>Points importants à prendre en compte
1. Le script utilisé pour installer Hue exécute l’opération uniquement sur le nœud principal primaire du cluster.

2. Pendant l’installation, plusieurs services Hadoop (HDFS, fils, RM2, Oozie) sont redémarrés pour mettre à jour la configuration. Une fois que le script a terminé l’installation de Hue, le démarrage d’autres services Hadoop peut prendre du temps. Cela peut affecter dans un premier temps les performances de Hue. Une fois que tous les services ont démarré, Hue est complètement fonctionnel.
3. Teinte ne comprend pas les travaux Tez, qui est la valeur par défaut de Hive. Si vous souhaitez utiliser MapReduce comme moteur d’exécution de Hive, mettez à jour le script pour utiliser la commande suivante dans votre script :

        set hive.execution.engine=mr;

4. Avec les clusters Linux, vous pouvez avoir un scénario dans lequel vos services fonctionnent sur le nœud principal primaire alors que le Gestionnaire de ressources s’exécute sur le nœud principal secondaire. Un tel scénario peut entraîner des erreurs (illustrées ci-dessous) lors de l’utilisation de Hue pour afficher les détails des travaux EN COURS sur le cluster. Toutefois, vous pouvez afficher les détails du travail lorsque la tâche est terminée.

   ![Erreur de portail Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Erreur de portail Hue")

   Il s’agit d’un problème connu. Pour résoudre ce problème, modifiez Ambari afin que le Gestionnaire de ressources actif s’exécute également sur le nœud principal primaire.
5. Hue connaît WebHDFS, tandis que les clusters HDInsight utilisent le stockage Azure à l’aide de `wasb://`. Par conséquent, le script personnalisé utilisé avec l’action de script installe WebWasb, qui est un service compatible WebHDFS permettant de communiquer avec WASB. Donc, bien que le portail Hue indique HDFS à certains endroits (comme lorsque vous déplacez votre souris sur l’ **Explorateur de fichiers**), il doit être compris comme WASB.

## <a name="next-steps"></a>Étapes suivantes
* [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.
* [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d’effectuer de puissantes opérations de recherche sur des données stockées.
* [Installation de R sur des clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Utilisez la personnalisation de clusters pour installer R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
