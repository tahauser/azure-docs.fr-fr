---
title: Data Lake Tools pour Visual Studio avec Hortonworks Sandbox - Azure HDInsight | Microsoft Docs
description: "Apprenez à utiliser Data Lake Tools pour Visual Studio avec le bac à sable Hortonworks s’exécutant sur une machine virtuelle locale. Ces outils vous permettent de créer et d’exécuter des travaux Hive et Pig sur le bac à sable, ainsi que d’en afficher le résultat et l’historique."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/07/2018
ms.author: larryfr
ms.openlocfilehash: 7c79e66c6ee4d5aa3ffd052306f57666df866313
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utiliser Azure Data Lake Tools pour Visual Studio avec le Bac à sable (sandbox) Hortonworks

Azure Data Lake inclut des outils permettant de travailler avec des clusters Hadoop génériques. Ce document décrit les étapes nécessaires pour utiliser Data Lake Tools avec le Bac à sable (sandbox) Hortonworks s’exécutant sur une machine virtuelle locale.

Hortonworks Sandbox permet de travailler avec Hadoop localement sur votre environnement de développement. Après avoir développé une solution, lorsque vous souhaitez la déployer à grande échelle, vous pouvez passer à un cluster HDInsight.

## <a name="prerequisites"></a>Prérequis

* Le Bac à sable (sandbox) Hortonworks s’exécutant sur une machine virtuelle dans votre environnement de développement. Ce document a été écrit et testé avec le bac à sable s’exécutant sur Oracle VirtualBox. Pour plus d’informations sur le paramétrage du bac à sable, consultez [Get started with the Hortonworks sandbox](hadoop/apache-hadoop-emulator-get-started.md) (Prise en main du bac à sable Hortonworks) document.

* Visual Studio 2013, Visual Studio 2015 ou Visual Studio 2017 (toute édition).

