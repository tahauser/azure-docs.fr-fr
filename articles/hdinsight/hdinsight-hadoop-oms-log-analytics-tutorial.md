---
title: Utiliser Log Analytics pour surveiller les clusters Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Azure Log Analytics pour surveiller les travaux en cours d’exécution dans un cluster HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 2c43b9aabda56e1204870575e86934bf93d05882
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Utiliser Azure Log Analytics pour surveiller les clusters HDInsight

Découvrez comment utiliser Azure Log Analytics pour surveiller les opérations de cluster Hadoop dans HDInsight.

[Log Analytics](../log-analytics/log-analytics-overview.md) est un service d’[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources. 

## <a name="prerequisites"></a>configuration requise

* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).

* Un **cluster Azure HDInsight**. Actuellement, vous pouvez utiliser Azure Operations Management Suite avec les types de cluster HDInsight suivants :

    * Hadoop
    * hbase
    * Interactive Query
    * Kafka
    * Spark
    * Storm

    Pour savoir comment créer un cluster HDInsight, consultez [Bien démarrer avec Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un espace de travail Log Analytics**. Considérez cet espace de travail comme un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Vous devez déjà avoir créé un espace de travail de ce type que vous pouvez associer aux clusters Azure HDInsight. Pour obtenir des instructions, consultez [Créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="enable-log-analytics-by-using-the-portal"></a>Activer Log Analytics à l’aide du portail

Cette section vous explique comment configurer un cluster Hadoop HDInsight existant pour utiliser un espace de travail Azure Log Analytics pour surveiller les travaux, les journaux de débogage, etc.

1. Ouvrez un cluster HDInsight dans le portail Azure.
2. Dans le volet gauche, cliquez sur **Surveillance**.
3. Dans le volet droit, cliquez sur **Activer**, sélectionnez un espace de travail Log Analytics, puis cliquez sur **Enregistrer**.

    ![Activer la surveillance des clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Activer la surveillance des clusters HDInsight")

    L’enregistrement du paramètre prend quelques instants.  Une fois terminé, vous pouvez voir le bouton **Ouvrir le tableau de bord OMS** en haut de la page. 

    ![Ouvrir le tableau de bord Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Ouvrir le tableau de bord OMS")

5. Cliquez sur **Ouvrir le tableau de bord OMS**.
6. Entrez vos informations d’identification Azure si vous êtes invité à le faire.

    ![Portail Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portail Operations Management Suite")

## <a name="enable-log-analytics-by-using-azure-powershell"></a>Activer Log Analytics à l’aide d’Azure PowerShell

Vous pouvez activer Log Analytics à l’aide d’Azure PowerShell. L’applet de commande est :

```powershell
Enable-AzureRmHDInsightOperationsManagementSuite
```

Consultez [Enable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/Enable-AzureRmHDInsightOperationsManagementSuite?view=azurermps-5.0.0).

Pour désactiver, l’applet de commande est 

```powershell
Disable-AzureRmHDInsightOperationsManagementSuite
```

Consultez [Disable-AzureRmHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/disable-azurermhdinsightoperationsmanagementsuite?view=azurermps-5.0.0).


## <a name="next-steps"></a>Étapes suivantes
* [Ajouter des solutions de gestion de clusters HDInsight à Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
