---
title: Interroger Azure Log Analytics pour surveiller les clusters Azure HDInsight | Microsoft Docs
description: "Découvrez comment exécuter des requêtes sur Azure Log Analytics pour surveiller les travaux en cours d’exécution dans un cluster HDInsight."
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
ms.openlocfilehash: f9e023ae8d6c50049b588cf056629cd8d46ee603
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>Interroger Azure Log Analytics pour surveiller les clusters HDInsight

Découvrez certains scénarios d’utilisation de base d’Azure Log Analytics pour surveiller des clusters Azure HDInsight :

* [Analyser les métriques du cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Rechercher des messages de journal spécifiques](#search-for-specific-log-messages)
* [Créer des alertes d’événement](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Prérequis

* Vous devez avoir configuré un cluster HDInsight pour utiliser Azure Log Analytics. Pour obtenir des instructions, consultez [Utiliser Azure Log Analytics avec des clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Vous devez avoir ajouté à l’espace de travail [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) les solutions de gestion de cluster spécifiques à HDInsight, comme décrit dans [Ajouter des solutions de gestion de clusters HDInsight à Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analyser les métriques du cluster HDInsight

Découvrez comment rechercher des métriques spécifiques pour votre cluster HDInsight.

1. Ouvrez un cluster HDInsight que vous avez associé à Azure Log Analytics dans le portail Azure.
2. Cliquez sur **Surveillance**, puis cliquez sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Cliquez sur **Recherche dans les journaux** dans le menu de gauche.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Saisissez la requête suivante dans la zone de recherche afin de rechercher toutes les mesures pour toutes les mesures disponibles dans tous les clusters HDInsight configurés pour utiliser Azure Log Analytics, puis appuyez sur **ENTRÉE**.

        `search *` 

    ![Rechercher toutes les mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Rechercher toutes les mesures")

    Le résultat doit être semblable à ceci :

    ![Rechercher toutes les sorties de mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Rechercher toutes les sorties de mesures")

5. Dans le volet gauche, sous **Type**, sélectionnez une mesure que vous souhaitez approfondir, puis cliquez sur **Appliquer**. La capture d’écran qui suit présente le type `metrics_resourcemanager_queue_root_default_CL` sélectionné. 

    > [!NOTE]
    > Vous devrez peut-être cliquer sur le bouton **[+]Plus** pour trouver la mesure que vous recherchez. En outre, comme le bouton **Appliquer** figure au bas de la liste, vous devez faire défiler la page vers le bas pour l’afficher.
    > 
    >    

    Notez que la requête dans la zone de texte apparaît dans la zone en surbrillance dans la capture d’écran suivante :

    ![Rechercher des mesures spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Rechercher des mesures spécifiques")

6. Pour approfondir cette mesure spécifique. Par exemple, vous pouvez affiner les résultats en fonction de la moyenne des ressources utilisées dans un intervalle de 10 minutes, en les classant par nom de cluster à l’aide de la requête suivante :

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Au lieu d’affiner les résultats en fonction de la moyenne des ressources utilisées, vous pouvez utiliser la requête suivante pour affiner les résultats en fonction du moment où les ressources maximales ont été utilisées (ainsi qu’aux 90e et 95e centiles) dans une fenêtre de 10 minutes :

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Rechercher des messages de journal spécifiques

Découvrez comment rechercher des messages d’erreur pendant une fenêtre de temps spécifique. Ces étapes ne sont qu’un exemple d’une méthode permettant d’afficher le message d’erreur qui vous intéresse. Vous pouvez utiliser n’importe quelle propriété disponible pour rechercher les erreurs que vous voulez trouver.

1. Ouvrez un cluster HDInsight que vous avez associé à Azure Log Analytics dans le portail Azure.
2. Cliquez sur **Surveillance**, puis cliquez sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Dans l’écran d’accueil du tableau de bord OMS, cliquez sur **Recherche dans les journaux**.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Saisissez la requête suivante afin de rechercher tous les messages d’erreur dans tous les clusters HDInsight configurés pour utiliser Azure Log Analytics, puis appuyez sur **ENTRÉE**. 

         search "Error"

    Vous devez voir un résultat semblable à ce qui suit :

    ![Rechercher tous les résultats d’erreur](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Rechercher tous les résultats d’erreur")

5. Dans le volet gauche, sous la catégorie **Type**, sélectionnez un type d’erreur que vous souhaitez approfondir, puis cliquez sur **Appliquer**.  Notez que les résultats sont affinés pour n’afficher que l’erreur du type que vous avez sélectionné.
7. Vous pouvez approfondir cette liste d’erreurs spécifiques en utilisant les options disponibles dans le volet gauche. Par exemple, 

    - Pour afficher les messages d’erreur d’un nœud de travail spécifique :

        ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Rechercher un résultat d’erreur spécifique")

    - Pour afficher une erreur qui s’est produite à une heure donnée :

        ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Rechercher un résultat d’erreur spécifique")

9. Pour afficher l’erreur spécifique. Vous pouvez cliquer sur **[+]afficher plus** pour examiner le message d’erreur.

    ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Rechercher un résultat d’erreur spécifique")

## <a name="create-alerts-for-tracking-events"></a>Créer des alertes pour des événements de suivi

La première étape pour créer une alerte consiste à définir une requête qui déclenche l’alerte. Par souci de simplicité, nous utiliserons la requête suivante, qui fournit la liste des applications ayant échoué en cours d’exécution sur les clusters HDInsight.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Vous pouvez utiliser la requête de votre choix pour créer une alerte.

1. Ouvrez un cluster HDInsight que vous avez associé à Azure Log Analytics dans le portail Azure.
2. Cliquez sur **Surveillance**, puis cliquez sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Dans l’écran d’accueil du tableau de bord OMS, cliquez sur **Recherche dans les journaux**.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Exécutez la requête suivante là où vous souhaitez créer une alerte, puis appuyez sur **ENTRÉE**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Cliquez sur **Alerte** en haut de la page.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Saisir une requête pour créer une alerte")

4. Dans la fenêtre **Ajouter une règle d’alerte**, entrez la requête puis d’autres détails pour créer une alerte, puis cliquez sur **Enregistrer**.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Saisir une requête pour créer une alerte")

    La capture d’écran montre la configuration pour l’envoi d’une notification par courrier électronique lorsque la requête d’alerte renvoie un résultat.

5. Vous pouvez également modifier ou supprimer une alerte existante. Pour cela, dans n’importe quelle page du portail OMS, cliquez sur l’icône **Paramètres**.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Saisir une requête pour créer une alerte")

6. À la page **Paramètres**, cliquez sur **Alertes** pour afficher les alertes que vous avez créées. Vous pouvez également activer ou désactiver une alerte, la modifier ou la supprimer. Pour plus d’informations, voir [Utilisation des règles d’alerte dans Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Voir aussi

* [Utilisation d’Operations Management Suite Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Création de règles d’alerte dans Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
