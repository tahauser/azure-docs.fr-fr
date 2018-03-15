---
title: "Installer une application publiée - Dataiku DSS - Azure HDInsight | Microsoft Docs"
description: "Installez et utilisez l’application Hadoop tierce Dataiku DSS."
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
ms.openlocfilehash: 835f433e0bf79e8bc4d9b30963196f65bc53cd0e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---dataiku-dds"></a>Installer une application publiée - Dataiku DSS

Cet article explique comment installer et exécuter l’application Hadoop publiée [Dataiku DSS](https://www.dataiku.com/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et la liste des applications publiées d’éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>À propos de Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/) est une plateforme collaborative de science des données qui permet aux chercheurs de données de générer et d’offrir des solutions d’analyse. La plateforme DSS est proposée en tant qu’application HDInsight, ce qui vous permet d’utiliser la science des données pour générer des solutions Big Data et de les exécuter de manière professionnelle à l’échelle de l’entreprise.

Vous pouvez utiliser DSS pour implémenter une solution d’analyse complète, en commençant par l’ingestion, la préparation et le traitement des données. Une solution DSS peut également inclure l’apprentissage et l’application de modèles Machine Learning, la visualisation, et enfin l’utilisation.

Vous pouvez installer DSS sur HDInsight à l’aide de clusters Hadoop ou Spark. Vous pouvez installer DSS sur des clusters déjà en cours d’exécution ou durant la création de nouveaux clusters. DSS prend également en charge le stockage d’objets blob Azure comme connecteur pour la lecture des données.

Vous pouvez utiliser DSS pour générer des projets, à partir desquels générer ensuite des travaux MapReduce ou Spark. Ces travaux sont exécutés sous forme de travaux MapReduce ou Spark réguliers sur HDInsight, ce qui vous permet d’adapter le cluster à la demande.

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau(x) de cluster : Standard, Premium
* Type (s) de cluster : Hadoop, Spark
* Version(s) de cluster : 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Installer l’application publiée Dataiku DSS

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Lancer Dataiku DSS

1. Après l’installation, vous pouvez lancer DSS à partir de votre cluster dans le portail Azure en accédant au volet **Paramètres**, puis en cliquant sur **Applications** sous la catégorie **Général**. Le volet Applications installées répertorie les applications installées.

    ![Application Dataiku DSS installée](./media/hdinsight-apps-install-dataiku/app.png)

2. Quand vous sélectionnez DSS sur HDInsight, un lien vers la page web apparaît, ainsi que le chemin du point de terminaison SSH. Sélectionnez le lien WEBPAGE (Page web).

3. Quand l’application est lancée pour la première fois, un formulaire s’affiche pour vous aider à créer un compte Dataiku gratuitement. Si vous en disposez déjà d’un, vous pouvez vous y connecter. Vous pouvez également accéder à une version d’évaluation gratuite de 2 semaines d’[Édition Entreprise](https://www.dataiku.com/dss/editions/). À ce stade, vous avez le choix entre deux options : poursuivre en entrant votre clé de licence d’Édition Entreprise ou utiliser Community Edition.

4. Après avoir suivi les instructions de votre option de licence, un formulaire de connexion s’affiche. Entrez les informations d’identification par défaut affichées avant le formulaire de connexion.

Les étapes suivantes proposent une démonstration simple.

1. [Téléchargez l'exemple de fichier de commandes (format CSV)](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Dans le tableau de bord DSS, sélectionnez le lien **+** (Nouveau projet) dans le menu de gauche pour créer un projet.

    ![Lien de nouveau projet](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Dans le formulaire de nouveau projet, entrez un **nom**. La **clé de projet** est automatiquement renseignée à l’aide d’une valeur suggérée. Dans le cas présent, entrez « Orders » (Commandes). Cliquez sur **CREATE** (Créer).

    ![Formulaire de nouveau projet](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Sélectionnez **+ IMPORT YOUR FIRST DATASET** (Importer votre premier jeu de données) dans la page de nouveau projet.

    ![Chargement de fichiers](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Sélectionnez **Upload your files** (Charger vos fichiers) dans la liste des jeux de données **Files** (Fichiers). La boîte de dialogue de chargement s’affiche. Cliquez sur Add a file (Ajouter un fichier), sélectionnez le fichier `haiku_shirt_sales.csv` que vous avez téléchargé et confirmez l’opération.

6. Le fichier est chargé dans DSS. Vérifiez si DSS a correctement détecté le format CSV en cliquant sur le bouton d’aperçu.

    ![Chargement de fichiers](./media/hdinsight-apps-install-dataiku/preview.png)

7. L’importation est presque parfaite. Le fichier CSV utilise un séparateur à onglets. Les données s’affichent dans un format tabulaire, avec des colonnes portant le nom de composants, et des lignes représentant des observations. La seule erreur dans cet exemple est que le fichier contenait apparemment une ligne vide entre l’en-tête et les données. Pour corriger cette erreur, entrez `1` dans le champ **Skip next lines** (Ignorer les lignes suivantes).

    ![Enregistrer](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Attribuez un nom au nouveau jeu de données. Entrez **haiku_shirt_sales** dans le champ situé en haut de l’écran, puis sélectionnez le bouton **Create** (Créer).

9. Une vue tabulaire des données s’affiche. Vous pouvez y consulter les informations nécessaires. Vous observerez que pour chaque colonne, Dataiku Science Studio a détecté un type de données (en _bleu_). Dans le cas présent, il s’agit de texte, de chiffres ou de dates (données non analysées). Un indicateur affiche dans quelle mesure les valeurs de la colonne ne semblent pas correspondre au type (en rouge) ou sont manquantes (espace vide). Dans cet exemple de jeu de données, la colonne department contient à la fois des valeurs non renseignées et des données non valides.

    ![Vue tabulaire](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulation de données

Les données du monde réel sont presque toujours désordonnées, et rarement bien présentées. Le nettoyage des données nécessite généralement une chaîne de scripts et la logique métier associée. Dataiku DSS fournit un outil **Lab** dédié pour réaliser cette tâche plus facilement.

1. Cliquez sur le bouton **Lab** dans l’angle supérieur droit.

    ![Bouton Lab](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. La fenêtre Lab s’ouvre. Le laboratoire n’est autre que l’emplacement où vous travaillez sur votre jeu de données de manière itérative pour l’analyser en profondeur. Ce didacticiel montre l’aspect de Visual analysis. Sélectionnez le bouton **New** (Nouveau) situé sous Visual analysis. Vous êtes invité à attribuer un nom à votre analyse. Pour l’instant, laissez le nom par défaut et cliquez sur **CREATE** (Créer).

    ![Création d’un projet de laboratoire](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Sélectionnez le bouton **Quick columns stats** (Statistiques rapides par colonnes) situé dans l’angle supérieur droit de la page.

    ![Bouton de statistiques rapides par colonnes](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. La page affiche des statistiques pour les types et valeurs de données sous forme de graphiques basés sur la chronologie dans le volet **Columns quick view** (Aperçu rapide par colonnes).

    ![Volet d’aperçu rapide par colonnes](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Vous pouvez à présent explorer DSS à l’aide des exemples de données. Vous pouvez nettoyer et travailler avec les données, et créer de nouvelles visualisations.

Pour accéder à des didacticiels complets, consultez [Learn Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Étapes suivantes

* [Documentation sur Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight non publiées sur HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnaliser des clusters HDInsight Linux à l’aide d’actions de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides dans HDInsight](hdinsight-apps-use-edge-node.md) : apprenez à utiliser un nœud de périphérie vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
