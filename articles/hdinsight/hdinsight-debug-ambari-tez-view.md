---
title: "Utiliser la vue Tez d’Ambari avec HDInsight - Azure | Microsoft Docs"
description: "Découvrez comment utiliser la vue Tez d’Ambari pour déboguer les travaux Tez dans HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: bf34bc5a65c2beb55c7039868e4d90d83d28de51
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Utiliser les vues Ambari pour déboguer les travaux Tez dans HDInsight

L’interface utilisateur Web d’Ambari pour HDInsight contient une vue Tez qui peut servir à comprendre et à déboguer les tâches utilisant Tez. La vue Tez vous permet de visualiser le travail sous forme de graphique d’éléments connectés, d’explorer chacun d’entre eux, ainsi que d’extraire des statistiques et des informations de journalisation.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prérequis

* Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez l’article [Prise en main de HDInsight sous Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Un navigateur web moderne qui prend en charge HTML5.

## <a name="understanding-tez"></a>Présentation de Tez

Tez est une infrastructure extensible pour le traitement des données dans Hadoop plus rapide que le traitement MapReduce traditionnel. Pour les clusters HDInsight sous Linux, Tez est le moteur par défaut pour Hive.

Tez crée un graphe orienté acyclique qui décrit l’ordre des actions requises par les tâches. Les actions individuelles sont appelées des vertex et exécutent une partie du travail global. L’exécution réelle du travail décrit par un vertex est appelée une tâche, et peut être répartie sur plusieurs nœuds du cluster.

### <a name="understanding-the-tez-view"></a>Présentation de la vue Tez

La vue Tez fournit des informations historiques et des informations sur les processus en cours d’exécution. Ces informations vous indiquent comment une tâche est répartie entre les clusters. Elle affiche également les compteurs utilisés par les tâches et les vertex et les informations des erreurs liées à la tâche. Elle peut fournir des informations utiles dans les scénarios suivants :

* Surveiller les processus à long terme, voir l'avancement des tâches de mappage et de réduction.
* Analyser les données historiques des processus ayant réussi ou échoué, afin de savoir comment le traitement peut être amélioré ou pourquoi il a échoué.

## <a name="generate-a-dag"></a>Générer un DAG

La vue Tez ne contient des données que si une tâche qui utilise le moteur Tez est en cours d’exécution ou a déjà été exécutée. Les requêtes Hive simples peuvent être résolues sans utiliser Tez. Les requêtes plus complexes qui effectuent des opérations de filtrage, de regroupement, de tri, de jointure, etc. utilisent le moteur Tez.

Utilisez les étapes suivantes pour exécuter une requête Hive utilisant Tez :

1. Dans un navigateur web, accédez à l’adresse https://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom de votre cluster HDInsight.

2. Dans le menu situé en haut de la page, sélectionnez l’icône **Vues** . Cette icône représente une série de carrés. Dans la liste déroulante qui s’affiche, sélectionnez **Vue Hive**.

    ![Sélectionner la vue Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Une fois la vue Hive chargée, collez la requête suivante dans l’éditeur de requête, puis cliquez sur **Exécuter**.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    Une fois le travail terminé, le résultat devrait s’afficher dans la section **Résultats du processus de requête** . Les résultats doivent être similaires au texte suivant :

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. Sélectionnez l’onglet **Journal**. Des informations similaires au texte suivant s’affichent :

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Enregistrez la valeur **ID d’application** , car elle est utilisée dans la section suivante.

## <a name="use-the-tez-view"></a>Utiliser la vue Tez

1. Dans le menu situé en haut de la page, sélectionnez l’icône **Vues** . Dans la liste déroulante qui s’affiche, sélectionnez **Vue Tez**.

    ![Sélectionner la vue Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Une fois la vue Tez chargée, la liste des requêtes Hive en cours d’exécution ou exécutées sur le cluster s’affiche.

    ![Tous les DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. Si vous ne voyez qu’une seule entrée, il s’agit de la requête que vous avez exécutée dans la section précédente. Si vous avez plusieurs entrées, vous pouvez les rechercher en haut de la page à l’aide des champs.

4. Sélectionnez **l’ID de requête** pour une requête Hive. De plus amples informations sur la requête s’affichent.

    ![Détails du DAG](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. Les onglets de cette page vous permettent d’afficher les informations suivantes :

    * **Détails de la requête** : plus d’informations sur la requête Hive.
    * **Chronologie** : plus d’informations sur la durée de chaque étape du traitement.
    * **Configurations** : la configuration utilisée pour cette requête.

    À partir de __Détails de la requête__, vous pouvez utiliser les liens pour rechercher des informations sur __Application__ ou __DAG__ pour cette requête.
    
    * Le lien __Application__ affiche des informations sur l’application YARN pour cette requête. À ce stade, vous pouvez accéder aux journaux des applications YARN.
    * Le lien __DAG__ affiche des informations sur le graphique acyclique dirigé pour cette requête. Cette vue permet d’afficher une représentation graphique du DAG. Vous pouvez également rechercher des informations sur les vertex dans le DAG.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser la vue Tez, obtenez plus d’informations sur l’ [Utilisation de Hive dans HDInsight](hadoop/hdinsight-use-hive.md).

Pour plus d’informations techniques sur Tez, consultez la [page Tez sur Hortonworks](http://hortonworks.com/hadoop/tez/)(en anglais).

Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)
