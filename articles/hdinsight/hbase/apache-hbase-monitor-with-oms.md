---
title: Surveiller HBase avec Microsoft Operations Management Suite (OMS) - Azure HDInsight | Microsoft Docs
description: Utilisez OMS avec Azure Log Analytics pour surveiller les clusters HDInsight HBase.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f78d570cfa8b040cd7673a5e14e6a992511f60bb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="monitor-hbase-with-operations-management-suite-oms"></a>Surveiller HBase avec Microsoft Operations Management Suite (OMS)

HDInsight HBase Monitoring utilise Azure Log Analytics pour collecter des mesures de performances HDInsight HBase à partir de vos nœuds de cluster HDInsight. Il fournit des visualisations et des tableaux de bord spécifiques à HBase, des outils pour rechercher les mesures, ainsi que la possibilité de créer des alertes et des règles de surveillance personnalisées. Vous pouvez surveiller les mesures de plusieurs clusters HDInsight HBase répartis sur des abonnements Azure différents.

Log Analytics est un service d’[Operations Management Suite (OMS)](../../operations-management-suite/operations-management-suite-overview.md) qui surveille vos environnements cloud et locaux et assure leur disponibilité et leurs performances. Log Analytics collecte les données générées par les ressources dans vos environnements cloud et locaux, ainsi que celles d’autres outils d’analyse pour fournir une analyse sur plusieurs sources.

Les [solutions de gestion Log Analytics](../../log-analytics/log-analytics-add-solutions.md) ajoutent des fonctionnalités à OMS en fournissant des données et des outils d’analyse supplémentaires. Les solutions de gestion Log Analytics représentent une collection de règles logiques, de visualisation et d’acquisition des données qui fournissent des mesures pour une zone particulière. Une solution peut également définir de nouveaux types d’enregistrements à collecter, et ces enregistrements peuvent être analysés avec des recherches dans les journaux ou de nouvelles fonctionnalités d’interface utilisateur.

[Insight & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) repose sur la plateforme Log Analytics. Vous pouvez choisir d’utiliser les fonctions de Log Analytics et de payer par Go ingéré par le service ou de faire basculer votre espace de travail vers le niveau Insight & Analytics et de payer par nœud géré par le service. Insight & Analytics offre un sur-ensemble de fonctionnalités proposées par Log Analytics. La solution HBase Monitoring est disponible avec Log Analytics ou Insight & Analytics.

Lorsque vous approvisionnez une solution HDInsight HBase Monitoring, vous créez un espace de travail OMS. Chaque espace de travail est un environnement Log Analytics unique avec son propre référentiel de données, et ses propres sources de données et solutions. Vous pouvez créer plusieurs espaces de travail dans votre abonnement afin de prendre en charge différents environnements, par exemple de production et de test.

