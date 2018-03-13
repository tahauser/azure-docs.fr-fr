---
title: "Gérer des clusters Hadoop dans HDInsight au moyen du portail Azure | Microsoft Docs"
description: "Apprenez à créer et à gérer des clusters HDInsight avec le portail Azure."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: b0a9b808f2c1a65d10c1c12500325a9b5a34bc5a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

L’utilisation du [portail Azure][azure-portal], vous permet de gérer des clusters Hadoop dans HDInsight. Utilisez le sélecteur d’onglets ci-dessus pour obtenir des informations sur la gestion de clusters Hadoop dans HDInsight à l’aide d’autres outils.

**Configuration requise**

Pour réaliser les étapes décrites dans cet article, vous devez disposer d’un **abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Ouvrez le portail Azure
1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Une fois que vous ouvrez le portail, vous pouvez :

   * Cliquez sur **Créer une ressource** dans le menu de gauche pour créer un nouveau cluster :

       ![bouton nouveau cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Entrez **HDInsight** dans **Rechercher sur le Marketplace**, cliquez sur **HDInsight**, puis cliquez sur **Créer**.

   * Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants :

       ![bouton de cluster HDinsight du portail Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Si le bouton **Clusters HDInsight** n’est pas affiché, cliquez sur **Clusters HDInsight** sous la section **Intelligence + analyse**.


## <a name="create-clusters"></a>Créer des clusters
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md). Pour obtenir des informations générales sur la création de clusters, consultez [Création de clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

Vous devez spécifier un abonnement Azure lorsque vous créez un cluster HDInsight. Ce cluster peut être créé dans un nouveau groupe de ressources Azure ou dans un groupe de ressources existant. Vous pouvez utiliser les étapes suivantes pour vérifier vos autorisations lors de la création de clusters HDInsight :

- Pour créer un groupe de ressources :

    1. Connectez-vous au [Portail Azure](https://portal.azure.com).
    2. Cliquez sur **Abonnement** dans le menu de gauche. Il présente une icône en forme de clé jaune. Vous devriez voir une liste d’abonnements.
    3. Cliquez sur l’abonnement que vous utilisez pour créer des clusters. 
    4. Cliquez sur **Mes autorisations**.  Votre [rôle](../active-directory/role-based-access-control-what-is.md#built-in-roles) sur l’abonnement s’affiche. Vous devez avoir au moins un accès collaborateur pour créer le cluster HDInsight.

- Pour utiliser un groupe de ressources existant :

    1. Connectez-vous au [Portail Azure](https://portal.azure.com).
    2. Cliquez sur **Groupes de ressources** dans le menu de gauche pour répertorier les groupes de ressources.
    3. Cliquez sur le groupe de ressources que vous souhaitez utiliser pour la création de votre cluster HDInsight.
    4. Cliquez sur **Contrôle d’accès (IAM)**et vérifiez que vous (ou le groupe auquel vous appartenez) avez au moins un accès Collaborateur au groupe de ressources.

Si l’erreur NoRegisteredProviderFound ou MissingSubscriptionRegistration s’affiche, consultez la page [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Énumération et affichage des clusters
1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants. Si vous ne voyez pas **Clusters HDInsight**, cliquez d’abord sur **Tous les services**.
3. Cliquez sur le nom du cluster. Si la liste de clusters est longue, vous pouvez utiliser le filtre en haut de la page.
4. Double-cliquez sur un cluster dans la liste pour afficher la page de la vue d’ensemble :

    ![Éléments principaux du cluster HDInsight du portail Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)**Menu Vue d’ensemble :**
    * **Tableau de bord** : ouvre l’interface utilisateur web d’Ambari pour le cluster.
    * **Secure Shell**: affiche les instructions pour se connecter au cluster à l’aide d’une connexion Secure Shell (SSH).
    * **Mise à l’échelle du cluster**: permet de modifier le nombre de nœuds de travail pour ce cluster.
    * **Déplacer** : déplace le cluster vers un autre groupe de ressources ou un autre abonnement.
    * **Supprimer**: supprime le cluster.

    **Menu de gauche :**
    * **Journaux d’activité** : affiche et interroge les journaux d’activité.
    * **Contrôle d’accès (IAM)** : utilise les attributions de rôle.  Consultez [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md).
    * **Balises** : permet de spécifier des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée **projet**, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.
    * **Diagnostiquer et résoudre les problèmes** : affiche les informations de dépannage.
    * **Verrous** : permet d’ajouter un verrou pour empêcher la modification ou la suppression du cluster.
    * **Script d’automatisation** : permet d’afficher et d’exporter le modèle Azure Resource Manager pour le cluster. Actuellement, vous pouvez exporter uniquement le compte de stockage Azure dépendant. Consultez [Création de clusters Hadoop basés sur Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Démarrage rapide** : affiche des informations qui vous aident à prendre en main HDInsight.
    * **Outils pour HDInsight** : informations d’aide pour les outils associés à HDInsight.
    * **Subscription Core Usage** (Utilisation des cœurs d’abonnement) : permet d’afficher les cœurs disponibles pour votre abonnement.
    * **Mise à l’échelle de cluster**: augmenter et diminuer le nombre de nœuds de travail de cluster. Consultez [Mettre à l’échelle des clusters](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Connexion SSH + Cluster** : affiche les instructions pour se connecter au cluster à l’aide d’une connexion Secure Shell (SSH). Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Partenaire HDInsight**: permet d’ajouter ou de supprimer le partenaire HDInsight actuel.
    * **Metastores externes**: affiche les metastores Hive et Oozie. Les metastores ne peuvent être configurés qu’au cours du processus de création de cluster. Consultez [Utiliser un metastore Hive/Oozie](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Actions de script**: exécutent des scripts Bash sur le cluster. Consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).
    * **Applications** : permet d’ajouter/supprimer des applications HDInsight.  Consultez [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).
    * **Surveillance** : permet de surveiller le cluster dans Azure Operations Management Suite et Azure Log Analytics.
    * **Propriétés**: affichez les propriétés du cluster
    * **Comptes de stockage** : permet d’afficher les comptes de stockage et les clés. Les comptes de stockage sont configurés pendant le processus de création de cluster.
    * **Accès à Data Lake Store** : permet de configurer l’accès aux Data Lake Stores.  Consultez [Créer des clusters HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    * **Resource Health** : consultez [Vue d’ensemble d’Azure Resource Health](../service-health/resource-health-overview.md).
    * **Nouvelle demande de support** : permet de créer un ticket de support auprès du support Microsoft.
    
6. Cliquez sur **Propriétés**:

    Les propriétés sont les suivantes :

   * **Nom d’hôte**: nom du Cluster.
   * **URL du cluster** : URL de l’interface web Ambari.
   * **Secure Shell (SSH)** : nom d’utilisateur et nom d’hôte à utiliser pour accéder au cluster via SSH.
   * **État** : Abandonné, Accepté, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, En fonctionnement, En cours d’exécution, Erreur, En cours de suppression, Supprimé, TimedOut, DeleteQueued, DeleteTimedOut, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued ou ClusterCustomization.
   * **Région**: emplacement Azure. Pour obtenir la liste des emplacements Azure pris en charge, consultez la zone de liste déroulante **Région** sur [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Créé le** : date à laquelle le cluster a été déployé.
   * **Système d’exploitation** : **Windows** ou **Linux**.
   * **Type**: Hadoop, Hbase, Storm, Spark.
   * **Version**. Voir [Versions HDInsight](hdinsight-component-versioning.md).
   * **Abonnement**: nom de l’abonnement.
   * **Source de données par défaut**: système de fichiers de cluster par défaut.
   * **Taille des nœuds de travail** : taille de machine virtuelle des nœuds de travail sélectionnée.
   * **Taille du nœud principal** : taille de machine virtuelle des nœuds principaux sélectionnée.
   * **Réseau virtuel** : nom du réseau virtuel et du sous-réseau sur lesquels le cluster est déployé, si vous en avez sélectionné un au moment du déploiement.

## <a name="delete-clusters"></a>Suppression des clusters
La suppression d’un cluster ne supprime pas le compte de stockage par défaut ni les comptes de stockage liés. Vous pouvez recréer le cluster en utilisant les mêmes comptes de stockage et les mêmes metastores. Il est recommandé d’utiliser un nouveau conteneur d’objets blob par défaut quand vous recréez le cluster.

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **HDInsight Clusters** dans le menu de gauche. Si vous ne voyez pas **Clusters HDInsight**, cliquez d’abord sur **Tous les services**.
3. Cliquez sur le cluster que vous souhaitez supprimer.
4. Cliquez sur **Supprimer** dans le menu supérieur, puis suivez les instructions.

Voir aussi [Pause/arrêt de clusters](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Ajouter des comptes de stockage

Vous pouvez ajouter des comptes de stockage Azure et des comptes Azure Data Lake Store supplémentaires après la création d’un cluster. Pour plus d’informations, consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster Azure HDInsight sans avoir à recréer ce cluster.

> [!NOTE]
> Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés.  Voir [Énumération et affichage des clusters](#list-and-show-clusters).
>
>

L’impact de la modification du nombre de nœuds de données varie en fonction de chaque type de cluster pris en charge par HDInsight :

* Hadoop

    Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

    Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Ce comportement entraîne l’échec de toutes les tâches en cours d’exécution ou en attente lors de la réalisation de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.
* hbase

    Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, consultez [Prise en main d’un exemple Apache HBase dans HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Néanmoins, une fois l’opération de mise à l’échelle terminée, vous devrez rééquilibrer la topologie.

    Cela peut se faire de deux façons à l’aide de :

  * l'interface utilisateur Web de Storm
  * l’outil d’interface de ligne de commande (CLI)

    Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

    ![Rééquilibrage de mise à l’échelle HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Voici un exemple de commande CLI pour rééquilibrer la topologie Storm :

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Pour mettre à l’échelle des clusters**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **HDInsight Clusters** dans le menu de gauche.
3. Cliquez sur le cluster que vous souhaitez mettre à l’échelle.
3. Cliquez sur **Mettre à jour le cluster**.
4. Entrez une valeur dans le champ **Nombre de nœuds de travail**. La limite du nombre de nœuds de cluster varie selon les abonnements Azure. Vous pouvez contacter le support de facturation pour augmenter la limite.  Les informations de coût reflètent les modifications apportées au nombre de nœuds.

    ![HDInsight hadoop hbase storm spark mise à l’échelle](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Pause/arrêt de clusters

La plupart des travaux Hadoop sont les traitements par lots exécutés occasionnellement seulement. Pour la plupart des clusters Hadoop, il existe de longues périodes pendant lequel le cluster n’est pas utilisé pour le traitement. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation.
Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Il existe de nombreuses façons de programmer le processus :

* Utilisateur d’Azure Data Factory. Pour créer des services liés HDInsight à la demande, consultez la section [Création de clusters Hadoop à la demande basés sur Linux dans HDInsight avec Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) .
* Utilisation d’Azure PowerShell  Voir [Analyse des données sur les retards de vol](hdinsight-analyze-flight-delay-data.md).
* Utiliser l’interface de ligne de commande Microsoft Azure Voir [Gestion des clusters HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md).
* Utilisation du kit de développement logiciel .NET. Voir [Envoyer des tâches Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Pour les informations de tarification, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pour supprimer un cluster du portail, consultez [Supprimer les clusters](#delete-clusters)

## <a name="move-cluster"></a>Déplacer un cluster

Vous pouvez déplacer un cluster HDInsight vers un autre groupe de ressources Azure ou un autre abonnement.  Voir [Énumération et affichage des clusters](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Mettre à niveau des clusters

Consultez [Mettre à niveau le cluster HDInsight](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Ouvrir l’interface utilisateur web d’Ambari

Ambari fournit une interface utilisateur web de gestion Hadoop intuitive et facile à utiliser secondée par ses API RESTful. Ambari permet aux administrateurs système de gérer et de surveiller des clusters Hadoop.

1. Ouvrez un cluster HDInsight à partir du portail Azure.  Voir [Énumération et affichage des clusters](#list-and-show-clusters).
2. Cliquez sur **Tableau de bord du cluster**.

    ![Menu de cluster Hadoop HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Entrez le nom d’utilisateur et le mot de passe du cluster.  Le nom d’utilisateur du cluster par défaut est _admin_. L’interface utilisateur web d’Ambari ressemble à ceci :

    ![Interface utilisateur web d’Ambari HDInsight Hadoop](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Modifier les mots de passe
Un cluster HDInsight peut disposer de deux comptes d'utilisateur. Le nom d’utilisateur du cluster HDInsight ( le compte d’utilisateur HTTP) et le compte d’utilisateur SSH sont créés durant le processus de création du cluster. Vous pouvez utiliser l’interface utilisateur web d’Ambari pour modifier le nom d’utilisateur et le mot de passe du compte d’utilisateur du cluster, et des actions de script pour modifier le compte d’utilisateur SSH

### <a name="change-the-cluster-user-password"></a>Modifier le mot de passe d’utilisateur du cluster
Vous pouvez utiliser l’interface utilisateur web d’Ambari pour modifier le mot de passe utilisateur du cluster. Pour vous connecter à Ambari, vous devez utiliser le nom d’utilisateur et le mot de passe du cluster existants.

> [!NOTE]
> La modification du mot de passe d’utilisateur (admin) du cluster peut entraîner l’échec de l’exécution des actions de script sur ce cluster. Si des actions de script persistantes ciblent des nœuds de travail, ces scripts échouent lorsque vous ajoutez des nœuds au cluster par le biais d’opérations de redimensionnement. Pour plus d’informations sur les actions de script, consultez la section [Personnaliser des clusters HDInsight à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Connectez-vous à l’interface utilisateur web d’Ambari à l’aide des informations d’identification de l’utilisateur du cluster HDInsight. Le nom d’utilisateur par défaut est **admin**. L’URL est **https://&lt;HDInsight Cluster Name>azurehdinsight.net**.
2. Cliquez sur **Admin** dans le menu supérieur et cliquez sur « Gérer Ambari ».
3. Dans le menu de gauche, cliquez sur **Utilisateurs**.
4. Cliquez sur **Admin**.
5. Cliquez sur **Modifier le mot de passe**.

Ambari modifie ensuite le mot de passe sur tous les nœuds du cluster.

### <a name="change-the-ssh-user-password"></a>Modifier le mot de passe d’utilisateur SSH
1. À l’aide d’un éditeur de texte, enregistrez le texte suivant dans un fichier nommé **changepassword.sh**.

    > [!IMPORTANT]
    > Vous devez utiliser un éditeur qui utilise LF comme caractère de fin de ligne. Si l’éditeur utilise CRLF, le script échoue.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Chargez le fichier sur un emplacement de stockage accessible à partir de HDInsight à l’aide d’une adresse HTTP ou HTTPS. Par exemple, un magasin de fichiers public tel que le stockage d’objets blob Azure ou OneDrive. Enregistrez l’URI (adresse HTTP ou HTTPS) dans le fichier, car vous en aurez besoin à l’étape suivante.
3. À partir du portail Azure, cliquez sur **Clusters HDInsight**.
4. Cliquez sur votre cluster HDInsight.
4. Cliquez sur **Actions de script**.
4. Dans le panneau **Actions de script**, sélectionnez **Envoyer**. Lorsque le panneau **Envoyer une action de script** s’affiche, entrez les informations suivantes :

   | Champ | Valeur |
   | --- | --- |
   | NOM |Modifier le mot de passe SSH |
   | URI de script bash |URI du fichier changepassword.sh |
   | Nœuds (En-tête, Collaborateur, Nimbus, Superviseur, Zookeeper, etc.) |✓ pour tous les types de nœuds répertoriés |
   | parameters |Entrez le nom d’utilisateur SSH, puis le nouveau mot de passe. Il doit y avoir un espace entre le nom d’utilisateur et le mot de passe. |
   | Conservez cette action de script... |Laissez ce champ non coché. |
5. Sélectionnez **Créer** pour appliquer le script. Une fois le script terminé, vous pouvez vous connecter au cluster à l’aide de SSH avec le nouveau mot de passe.

## <a name="grantrevoke-access"></a>Octroyer/Révoquer l’accès
Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer ou octroyer l’accès à l’aide de l’[interface de ligne de commande Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) ou d’[Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Rechercher l’ID d’abonnement

**Pour rechercher vos ID d’abonnement Azure**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Abonnements**. Chaque abonnement a un nom et un ID.

Chaque cluster est lié à un abonnement Azure. L’ID d’abonnement est indiqué dans la vignette **Essential** du cluster. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Trouvez le groupe de ressources
En mode Azure Resource Manager, chaque cluster HDInsight est créé avec un groupe de ressources Azure Resource Manager. Le groupe de ressources Azure Resource Manager appartenant à un cluster apparaît dans :

* La liste de clusters comporte une colonne **Groupe de ressources** .
* Mosaïque **Essential** du cluster.  

Voir [Énumération et affichage des clusters](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Rechercher les comptes de stockage

Les clusters HDInsight utilisent un compte Stockage Azure ou un magasin Azure Data Lake Store pour stocker les données. Chaque cluster HDInsight peut avoir un compte de stockage par défaut et une série de comptes de stockage liés. Pour répertorier les comptes de stockage, vous ouvrez le cluster à partir du portail, puis vous cliquez sur **Comptes de stockage** :

![Comptes de stockage du cluster HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

La capture d’écran précédente comporte une colonne __Default__ (Par défaut) qui indique si le compte est le compte de stockage par défaut.

Pour répertorier les comptes Data Lake Store, cliquez sur **Data Lake Store access** (Accès à Data Lake Store) dans la capture d’écran précédente.

## <a name="run-hive-queries"></a>Exécuter des requêtes Hive
Vous ne pouvez pas exécuter un travail Hive directement à partir du portail Azure. À la place, utilisez l’affichage Hive dans l’interface utilisateur web d’Ambari.

**Pour exécuter des requêtes Hive à l’aide de l’affichage Hive d’Ambari**

1. Connectez-vous à l’interface utilisateur web d’Ambari à l’aide des informations d’identification de l’utilisateur du cluster HDInsight. Le nom d’utilisateur par défaut est **admin**. L’URL est **https://&lt;HDInsight Cluster Name>azurehdinsight.net**.
2. Ouvrez l’affichage Hive comme illustré dans la capture d’écran suivante :  

    ![Affichage Hive dans HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Cliquez sur **Requête** dans le menu supérieur.
4. Entrez une requête Hive dans l’**éditeur de requête**, puis cliquez sur **Exécuter**.

## <a name="monitor-jobs"></a>Surveiller des travaux
Consultez la section [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Parcourir les fichiers
Vous pouvez utiliser le portail Azure pour parcourir le contenu du conteneur par défaut.

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants.
3. Cliquez sur le nom du cluster. Si la liste de clusters est longue, vous pouvez utiliser le filtre en haut de la page.
4. Cliquez sur **Comptes de stockage** à partir du menu de gauche du cluster.
5. Cliquez sur un compte de stockage.
7. Cliquez sur la vignette **Objets blob** .
8. Cliquez sur le nom du conteneur par défaut.

## <a name="monitor-cluster-usage"></a>Surveiller l’utilisation du cluster
La section **Utilisation** du panneau du cluster HDInsight affiche des informations sur le nombre de mémoires à tore disponibles pour votre abonnement à utiliser avec HDInsight, ainsi que le nombre de mémoires à tore magnétique affectées à ce cluster et la façon dont elles sont attribuées aux nœuds de ce cluster. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

> [!IMPORTANT]
> Pour surveiller les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Se connecter à un cluster

* [Utilisation de Hive avec HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert certaines fonctions d’administration de base. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Création de clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [En savoir plus sur l’utilisation de l’interface utilisateur web Ambari](hdinsight-hadoop-manage-ambari.md)
* [En savoir plus sur l’utilisation de l’API Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Utilisation d'Hive dans HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation de Pig dans HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation de Sqoop dans HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Prise en main d’Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Ligne de commande Hadoop"
