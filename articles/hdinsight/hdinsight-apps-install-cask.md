---
title: "Installer une application publiée - Datameer - Azure HDInsight | Microsoft Docs"
description: "Installez et utilisez l’application Hadoop tierce Datameer."
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
ms.openlocfilehash: 4b83f2a2228ef0dd7fa56b5a71b267d1e4302620
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Installer une application publiée : CDAP (Cask Data Application Platform)

Cet article explique comment installer et exécuter l’application Hadoop publiée [CDAP](http://cask.co/products/cdap/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et une liste des applications publiées par des éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>À propos de CDAP

Le développement d’applications dans Hadoop peut se révéler difficile.  Il existe un nombre croissant d’extensions de technologie Hadoop dont l’intégration peut prendre un certain temps. La surveillance du flux de données et la collecte des métriques peuvent nécessiter la création d’une solution distincte.

### <a name="how-does-cdap-help"></a>En quoi CDAP peut-il être utile ?

Cask Data Application Platform (CDAP) est une plateforme d’intégration pour le Big Data. CDAP vous permet de vous concentrer sur la création d’applications plutôt que sur l’infrastructure sous-jacente.

CDAP utilise des concepts et des abstractions de haut niveau familières aux développeurs. Ces abstractions masquent la complexité des systèmes internes et facilitent la réutilisation des solutions.

Une extension CDAP appelée [Cask Hydrator](http://cask.co/products/hydrator/) fournit une interface utilisateur pour développer et gérer des pipelines de données. Un pipeline de données se compose de différents *plug-ins qui effectuent des tâches comme l’acquisition, la transformation, l’analyse de données ainsi que des opérations post-exécution.

Chaque plug-in CDAP comportant une interface bien définie, l’évaluation des différentes technologies consiste simplement à remplacer un plug-in par un autre, sans toucher au reste de l’application.

Les *pipelines* CDAP fournissent une représentation graphique de haut niveau du flux de données dans votre application. Cette visualisation montre le flux complet des données, de l’ingestion, en passant par les transformations de données et les analyses, jusqu’à une banque de données externe.

L’exemple suivant montre un pipeline de données qui reçoit des données Twitter en temps réel, puis exclut certains tweets selon des critères prédéfinis. Le pipeline de données transforme les données brutes des tweets et projette ces données dans un format plus lisible, puis regroupe les tweets en fonction d’un jeu de valeurs et inscrit les résultats dans une banque HBase.

![Pipeline CDAP](./media/hdinsight-apps-install-cask/pipeline.png)

Ce pipeline complet est construit à l’aide de l**’interface utilisateur Cask Hydrator**, en utilisant son interface plug-in et sa fonctionnalité de glisser-déposer pour établir des connexions entre les différentes étapes. Vous pouvez isoler et modifier les fonctionnalités de chaque plug-in de manière indépendante. À l’aide de CDAP, des pipelines similaires peuvent être générés et validés en quelques heures. Dans l’environnement Hadoop classique, la construction de telles solutions peut prendre plusieurs jours.

CDAP fournit également une extension appelée [Cask Tracker](http://cask.co/products/tracker/) pour suivre visuellement les données à mesure qu’elles transitent par l’application. Cask Tracker ajoute une *gouvernance des données* au système afin que les ressources de données soient gérées de manière formelle dans toute l’application. Vous pouvez suivre le lignage de chaque point de données, collecter des mesures pertinentes et effectuer l’audit des données tout au long du processus.

Voici une illustration de la façon dont les données circulent dans le pipeline ci-dessus :

![Traqueur CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau cluster : standard
* Type de cluster : HBase
* Version de cluster : 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Installer l’application publiée CDAP

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Lancer CDAP

1. Après l’installation, lancez CDAP à partir de votre cluster dans le portail Azure en accédant au volet **Paramètres**, puis en sélectionnant **Applications** sous la catégorie **Général**. Le volet Applications installées répertorie toutes les applications installées.

    ![Application CDAP installée](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Quand vous sélectionnez CDAP, un lien vers la page web et un point de terminaison HTTP apparaissent, ainsi que le chemin du point de terminaison SSH. Sélectionnez le lien vers la page web.

3. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de votre cluster.

    ![Authentification](./media/hdinsight-apps-install-cask/auth.png)

4. Une fois que vous êtes connecté, la page d’accueil de l’interface graphique utilisateur Cask CDAP apparaît.

    ![Page d’accueil de l’interface graphique utilisateur Cask](./media/hdinsight-apps-install-cask/gui.png)

5. Pour explorer l’interface CDAP, cliquez sur le lien du menu **Cask Market** en haut de la page.

    ![Lien vers Cask Market](./media/hdinsight-apps-install-cask/cask-market.png)

6. Sélectionnez **Exemple de journal d’accès** dans la liste.

    ![Exemple de journal d’accès](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Cliquez sur **Charger** pour confirmer.

    ![Cliquez sur Charger](./media/hdinsight-apps-install-cask/market-load.png)

8. Une vue de l’exemple de données apparaît. Sélectionnez **Suivant**.

    ![Exemple de journal accès - afficher les données](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Sélectionnez **Stream** comme type de Destination, entrez un nom de destination, puis sélectionnez **Terminer**.

    ![Exemple de journal d’accès - sélectionner la destination](./media/hdinsight-apps-install-cask/market-destination.png)

10. Une fois le pack de données correctement chargé, sélectionnez **Afficher les détails du flux**.

    ![Chargement réussi du pack de données](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Pour activer les métadonnées de l’espace de noms, sélectionnez **Activer** sous l’onglet Utilisation dans la page des détails du journal d’accès.

    ![Exemple de journal d’accès - chargé - activer les métadonnées](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Une fois les métadonnées activées, un graphique montrant les informations du message d’audit apparaît.

    ![Exemple de journal d’accès - métadonnées activées](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Pour explorer les données du journal, sélectionnez l’icône **Explorer** en haut de la page.

    ![Exemple de journal d’accès - Explorer](./media/hdinsight-apps-install-cask/log-explore.png)

14. Vous voyez un exemple de requête SQL. N’hésitez pas à le modifier selon vos besoins, puis sélectionnez **Exécuter**.

    ![Exemple de journal d’accès - explorer le jeu de données avec une requête](./media/hdinsight-apps-install-cask/log-query.png)

15. Une fois la requête terminée, sélectionnez l’icône **Affichage** sous la colonne Actions.

    ![Exemple de journal accès - afficher la requête terminée](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Les résultats de la requête apparaissent.

    ![Exemple de journal d’accès - résultats de la requête](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Étapes suivantes

* [Documentation Cask](http://cask.co/resources/documentation/).
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périphérique vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
