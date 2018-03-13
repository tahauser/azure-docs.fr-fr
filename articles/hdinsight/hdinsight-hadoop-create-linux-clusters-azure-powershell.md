---
title: "Créer des clusters Hadoop à l’aide de PowerShell - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight à l’aide d’Azure PowerShell."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 2bbb34987bbf639bcd246afbfd4debea98681922
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell est un environnement puissant de création de scripts vous permettant de contrôler et d’automatiser le déploiement et la gestion de vos charges de travail dans Microsoft Azure. Ce document fournit des informations sur la création d’un cluster HDInsight basé sur Linux à l’aide d’Azure PowerShell. Il inclut également un exemple de script.

> [!NOTE]
> Azure PowerShell est uniquement disponible sur les clients Windows. Si vous utilisez un client Linux, Unix ou Mac OS X, consultez [Création de clusters basés sur Linux dans HDInsight à l’aide de l’interface CLI Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) pour plus d’informations sur l’utilisation de l’interface CLI Azure pour créer un cluster.

## <a name="prerequisites"></a>configuration requise
Avant de commencer cette procédure, vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > La prise en charge de la gestion des ressources HDInsight par Azure PowerShell à l’aide d’Azure Service Manager est **déconseillée** ; elle a été supprimée le 1er janvier 2017. Dans ce document, la procédure repose sur les nouvelles applets de commande HDInsight qui fonctionnent avec Azure Resource Manager.
    >
    > Suivez les étapes indiquées dans [Installer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) pour installer la dernière version d’Azure PowerShell. Si vous devez modifier certains scripts pour utiliser les nouvelles applets de commande fonctionnant avec Azure Resource Manager, consultez [Migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) pour plus d’informations.

## <a name="create-cluster"></a>Créer un cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Les procédures suivantes sont nécessaires pour créer un cluster HDInsight en utilisant Azure PowerShell :

* Création d’un groupe de ressources Azure
* Création d'un compte Azure Storage
* Création d'un conteneur d'objets blob Azure
* Création d'un cluster HDInsight

Le script suivant montre comment créer un nouveau cluster :

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Les valeurs que vous spécifiez pour la connexion du cluster sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Utilisez ce compte pour la connexion aux services hébergés sur le cluster, tels que des interfaces utilisateur ou des API REST.

Les valeurs que vous spécifiez pour l’utilisateur SSH sont utilisées pour créer l’utilisateur SSH pour le cluster. Utilisez ce compte pour démarrer une session SSH à distance sur le cluster et pour exécuter des tâches. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Si vous envisagez d’utiliser plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez également spécifier une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

La création d’un cluster peut prendre jusqu’à 20 minutes.

## <a name="create-cluster-configuration-object"></a>Création de cluster : objet de configuration

Vous pouvez également créer un objet de configuration HDInsight à l’aide de l’applet de commande `New-AzureRmHDInsightClusterConfig`. Il est ensuite possible de modifier cet objet de configuration pour activer des options de configuration supplémentaires pour votre cluster. Enfin, utilisez le paramètre `-Config` de l’applet de commande `New-AzureRmHDInsightCluster` pour utiliser la configuration.

Le script suivant crée un objet de configuration pour configurer un R Server sur le type de cluster HDInsight. La configuration active un nœud de périmètre, RStudio et un compte de stockage supplémentaire.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> L’utilisation d’un compte de stockage dans un autre emplacement que le cluster HDInsight n’est pas prise en charge. Pour cet exemple, créez le compte de stockage supplémentaire dans le même emplacement que le serveur.

## <a name="customize-clusters"></a>Personnalisation des clusters

* Consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un cluster HDInsight, parcourez les ressources ci-dessous pour apprendre à l’utiliser.

### <a name="hadoop-clusters"></a>Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clusters HBase

* [Prise en main de HBase sur HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](storm/apache-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clusters Spark

* [Créer une application autonome avec Scala](spark/apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](spark/apache-spark-livy-rest-interface.md)
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](spark/apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](spark/apache-spark-machine-learning-mllib-ipython.md)

