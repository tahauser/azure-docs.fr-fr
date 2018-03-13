---
title: "Gérer des clusters Hadoop Windows dans HDInsight au moyen du portail Azure | Microsoft Docs"
description: "Découvrez la façon d'administrer le service HDInsight. Créez un cluster HDInsight, ouvrez la console JavaScript interactive et ouvrez la console de commandes Hadoop."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0b34f861b306dc62cbdbf97378a1c370494bbb20
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gérer des clusters Hadoop Windows dans HDInsight à l'aide du portail Azure

Le [portail Azure][azure-portal] vous permet de créer des clusters Hadoop Windows dans Azure HDInsight, de modifier le mot de passe utilisateur Hadoop et d’activer le protocole RDP (Remote Desktop Protocol) afin d’accéder à la console de commandes Hadoop sur le cluster.

Les informations mentionnées dans cet article s’appliquent uniquement aux clusters HDInsight basés sur Windows. Pour plus d’informations sur la gestion des clusters basés sur Linux, voir [Gérer des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-portal-linux.md).

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>configuration requise

Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Compte Azure Storage** - Le cluster HDInsight utilise un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Pour plus d’informations sur l’expérience transparente offerte par le stockage d’objets blob Azure avec les clusters HDInsight, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la page [Création d’un compte de stockage](../storage/common/storage-create-storage-account.md).