## <a name="provision-hdinsight-hbase-monitoring"></a>Approvisionner HDInsight HBase Monitoring

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Dans le volet **Nouveau** sous **Marketplace**, sélectionnez **Surveillance + gestion**.
3. Dans le volet **Surveillance + gestion**, cliquez sur **Afficher tout**.

    ![Volet Surveillance + gestion](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. Dans la liste, recherchez la bande **Solutions de gestion**. À droite de **Solutions de gestion**, cliquez sur **Plus**.

    ![Volet Surveillance + gestion](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. Dans le volet **Solutions de gestion**, sélectionnez la solution de gestion HDInsight HBase Monitoring à jouter à un espace de travail.

    ![Volet Solutions de gestion](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. Dans le volet de la solution de gestion, passez en revue les informations la concernant, puis sélectionnez **Créer**. 
7. Dans le volet de *nom de la solution de gestion*, sélectionnez un espace de travail existant à associer à la solution de gestion, ou créez un espace de travail OMS, puis sélectionnez-le.
8. Modifiez les paramètres de l’espace de travail concernant l’abonnement Azure, le groupe de ressources et l’emplacement comme nécessaire. 
    ![espace de travail de la solution](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Sélectionnez **Créer**.  
10. Pour utiliser cette nouvelle solution de gestion dans son espace de travail, accédez à **Log Analytics** > ***nom de l’espace de travail*** > **Solutions**. Une entrée pour votre solution de gestion s’affiche dans la liste. Sélectionnez l’entrée pour accéder à la solution.

    ![Solutions Log Analytics](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Le volet de votre solution HDInsight HBase Monitoring s’affiche.

    ![Volet des solutions de HDInsight HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Les vignettes Résumé n’affichent pas de données, car vous n’avez pas encore configuré votre cluster HDInsight HBase pour envoyer des données à Log Analytics.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Connecter un cluster HDInsight HBase à Log Analytics

Pour utiliser les outils fournis par HDInsight HBase Monitoring, vous devez configurer votre cluster afin qu’il transmette les mesures issues de son serveur de région, de ses nœuds principaux et de ses nœuds ZooKeeper à Log Analytics. Cette configuration est effectuée en exécutant une action de script sur votre cluster HDInsight HBase.

### <a name="get-oms-workspace-id-and-workspace-key"></a>Obtenir l’ID de l’espace de travail OMS et la clé de l’espace de travail

Vous avez besoin de l’ID de votre espace de travail OMS et de la clé de votre espace de travail pour activer les nœuds de votre cluster permettant de s’authentifier auprès de Log Analytics. Pour obtenir ces valeurs :

1. À partir de votre volet HBase Monitoring dans le portail Azure, sélectionnez Vue d’ensemble.

    ![Volet de la solution HBase Monitoring](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Sélectionnez Portail OMS pour ouvrir le portail OMS dans une autre fenêtre ou un autre onglet de navigateur.

    ![Sélectionner Portail OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. Sur la page d’accueil du portail OMS, sélectionnez Paramètres.

    ![Portail OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Sélectionnez Source connectée, Linux Servers (Serveurs Linux).

    ![Sélectionner Source connectée, Linux Servers (Serveurs Linux)](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Notez les valeurs d’ID de l’espace de travail et la clé primaire affichées, car ce sont les valeurs dont vous avez besoin pour l’action de script.

### <a name="run-the-script-action"></a>Exécuter l’action de script

Pour activer la collecte de données à partir de votre cluster HDInsight HBase, exécutez une action de script sur tous les nœuds du cluster :

1. Accédez au volet de votre cluster HDInsight HBase dans le portail Azure.
2. Sélectionnez **Actions de script**.

    ![Actions de script](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Sélectionnez **Envoyer**.

    ![Envoyer](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. Dans l’action **Envoyer le script**, définissez le type de script sur `"- Custom"`.
5. Fournissez un nom pour ce script.
6. Pour **URI de script bash**, collez l’URI suivant :

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. Pour **Types de nœuds**, sélectionnez les trois valeurs (**Principal**, **Région**, **ZooKeeper**).
8. Dans la zone de texte **Paramètres**, entrez votre ID d’espace de travail et votre clé d’espace de travail, en plaçant chaque valeur entre guillemets et en les séparant par une espace.

        "<Workspace ID>" "<Workspace Key>"

9. Sélectionnez **Persist this script action** (Conserver cette action de script) pour la réexécuter quand de nouveaux nœuds sont ajoutés au cluster.

    ![Paramètres d’action de script](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Sélectionnez **Créer**.
11. L’exécution de l’action de script prend quelques minutes. Vous pouvez surveiller son état dans le volet Actions de script.

    ![Action de script en cours d’exécution](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Une fois l’action de script terminée, une coche verte doit figurer en regard du nom du script dans la liste.

    ![Action de script terminée](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Afficher la solution HDInsight HBase Monitoring

Une fois l’action de script terminée, des données doivent s’afficher dans la solution Monitoring au bout de quelques minutes.

1. Dans le portail Azure, accédez au volet de votre solution HDInsight HBase.
2. Sélectionnez l’onglet **Vue d’ensemble**.
3. Sous **Résumé**, une vignette indique le nombre de serveurs de région en cours de surveillance.

    ![Vignette Résumé de surveillance](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Sélectionnez la vignette avec le nombre de serveurs de région. Le tableau de bord principal s’affiche.
5. Ce tableau de bord permet d’accéder à des statistiques sur les régions, au nombre de journaux WAL (write-ahead log) en cours d’utilisation, à une collection de recherches Log Analytics (par exemple, pour les journaux de serveur de région, journaux Phoenix et exceptions) et à une collection de graphiques classiques permettant de visualiser les mesures pertinentes. 

    ![Tableau de bord principal](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. En sélectionnant l’une de ces options, vous pourrez descendre dans la hiérarchie de la vue Recherche dans les journaux dans laquelle vous pourrez affiner votre requête et explorer les données plus en détail.

## <a name="next-steps"></a>étapes suivantes

* [Utilisation des règles d’alerte dans Log Analytics](../../log-analytics/log-analytics-alerts-creating.md)
* [Trouver des données avec les recherches de journaux dans Log Analytics](../../log-analytics/log-analytics-log-searches.md).
