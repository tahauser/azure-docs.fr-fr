---
title: "Installer une application publiée - StreamSets Data Collector - Azure HDInsight | Microsoft Docs"
description: "Installez et utiliser l’application Hadoop tierce StreamSets Data Collector."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 90775452c58457ae8ecc73687a375606474158f5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Installer une application publiée - StreamSets Data Collector

Cet article explique comment installer et exécuter l’application Hadoop publiée [StreamSets Data Collector pour HDInsight](https://streamsets.com/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et la liste des applications publiées d’éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>À propos de StreamSets Data Collector

StreamSets Data Collector se déploie sur une application Azure HDInsight. StreamSets Data Collector offre un environnement de développement intégré complet qui vous permet de concevoir, tester, déployer et gérer des pipelines d’ingestion universels. Ces pipelines maillent les flux et groupent les données, tout en incluant diverses transformations intégrées au flux, sans aucune écriture de code personnalisé.

StreamSets Data Collector vous permet de développer les flux de données à l’aide de nombreux composants Big Data comme HDFS, Kafka, Solr, Hive, HBASE et Kudu. Une fois que StreamSets Data Collector est en cours d’exécution sur le serveur Edge ou dans votre cluster Hadoop, vous bénéficiez d’une surveillance en temps réel des anomalies de données et des opérations sur les flux de données. Cette surveillance comprend les alertes basées sur des seuils, la détection des anomalies et la correction automatique des enregistrements d’erreur.

StreamSets Data Collector est conçu pour isoler de manière logique chacune des étapes dans un pipeline, ce qui vous permet de satisfaire les nouvelles exigences métiers en ajoutant de nouveaux processeurs et connecteurs sans codage supplémentaires, et avec un temps d’arrêt minimal.

### <a name="streamsets-resource-links"></a>Liens vers les ressources StreamSets

* [Documentation](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Didacticiels](https://github.com/streamsets/tutorials)
* [Forum Developer Support](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Canal Slack public StreamSets](https://streamsetters.slack.com/)
* [Code source](https://github.com/streamsets)

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau(x) de cluster : Standard ou Premium
* Version(s) de cluster : 3.5 ou ultérieures

## <a name="install-the-streamsets-data-collector-published-application"></a>Installer l’application publiée StreamSets Data Collector

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Lancer Streams Data Collector

1. Après l’installation, vous pouvez lancer StreamSets à partir de votre cluster dans le portail Azure en accédant au volet **Paramètres**, puis en cliquant sur **Applications** sous la catégorie **Général**. Le volet Applications installées répertorie les applications installées.

    ![Applications StreamSets installées](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Quand vous sélectionnez StreamSets Data Collector, un lien vers la page web apparaît, ainsi que le chemin du point de terminaison SSH. Sélectionnez le lien WEBPAGE (page web).

3. Dans la boîte de dialogue de connexion, utilisez les informations d’identification suivantes pour vous connecter : `admin` et `admin`.

4. Dans la page de prise en main, cliquez sur **Créer un pipeline**.

    ![Créer un pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Dans la fenêtre Nouveau pipeline, saisissez un nom pour le pipeline (« Hello World »), entrez éventuellement une description, puis sélectionnez **Enregistrer**.

6. La console Data Collector s’affiche. Le panneau Propriétés affiche les propriétés du pipeline.
 
    ![Console Data Collector](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Vous êtes maintenant prêt à suivre le [didacticiel StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Il vous indique une procédure pas à pas pour la création de votre premier pipeline.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides sur les clusters Hadoop dans HDInsight](hdinsight-apps-use-edge-node.md) : apprenez à utiliser un nœud de périphérie vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