## <a name="open-the-portal"></a>Ouvrir le portail
1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Une fois que vous ouvrez le portail, vous pouvez :

   * Cliquez sur **Créer une ressource** dans le menu de gauche pour créer un nouveau cluster :

       ![bouton nouveau cluster HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * Cliquez sur **HDInsight Clusters** dans le menu de gauche.

       ![bouton de cluster HDinsight du portail Azure](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     Si **HDInsight** n’apparaît pas dans le menu de gauche, cliquez sur **Parcourir**.

     ![bouton de cluster Parcourir du portail Azure](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>Créer des clusters
Pour les instructions de création à l’aide du portail, consultez [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez la rubrique [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md). Vous pouvez personnaliser HDInsight en utilisant l’une des options suivantes :

* Utilisez Action de script pour exécuter des scripts personnalisés pouvant personnaliser un cluster afin de modifier la configuration de cluster ou d’installer des composants personnalisés tels que Giraph ou Solr. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md).
* Utilisez les paramètres de personnalisation de cluster du kit de développement logiciel (SDK) HDInsight .NET ou Azure PowerShell pendant la création du cluster. Les modifications apportées à la configuration sont ainsi conservées pendant toute la durée de vie du cluster et ne sont pas affectées par les réinitialisations des nœuds du cluster qu’exécute régulièrement la plateforme Azure à des fins de maintenance. Pour plus d’informations sur l’utilisation des paramètres de personnalisation des clusters, consultez la rubrique [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Certains composants Java natifs, comme Mahout et Cascading, peuvent être exécutés sur le cluster en tant que fichiers JAR. Ces derniers peuvent être distribués au stockage d’objets blob Azure et envoyés aux clusters HDInsight à l’aide des mécanismes d’envoi de tâches Hadoop. Pour plus d’informations, consultez la rubrique [Envoi de tâches Hadoop par programme](hadoop/submit-apache-hadoop-jobs-programmatically.md).

  > [!NOTE]
  > En cas de problèmes lors du déploiement ou de l’appel des fichiers JAR sur les clusters HDInsight, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).
  >
  > Cascading n'est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight](hdinsight-component-versioning.md).
  >
  >

L’installation de logiciels personnalisés sur le cluster à l’aide d’une connexion Bureau à distance n’est pas prise en charge. Évitez de stocker des fichiers sur les disques du nœud principal, car vous les perdrez si vous devez recréer les clusters. Il est recommandé de stocker les fichiers sur le stockage d’objets blob Azure. Le stockage d'objets blob est permanent.

## <a name="list-and-show-clusters"></a>Énumération et affichage des clusters
1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **HDInsight Clusters** dans le menu de gauche.
3. Cliquez sur le nom du cluster. Si la liste de clusters est longue, vous pouvez utiliser le filtre en haut de la page.
4. Double-cliquez sur un cluster dans la liste pour afficher les détails.

    **Menu et essentials**:

    ![essentials du cluster HDInsight du portail Azure](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * Pour personnaliser le menu, cliquez avec le bouton droit sur le menu, puis sur **Personnaliser**.
   * **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.
   * **Tableau de bord**, **Tableau de bord du cluster** et **URL : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est une interface Ambari Web pour les clusters basés sur Linux. -**Secure Shell** : affiche les instructions pour se connecter au cluster à l’aide d’une connexion Secure Shell (SSH).
   * **Mise à l’échelle du cluster**: permet de modifier le nombre de nœuds de travail pour ce cluster.
   * **Supprimer**: supprime le cluster.
   * **Démarrage rapide** : affiche des informations qui vous aideront à prendre en main HDInsight.
   * **Utilisateurs : permet de définir des autorisations pour la *gestion via le portail* de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

     > [!IMPORTANT]
     > Ce paramètre affecte *uniquement* les autorisations d’accès à ce cluster dans le portail Azure et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.
     >
     >
   * **Balises** : permet de spécifier des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée **projet**, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.
   * **Vues Ambari** : liens vers Ambari Web.

     > [!IMPORTANT]
     > Pour gérer les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, consultez [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).
     >
     >

     **Utilisation**:

     ![utilisation de cluster HDInsight du portail Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. Cliquez sur **Settings**.

    ![utilisation de cluster HDInsight du portail Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **Propriétés**: affichez les propriétés du cluster
   * **Identité AAS de cluster**:
   * **Clés de stockage Azure**: permet d’afficher le compte de stockage par défaut et sa clé. Le compte de stockage est configuré pendant le processus de création de cluster.
   * **Connexion de cluster**: modifie le nom d’utilisateur de cluster et le mot de passe HTTP.
   * **Metastores externes**: affiche les metastores Hive et Oozie. Les metastores ne peuvent être configurés qu’au cours du processus de création de cluster.
   * **Mise à l’échelle de cluster**: augmenter et diminuer le nombre de nœuds de travail de cluster.
   * **Bureau à distance**: active et désactive l’accès au bureau à distance (RDP) et configure le nom d’utilisateur du bureau à distance.  Le nom d’utilisateur du bureau à distance doit être différent du nom d’utilisateur HTTP.
   * **Partenaire d’enregistrement**:

     > [!NOTE]
     > Ceci est une liste générique des paramètres disponibles. Ils ne sont pas tous présents pour tous les types de clusters.
     >
     >
6. Cliquez sur **Propriétés**:

    La section des propriétés comprend les éléments suivants :

   * **Nom d’hôte**: nom du Cluster.
   * **URL de cluster**.
   * **État**: inclut Abandonné, Accepté, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, En fonctionnement, En cours d’exécution, Erreur, En cours de suppression, Supprimé, TimedOut, DeleteQueued, DeleteTimedOut, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Région**: emplacement Azure. Pour obtenir la liste des emplacements Azure pris en charge, consultez la zone de liste déroulante **Région** sur [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Données créées**.
   * **Système d’exploitation** : **Windows** ou **Linux**.
   * **Type**: Hadoop, Hbase, Storm, Spark.
   * **Version**. Voir [Versions HDInsight](hdinsight-component-versioning.md)
   * **Abonnement**: nom de l’abonnement.
   * **ID d’abonnement**.
   * **Source de données principale**. Le compte de stockage d’objets blobs utilisé en tant que système de fichier Hadoop par défaut.
   * **Niveau de tarification des nœuds de travail**.
   * **Niveau de tarification du nœud principal**.

## <a name="delete-clusters"></a>Suppression des clusters
La suppression d’un cluster ne supprime pas le compte de stockage par défaut ou les comptes de stockage liés. Vous pouvez recréer le cluster en utilisant les mêmes comptes de stockage et les mêmes metastores.

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Supprimer** dans le menu supérieur, puis suivez les instructions.

Voir aussi [Pause/arrêt de clusters](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

> [!NOTE]
> Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés.  Voir [Énumération et affichage des clusters](#list-and-show-clusters).
>
>

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

* Hadoop

    Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

    Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.
* hbase

    Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, voir []
* Storm

    Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

    Cela peut se faire de deux façons à l’aide de :

  * l'interface utilisateur Web de Storm
  * l’outil d’interface de ligne de commande (CLI)

    Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

    ![HDInsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-management-portal/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Pour mettre à l’échelle des clusters**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Mettre le cluster à l’échelle**.
4. Entrez une valeur dans le champ **Nombre de nœuds de travail**. La limite du nombre de nœuds de cluster varie selon les abonnements Azure. Vous pouvez contacter le support de facturation pour augmenter la limite.  Les informations de coût reflètent les modifications apportées au nombre de nœuds.

    ![HDInsight Hadoop HBase Storm Spark mise à l’échelle](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pause/arrêt de clusters
La plupart des travaux Hadoop sont les traitements par lots exécutés occasionnellement seulement. Pour la plupart des clusters Hadoop, il existe de longues périodes pendant lequel le cluster n’est pas utilisé pour le traitement. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation.
Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Il existe de nombreuses façons de programmer le processus :

* Utilisateur d’Azure Data Factory. Voir [Service lié Azure HDInsight](../data-factory/compute-linked-services.md) et [Transformation et analyse en utilisant Azure Data Factory](../data-factory/transform-data.md) pour les services liés HDInsight à la demande et auto-définis.
* Utilisation d’Azure PowerShell  Voir [Analyse des données sur les retards de vol](hdinsight-analyze-flight-delay-data.md).
* Utiliser l’interface de ligne de commande Microsoft Azure Voir [Gestion des clusters HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md).
* Utilisation du kit de développement logiciel .NET. Voir [Envoyer des tâches Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Pour les informations de tarification, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pour supprimer un cluster du portail, consultez [Supprimer les clusters](#delete-clusters)

## <a name="change-cluster-username"></a>Modifier le nom d’utilisateur du cluster
Un cluster HDInsight peut disposer de deux comptes d'utilisateur. Le compte d’utilisateur du cluster HDInsight est créé lors du processus de création. Vous pouvez également créer un compte d’utilisateur RDP pour accéder au cluster via RDP. Consultez la rubrique [Activation du Bureau à distance](#connect-to-hdinsight-clusters-by-using-rdp).

**Modification du nom d’utilisateur et du mot de passe d’un cluster HDInsight**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Connexion du cluster**.
4. Si **Connexion du cluster** est activé, vous devez cliquer sur **Désactiver**, puis sur **Activer** avant de modifier le nom d’utilisateur et le mot de passe.
5. Modifiez le **Nom de connexion du cluster** et/ou le **Mot de passe de connexion du cluster**, puis cliquez sur **Enregistrer**.

    ![HDInsight modifier cluster utilisateur nom d’utilisateur mot de passe utilisateur http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>Octroyer/Révoquer l’accès
Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer/octroyer l’accès à partir du portail Azure.

> [!NOTE]
> En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.
>
>

**Pour octroyer/révoquer l’accès aux services web HTTP**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Connexion du cluster**.
4. Si **Connexion du cluster** est activé, vous devez cliquer sur **Désactiver**, puis sur **Activer** avant de modifier le nom d’utilisateur et le mot de passe.
5. Pour **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster**, entrez les nouveaux nom d’utilisateur et mot de passe (respectivement) du cluster.
6. Cliquez sur **ENREGISTRER**.

    ![HDInsight octroi suppression accès au service web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>Trouvez le compte de stockage par défaut
Chaque cluster HDInsight dispose d’un compte de stockage par défaut. Le compte de stockage par défaut et ses clés pour un cluster se trouvent sous **Paramètres**/**Propriétés**/**Clés de stockage Azure**. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Trouvez le groupe de ressources
En mode Azure Resource Manager, chaque cluster HDInsight est créé avec un groupe de ressources Azure. Le groupe de ressources Azure appartenant à un cluster apparaît dans :

* La liste de clusters comporte une colonne **Groupe de ressources** .
* Mosaïque **Essential** du cluster.  

Voir [Énumération et affichage des clusters](#list-and-show-clusters).

## <a name="open-hdinsight-query-console"></a>Ouvrir la console de requête HDInsight
La console de la requête HDInsight inclut les fonctionnalités suivantes :

* **Éditeur Hive**: interface web GUI pour l’envoi de tâches Hive.  Voir [Exécution de requêtes Hive à l’aide de la console de requêtes](hadoop/apache-hadoop-use-hive-query-console.md).

    ![éditeur hive de portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **Historique des tâches**: surveillez les tâches Hadoop.  

    ![historique de travail de portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    Cliquez sur **Nom de la requête** pour afficher les détails, notamment les propriétés du travail, la **requête du travail** et le **résultat du travail. Vous pouvez également télécharger la requête et la sortie sur votre station de travail.
* **Explorateur de fichiers**: accédez au compte de stockage par défaut et aux comptes de stockage liés.

    ![parcourir l’explorateur de fichier de portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    Sur la capture d’écran, le type **<Account>** indique que l’élément est un compte de stockage Azure.  Cliquez sur le nom de compte pour parcourir les fichiers.
* **Interface utilisateur Hadoop**.

    ![Interface utilisateur Hadoop du portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    À partir de **Interface utilisateur Hadoop*, vous pouvez parcourir les fichiers et consulter les journaux.
* **Interface utilisateur Yarn**.

    ![Interface utilisateur YARN du portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Exécuter des requêtes Hive
Pour exécuter des travaux Hive à partir du portail, cliquez sur **Éditeur Hive** dans la console de requête HDInsight. Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

## <a name="monitor-jobs"></a>Surveiller des travaux
Pour surveiller les tâches à partir du portail, cliquez sur **Historique des tâches** dans la console de requête HDInsight. Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

## <a name="browse-files"></a>Parcourir les fichiers
Pour rechercher des fichiers stockés dans le compte de stockage par défaut et les comptes de stockage liés, cliquez sur **Explorateur de fichiers** dans la console de requête HDInsight. Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

Vous pouvez également utiliser l’utilitaire **Parcourir le système de fichiers** à partir de **l’interface utilisateur Hadoop** dans la console HDInsight.  Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

## <a name="monitor-cluster-usage"></a>Surveiller l’utilisation du cluster
La section **Utilisation** du panneau du cluster HDInsight affiche des informations sur le nombre de mémoires à tore disponibles pour votre abonnement à utiliser avec HDInsight, ainsi que le nombre de mémoires à tore magnétique affectées à ce cluster et la façon dont elles sont attribuées aux nœuds de ce cluster. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

> [!IMPORTANT]
> Pour surveiller les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Ouvrez l’interface utilisateur Hadoop
Pour surveiller le cluster, parcourez le système de fichiers et vérifiez les journaux, puis cliquez sur **Interface utilisateur Hadoop** dans la console de requête HDInsight. Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

## <a name="open-yarn-ui"></a>Ouvrez l’interface utilisateur Yarn
Pour utiliser l'interface utilisateur Yarn, cliquez sur **Interface utilisateur Yarn** dans la console de requête HDInsight. Voir [Ouvrir la console de requête HDInsight](#open-hdinsight-query-console).

## <a name="connect-to-clusters-using-rdp"></a>Connexion à des clusters à l’aide du bureau à distance
Les informations d’identification du cluster fournies lors de sa création donnent accès aux services disponibles sur le cluster, mais pas au cluster proprement dit via le Bureau à distance. Vous pouvez activer l’accès Bureau à distance lorsque vous approvisionnez un cluster ou une fois l’approvisionnement effectué. Pour obtenir des instructions sur l’activation du Bureau à distance lors de la création, voir [Créer un cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

**Activation du Bureau à distance**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Bureau à distance**.
4. Renseignez les champs **Expiration**, **Nom d’utilisateur de bureau à distance** et **Mot de passe de bureau à distance**, puis cliquez sur **Activer**.

    ![HDInsight activer désactiver configurer Bureau à distance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    La valeur par défaut pour le champ Expiration est d’une semaine.

   > [!NOTE]
   > Vous pouvez également utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour activer le Bureau à distance sur un cluster. Utilisez la méthode **EnableRdp** sur l’objet client HDInsight de la manière suivante : **client.EnableRdp(nom cluster, emplacement, "utilisateur rdp", "mot de passe rdp", DateTime.Now.AddDays(6))**. De la même manière, pour désactiver le Bureau à distance sur le cluster, vous pouvez utiliser **client.DisableRdp(nom cluster, emplacement)**. Pour plus d’informations sur ces méthodes, consultez la rubrique [Référence du Kit de développement logiciel (SDK) HDInsight .NET](http://go.microsoft.com/fwlink/?LinkId=529017). Cela s’applique uniquement aux clusters HDInsight fonctionnant sous Windows.
   >
   >

**Pour vous connecter à un cluster à l’aide de RDP**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Bureau à distance**.
4. Cliquez sur **Connexion** , puis suivez les instructions. Si la connexion est désactivée, vous devez d’abord l’activer. Assurez-vous de bien utiliser le nom d’utilisateur et le mot de passe du Bureau à distance.  Vous ne pouvez pas utiliser les informations d’identification utilisateur du cluster.

## <a name="open-hadoop-command-line"></a>Ouverture de la ligne de commande Hadoop
Pour vous connecter au cluster à l’aide du Bureau à distance et utiliser la ligne de commande Hadoop, vous devez d’abord activer l’accès Bureau à distance sur le cluster, comme décrit à la section précédente.

**Pour ouvrir une ligne de commande Hadoop**

1. Connectez-vous au cluster en utilisant le Bureau à distance.
2. À partir du Bureau, double-cliquez sur **Hadoop Command Line**.

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Pour plus d’informations sur les commandes Hadoop, consultez la rubrique [Référence aux commandes Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Sur la capture d’écran précédente, le numéro de la version d’Hadoop est intégré au nom du dossier. Il peut changer en fonction de la version des composants Hadoop installés sur le cluster. Vous pouvez utiliser des variables d'environnement Hadoop pour faire référence à ces dossiers. Par exemple : 

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer un cluster HDInsight à l’aide du portail et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Envoi de tâches Hadoop par programme](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Ligne de commande Hadoop"
