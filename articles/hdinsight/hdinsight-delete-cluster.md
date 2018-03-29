---
title: Suppression d’un cluster HDInsight - Azure | Microsoft Docs
description: Informations sur les différentes méthodes que vous pouvez utiliser pour supprimer un cluster HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: ef4ada501aaf6a5c1218de51e3fcd6838904e5af
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Suppression d’un cluster HDInsight à l’aide de votre navigateur, PowerShell ou l’interface de ligne de commande Azure

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous découvrirez comment supprimer un cluster à l’aide du portail Azure, d’Azure PowerShell et de l’interface de ligne de commande (CLI) Azure 1.0.

> [!IMPORTANT]
> La suppression d’un cluster HDInsight ne supprime pas les comptes de stockage Azure ou le Data Lake Store associés au cluster. Vous pouvez réutiliser les données stockées dans ces services à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre cluster HDInsight. Si votre cluster HDInsight n’est pas épinglé au tableau de bord, vous pouvez le rechercher par son nom dans le champ de recherche.
   
    ![recherche dans le portail](./media/hdinsight-delete-cluster/navbar.png)

2. Dans les paramètres de cluster, sélectionnez l’icône **Supprimer**. Lorsque vous y êtes invité, sélectionnez **Oui** pour supprimer le cluster.
   
    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

À partir d’une invite PowerShell, utilisez la commande suivante pour supprimer le cluster :

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

## <a name="azure-cli-10"></a>Azure CLI 1.0

À partir d’une invite, utilisez la commande suivante pour supprimer le cluster :

    azure hdinsight cluster delete CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

> [!NOTE]
> Azure CLI 2.0 ne prend pas en charge la suppression des clusters HDInsight pour l’instant (23 octobre 2017).