* Le [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou version ultérieure.

* [Azure Data Lake Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configuration des mots de passe pour le bac à sable

Assurez-vous que le Bac à sable (sandbox) Hortonworks est en cours d’exécution. Puis suivez les instructions décrites dans le document [Get started with the Hortonworks sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) (Prise en main du bac à sable Hortonworks). Ces étapes permettent de configurer le mot de passe pour le compte SSH `root` et le compte Ambari `admin`. Ces mots de passe vous permettent de vous connecter au bac à sable à partir de Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Connexion des outils au bac à sable

1. Ouvrez Visual Studio, sélectionnez **Afficher**, puis **Explorateur de serveurs**.

2. Dans **l’Explorateur de serveurs**, cliquez avec le bouton droit sur l’entrée **HDInsight**, puis sélectionnez **Connexion à l’émulateur HDInsight**.

    ![Capture d’écran de l’Explorateur de serveurs avec Se connecter à l’émulateur HDInsight en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Dans la boîte de dialogue **Se connecter à l’émulateur HDInsight**, entrez le mot de passe que vous avez configuré pour Ambari.

    ![Capture d’écran de la boîte de dialogue avec la zone de texte pour la saisie du mot de passe en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Sélectionnez **Suivant** pour continuer.

4. Utilisez le champ **Mot de passe** pour entrer le mot de passe que vous avez configuré pour le compte `root`. Conservez la valeur par défaut dans les autres champs.

    ![Capture d’écran de la boîte de dialogue avec la zone de texte pour la saisie du mot de passe en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Sélectionnez **Suivant** pour continuer.

5. Attendez que la validation des services s’achève. Dans certains cas, la validation peut échouer et vous inviter à mettre à jour la configuration. Si la validation échoue, sélectionnez **Mettre à jour**, puis attendez la fin de la configuration et de la vérification du service.

    ![Capture d’écran de la boîte de dialogue avec le bouton Mettre à jour en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Le processus de mise à jour utilise Ambari pour remplacer la configuration du Bac à sable (sandbox) Hortonworks par celle attendue par Data Lake Tools pour Visual Studio.

6. Une fois la validation terminée, sélectionnez **Terminer** pour achever la configuration.
    ![Capture d’écran de la boîte de dialogue avec le bouton Terminer en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > Selon la vitesse de votre environnement de développement et la quantité de mémoire allouée à la machine virtuelle, la configuration et la validation des services peuvent prendre quelques minutes.

Une fois ces étapes accomplies, une entrée **HDInsight local cluster** apparaît dans la section **HDInsight** de l’Explorateur de serveurs.

## <a name="write-a-hive-query"></a>Écriture d’une requête Hive

Hive fournit un langage de requête de type SQL (HiveQL) pour le traitement des données structurées. Suivez les instructions suivantes pour apprendre à exécuter des requêtes à la demande sur le cluster local.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur l’entrée du cluster local que vous avez ajouté précédemment, puis sélectionnez **Écrire une requête Hive**.

    ![Capture d’écran de l’Explorateur de serveurs avec Écrire une requête Hive en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Une nouvelle fenêtre de requête s’affiche. Vous pouvez alors rapidement rédiger et envoyer une requête vers le cluster local.

2. Dans la nouvelle fenêtre de requête, entrez la commande suivante :

        select count(*) from sample_08;

    Pour exécuter la requête, sélectionnez **Envoyer** en haut de la fenêtre. Laissez les autres valeurs (**lot** et nom du serveur) par défaut.

    ![Capture d’écran de la fenêtre de requête avec le bouton Envoyer en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Vous pouvez également utiliser le menu déroulant en regard **d’Envoyer** pour sélectionner **Avancé**. Les options avancées vous permettent de spécifier des options supplémentaires lors de l’envoi du travail.

    ![Capture d’écran de la boîte de dialogue Envoyer le script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Une fois la requête envoyée, l’état du travail s’affiche. Celui-ci présente des informations sur le travail tel que Hadoop l’a traité. Le champ **État du travail** indique l’état du travail. Cet état est mis à jour régulièrement. Vous pouvez également utiliser l’icône d’actualisation pour rafraîchir l’état manuellement.

    ![Capture d’écran de la boîte de dialogue Vue du travail, avec État du travail en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Quand **État du travail** passe à **Terminé**, un graphe orienté acyclique (DAG) s’affiche. Ce diagramme décrit le chemin d’exécution déterminé par Tez lors du traitement de la requête Hive. Tez est le moteur d’exécution par défaut pour Hive sur le cluster local.

    > [!NOTE]
    > Tez est également le moteur par défaut lorsque vous utilisez des clusters HDInsight basés sur Linux. Il n’est pas le moteur par défaut pour HDInsight basé sur Windows. Pour l’utiliser dans cette configuration, vous devez ajouter la ligne `set hive.execution.engine = tez;` au début de votre requête Hive.

    Utilisez le lien **Sortie de la tâche** pour afficher la sortie. En l’occurrence, il s’agit de 823, soit le nombre de lignes contenues dans la table sample_08. Vous pouvez afficher des informations de diagnostic sur la tâche à l’aide des liens **Journal de la tâche** et **Download YARN Log (Télécharger le journal YARN)**.

4. Vous pouvez également exécuter des travaux Hive de façon interactive en définissant le champ **Lot** sur **Interactif**. Sélectionnez ensuite **Exécuter**.

    ![Capture d’écran avec les boutons Interactif et Exécuter en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Une requête interactive affiche le journal de sortie généré lors du traitement dans la fenêtre **HiveServer2 Output** (Sortie HiveServer2).

    > [!NOTE]
    > Ces informations sont identiques à celles accessibles via le lien **Journal du travail** une fois le travail terminé.

    ![Capture d’écran du journal de sortie](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Création d’un projet Hive

Vous pouvez également créer un projet qui contient plusieurs scripts Hive. Utilisez un projet lorsque vous possédez des scripts associés ou que vous souhaitez stocker des scripts dans un système de gestion de version.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau**, puis **Projet**.

2. Dans la liste des projets, développez **Modèles**, **Azure Data Lake**, puis sélectionnez **HIVE (HDInsight)**. Dans la liste des modèles, sélectionnez **Hive Sample (Exemple Hive)**. Entrez un nom et un emplacement, puis sélectionnez **OK**.

    ![Capture d’écran de la fenêtre Nouveau projet avec Azure Data Lake, HIVE, Hive Sample (Exemple Hive) et OK en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Le projet **Hive Sample (Exemple Hive)** contient deux scripts, **WebLogAnalysis.hql** et **SensorDataAnalysis.hql**. Vous pouvez les envoyer à l’aide du même bouton **Envoyer** en haut de la fenêtre.

## <a name="create-a-pig-project"></a>Création d’un projet Pig

Contrairement à Hive qui offre un langage de type SQL pour travailler avec des données structurées, Pig effectue des transformations sur les données. Pig fournit un langage (Pig Latin) permettant de développer un pipeline des transformations. Pour utiliser Pig avec le cluster local, procédez comme suit :

1. Ouvrez Visual Studio et sélectionnez **Fichier**, **Nouveau**, puis **Projet**. Dans la liste des projets, développez **Modèles**, **Azure Data Lake**, puis sélectionnez **Pig (HDInsight)**. Dans la liste des modèles, sélectionnez **Pig Application (Application Pig)**. Entrez un nom et un emplacement, puis sélectionnez **OK**.

    ![Capture d’écran de la fenêtre Nouveau projet avec Azure Data Lake, Pig, Pig Application (Application Pig) et OK en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Entrez le texte suivant comme contenu du fichier **script.pig** créé dans le cadre de ce projet.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Bien que Pig n’utilise pas le même langage que Hive, le mode d’exécution des travaux via le bouton **Envoyer** est cohérent pour les deux langages. La sélection de la liste déroulante en regard de **Envoyer** permet d’afficher une boîte de dialogue Envoyer contenant des options avancées pour Pig.

    ![Capture d’écran de la boîte de dialogue Envoyer le script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. L’état du travail et la sortie s’affichent de la même façon que pour une requête Hive.

    ![Capture d’écran d’un travail Pig terminé](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Affichage des tâches

Les outils Data Lake Tools permettent également d’afficher facilement les informations relatives aux travaux exécutés sur Hadoop. Pour afficher les travaux exécutés sur le cluster local, procédez comme suit.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur le cluster local, puis sélectionnez **Afficher les travaux**. Une liste des tâches qui ont été soumises au cluster s’affiche.

    ![Capture d’écran de l’Explorateur de serveurs avec Afficher les travaux en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Dans la liste des tâches, sélectionnez-en une pour en afficher les détails.

    ![Capture d’écran de l’Explorateur de travaux avec l’un des travaux en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Les informations affichées sont similaires à celles qui s’affichent après l’exécution d’une requête Hive ou Pig. Elles incluent des liens permettant d’afficher les informations de sortie et de journal.

3. Vous pouvez également modifier et soumettre à nouveau la tâche à partir d’ici.

## <a name="view-hive-databases"></a>Affichage des bases de données Hive

1. Dans **l’Explorateur de serveurs**, développez l’entrée **cluster local HDInsight**, puis développez **Bases de données Hive**. Les bases de données **Par défaut** et **xademo** sur le cluster local sont affichées. Le développement d’une base de données a pour effet d’afficher les tables que celle-ci contient.

    ![Capture d’écran de l’Explorateur de serveurs avec des bases de données développées](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Le développement d’une table affiche les colonnes qu’elle contient. Pour afficher rapidement les données, cliquez avec le bouton droit sur une table, puis sélectionnez **Afficher les 100 premières lignes**.

    ![Capture d’écran de l’Explorateur de serveurs avec une table développée et l’option Afficher les 100 premières lignes sélectionnée](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriétés de base de données et de table

Vous pouvez afficher les propriétés d’une base de données ou d’une table. La sélection de **Propriétés** affiche les détails de l’élément sélectionné dans la fenêtre Propriétés. Par exemple, examinez les informations affichées dans la capture d’écran suivante :

![Capture d’écran de le fenêtre Propriétés](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Création d’une table

Pour créer une table, cliquez avec le bouton droit sur une base de données, puis sélectionnez **Créer une table**.

![Capture d’écran de l’Explorateur de serveurs avec Créer une table en surbrillance](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Vous pouvez ensuite créer la table à l’aide d’un formulaire. Au bas de la capture d’écran suivante, vous pouvez voir le HiveQL brut utilisé pour créer la table.

![Capture d’écran du formulaire utilisé pour créer une table](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Étapes suivantes

* [Se familiariser avec Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
