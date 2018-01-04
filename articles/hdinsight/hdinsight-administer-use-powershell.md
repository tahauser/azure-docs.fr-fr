---
title: Gestion des clusters Hadoop dans HDInsight avec PowerShell - Azure | Microsoft Docs
description: "Découvrez comment effectuer des tâches d'administration pour les clusters Hadoop dans HDInsight au moyen d'Azure PowerShell."
services: hdinsight
editor: cgronlun
manager: jhubbard
tags: azure-portal
author: mumian
documentationcenter: 
ms.assetid: bfdfa754-18e5-4ef9-b0d6-2dbdcebc0283
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: 04bb16d4779741d71f686f53a8f325243ab8499e
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gestion des clusters Hadoop dans HDInsight au moyen d’Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell permet de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Azure. Dans cet article, vous allez apprendre à gérer des clusters Hadoop dans Azure HDInsight à l’aide d’Azure PowerShell. Pour la liste des applets de commande PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight][hdinsight-powershell-reference].

**Composants requis**

Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="install-azure-powershell"></a>Installation d'Azure PowerShell
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Si vous avez installé Azure PowerShell version 0.9x, vous devez le désinstaller avant d’installer une version plus récente.

Pour connaître la version de PowerShell installée :

```powershell
Get-Module *azure*
```

Pour désinstaller l’ancienne version, exécutez Programmes et fonctionnalités dans le Panneau de configuration.

## <a name="create-clusters"></a>Créer des clusters
Consultez la page [Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Énumérer les clusters
Utilisez la commande suivante pour afficher la liste de tous les clusters de l’abonnement actif :

```powershell
Get-AzureRmHDInsightCluster
```

## <a name="show-cluster"></a>Afficher le cluster
Utilisez la commande suivante pour afficher les détails d’un cluster spécifique dans l’abonnement actif :

```powershell
Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Suppression des clusters
Utilisez la commande suivante pour supprimer un cluster :

```powershell
Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>
```

Vous pouvez également supprimer un cluster en supprimant le groupe de ressources qui le contient. Supprimer un groupe de ressources supprime toutes les ressources dans le groupe, notamment le compte de stockage par défaut.

```powershell
Remove-AzureRmResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

> [!NOTE]
> Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés.  Voir [Énumération et affichage des clusters](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
>
>

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

* Hadoop

    Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

    Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Le redémarrage des services entraîne l’échec de toutes les tâches en cours ou en attente durant la réalisation de l’opération de mise à l’échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.
* hbase

    Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster, puis exécuter les commandes suivantes à partir d’une fenêtre d’invite de commandes :

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Storm

    Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

    Cela peut se faire de deux façons à l’aide de :

  * l'interface utilisateur Web de Storm
  * l’outil d’interface de ligne de commande (CLI)

    Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

    ![HDInsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

    Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Pour modifier la taille du cluster Hadoop à l’aide d’Azure PowerShell, exécutez la commande suivante depuis un ordinateur client :

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Octroyer/Révoquer l’accès
Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour révoquer :

```powershell
Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Pour octroyer :

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]
> En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.
>
>

L’octroi et la révocation de l’accès sont également possibles par le biais du portail. Consultez [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

## <a name="update-http-user-credentials"></a>Mettre à jour les informations d’identification de l’utilisateur HTTP
Il s’agit de la même procédure que [l’octroi et la révocation de l’accès HTTP](#grant/revoke-access). Si l’accès HTTP a été octroyé au cluster, vous devez d’abord le révoquer.  Octroyez ensuite l’accès avec les informations d’identification de l’utilisateur HTTP.

## <a name="find-the-default-storage-account"></a>Trouvez le compte de stockage par défaut
Le script PowerShell suivant montre comment obtenir le nom de compte de stockage par défaut et les informations connexes :

```powershell
#Login-AzureRmAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Trouvez le groupe de ressources
En mode Resource Manager, chaque cluster HDInsight appartient à un groupe de ressources Azure.  Pour rechercher le groupe de ressources :

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Soumettre les travaux
**Pour envoyer des tâches MapReduce**

Consultez [Exécution des exemples Hadoop MapReduce dans HDInsight basé sur Windows](hdinsight-run-samples.md)

**Pour envoyer des tâches Hive**

Consultez [Exécution de requêtes Hive avec PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Pour envoyer des tâches Pig**

Consultez [Exécution de tâches Pig avec PowerShell](hadoop/apache-hadoop-use-pig-powershell.md).

**Pour envoyer des tâches Sqoop**

Consultez l'article [Utilisation de Sqoop avec HDInsight](hadoop/hdinsight-use-sqoop.md).

**Pour envoyer des tâches Oozie**

Consultez [Utilisation d’Oozie avec Hadoop pour définir et exécuter un workflow dans HDInsight](hdinsight-use-oozie.md).

## <a name="upload-data-to-azure-blob-storage"></a>Téléchargement de données vers le stockage d'objets blob Azure
Consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Voir aussi
* [Documentation de référence des applets de commande HDInsight][hdinsight-powershell-reference]
* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide de l’interface de ligne de commande][hdinsight-admin-cli]
* [Création de clusters HDInsight][hdinsight-provision]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
