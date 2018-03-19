---
title: "Se connecter à Azure HDInsight avec Data Lake Tools pour Visual Studio | Microsoft Docs"
description: "Découvrez comment installer et utiliser Data Lake Tools pour Visual Studio pour vous connecter aux clusters Hadoop dans Azure HDInsight et exécuter des requêtes Hive."
keywords: "outils Hadoop,requête hive,Visual Studio,Hadoop Visual Studio"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: afd40d75bb9c5fd3170a4da215925244994d7749
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Utiliser Data Lake Tools pour Visual Studio pour se connecter à Azure HDInsight et exécuter des requêtes Hive

Découvrez comment utiliser Data Lake Tools pour Visual Studio (aussi appelé Azure Data Lake et Stream Analytics Tools) pour vous connecter aux clusters Hadoop dans [Azure HDInsight](../hdinsight-hadoop-introduction.md) et envoyer des requêtes Hive. 

Pour plus d’informations sur l’utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight](../hdinsight-hadoop-introduction.md) et [Prise en main de HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Pour plus d’informations sur la connexion au cluster Storm, consultez [Développement de topologies C# pour Apache Storm sur HDInsight à l’aide de Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Vous pouvez utiliser Data Lake Tools pour Visual Studio pour accéder à Azure Data Lake Analytics et à HDInsight. Pour plus d’informations sur Data Lake Tools, consultez le [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel et utiliser Data Lake Tools for Visual Studio, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight. Pour créer un cluster Azure HDInsight, consultez [Prise en main Hadoop dans Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pour exécuter des requêtes Hive interactives, il vous faut un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).
* Un ordinateur équipé de Visual Studio 2017, 2015 ou 2013 est installé.
    
    > [!NOTE]
    > Actuellement, seule la version anglaise de Data Lake Tools pour Visual Studio est disponible.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Installer ou mettre à jour Data Lake Tools pour Visual Studio

### <a name="install-data-lake-tools"></a>Installer Data Lake Tools

Data Lake Tools est installé par défaut pour Visual Studio 2017. Pour des versions antérieures de Visual Studio, vous pouvez installer Data Lake Tools à l’aide de [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx). Choisissez la version de Data Lake Tools qui correspond à celle de Visual Studio. 

### <a name="install-visual-studio"></a>Installation de Visual Studio

Si vous n’avez pas installé Visual Studio, utilisez [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) pour installer les dernières versions de Visual Studio Community et le kit de développement logiciel (SDK) Azure :

![Capture d’écran de Web Platform Installer de Data Lake Tools pour Visual Studio.](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Utilisez Web Platform Installer pour installer Data Lake Tools pour Visual Studio")

### <a name="update-the-tools"></a>Mettre à jour Data Lake Tools

1. Ouvrez Visual Studio.
2. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour**.
3. Développez **Mises à jour** puis sélectionnez **Azure Data Lake et Stream Analytics Tools** (si installé).

> [!NOTE]
>
> Vous ne pouvez utiliser que la version 2.3.0.0 ou ultérieure de Data Lake Tools pour vous connecter aux clusters Interactive Query et exécuter des requêtes Hive interactives.

## <a name="connect-to-azure-subscriptions"></a>Se connecter aux abonnements Azure
Vous pouvez utiliser Data Lake Tools pour Visual Studio pour vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

> [!NOTE]
> Pour plus d’informations sur la connexion à un cluster Hadoop générique, consultez [Écriture et soumission de requêtes Hive à l’aide de Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).
> 
> 

Pour vous connecter à votre abonnement Azure :

1. Ouvrez Visual Studio.
2. Dans le menu **Vue**, sélectionnez **Explorateur de serveurs**.
3. Dans l’Explorateur de serveurs, développez **Azure**, puis **HDInsight**.
   
   > [!NOTE]
   > La fenêtre **Liste des tâches HDInsight** doit s’ouvrir. Si elle ne s’ouvre pas, dans le menu **Vue**, sélectionnez **Autres fenêtres** puis **Fenêtre Liste des tâches HDInsight**.  
   > 
   > 
4. Entrez les informations d’identification de votre abonnement Azure, puis sélectionnez **Se connecter**. L’authentification n’est obligatoire que si vous ne vous êtes jamais connecté à l’abonnement Azure à partir de Visual Studio sur cet ordinateur.
5. Dans l’Explorateur de serveurs, une liste des clusters HDInsight existants apparaît. Si vous ne possédez aucun cluster, vous pouvez en créer un dans le portail Azure, avec Azure PowerShell ou à l’aide du Kit de développement logiciel (SDK) HDInsight. Pour plus d’informations, consultez la rubrique [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Capture d’écran de la Liste de clusters de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Liste de clusters de l’explorateur de serveurs de Data Lake Tools pour Visual Studio")
6. Développez un cluster HDInsight. Les **bases de données Hive**, un compte de stockage par défaut, les comptes de stockage liés et le **journal Hadoop Service** apparaissent. Vous pouvez développer davantage les entités.

Une fois connecté à votre abonnement Azure, vous êtes en mesure d’effectuer les tâches suivantes.

Pour vous connecter au portail Azure à partir de Visual Studio :

1. Dans l’Explorateur de serveurs, sélectionnez **Azure** > **HDInsight**.
2. Cliquez avec le bouton droit sur un cluster HDInsight, puis sélectionnez **Gérer le cluster dans le portail Azure**.

Pour poser des questions et envoyer des commentaires à partir de Visual Studio :

1. Dans le menu **Outils**, sélectionnez **HDInsight**.
2. Pour poser des questions, sélectionnez **Forum MSDN**. Pour envoyer des commentaires, sélectionnez **Envoyer un commentaire**.

## <a name="explore-linked-resources"></a>Explorer des ressources liées
Dans l’Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Cliquez avec le bouton droit sur n’importe quel conteneur pour afficher son contenu.

![Capture d’écran des Ressources liées de la liste de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Ressources liées de la liste")

Après l’ouverture d’un conteneur, vous pouvez utiliser les boutons suivants pour charger, supprimer et télécharger des objets blob :

![Capture d’écran des Opérations blob de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Charger, supprimer et télécharger des objets blob dans l’explorateur de serveurs")

## <a name="run-interactive-hive-queries"></a>Exécuter des requêtes Hive interactives
[Apache Hive](http://hive.apache.org) est une infrastructure d’entrepôt de données construite sur Hadoop. Hive est utilisée pour le résumé, les requêtes et l’analyse des données. Vous pouvez utiliser Data Lake Tools pour Visual Studio pour exécuter des requêtes Hive à partir de Visual Studio. Pour plus d’informations sur Hive, consultez l’article [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) utilise [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) dans Apache Hive 2.1. Interactive Query permet l’interactivité dans des requêtes d’entrepôt de données complexes sur des jeux de données volumineux stockés. L’exécution de requêtes Hive sur Interactive Query est beaucoup plus rapide que les programmes de traitement par lots Hive traditionnels. Pour plus d’informations, consultez [Exécuter des programmes de traitement par lots Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> Vous ne pouvez exécuter des requêtes Hive interactives que lorsque vous vous connectez à un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Vous pouvez aussi utiliser Data Lake Tools pour Visual Studio pour voir ce que contient la tâche Hive. Data Lake Tools pour Visual Studio collecte et fait apparaître les journaux Yarn de certaines tâches Hive.

### <a name="view-hivesampletable"></a>Afficher **hivesampletable**
Tous les clusters HDInsight disposent d’un exemple de table Hive par défaut nommé hivesampletable. Ce tableau Hive définit comment répertorier des tables Hive, afficher des schémas de table et répertorier les lignes de la table Hive.

Pour répertorier les tables Hive et afficher le schéma de la table Hive :

1. Pour voir le schéma de la table, dans l’**Explorateur de serveurs**, sélectionnez **Azure** > **HDInsight**. Sélectionnez votre cluster, puis **Bases de données Hive** > **Par défaut** > **hivesampletable**.
2. Cliquez avec le bouton droit sur **hivesampletable**, puis cliquez sur **Afficher les 100 premières lignes** pour répertorier les lignes. Cela revient à exécuter la requête Hive suivante à l’aide du pilote ODBC Hive :
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   Vous pouvez personnaliser le nombre de lignes.
   
   ![Capture d’écran d’une requête de schéma Hive dans HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Résultats de la requête Hive")

### <a name="create-hive-tables"></a>Créer des tables Hive
Vous pouvez utiliser des requêtes Hive ou utiliser la GUI pour créer une table Hive. Pour plus d’informations relatives à l’utilisation de requêtes Hive, consultez [Exécution de requêtes Hive](#run.queries).

Pour créer une table Hive :

1. Dans l’**Explorateur de serveurs**, sélectionnez **Azure** > **Clusters HDInsight**. Sélectionnez votre cluster HDInsight, puis **Bases de données Hive**.
2. Cliquez avec le bouton droit sur **Par défaut**, puis sélectionnez **Créer une table**.
3. Configurez la table.  
4. Sélectionnez **Créer une table** pour envoyer la tâche de création d’une table Hive.
   
    ![Capture d’écran de la fenêtre de création d’une table Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Créer une table Hive")

### <a name="run.queries"></a>Validation et exécution de requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux façons :

* Création de requêtes ad hoc
* Création d’une application Hive

Pour créer, valider et exécuter des requêtes ad hoc :

1. Dans l’**Explorateur de serveurs**, sélectionnez **Azure** > **Clusters HDInsight**.
2. Cliquez avec le bouton droit sur le cluster dans lequel vous souhaitez exécuter la requête, puis sélectionnez **Écrire une requête Hive**.  
3. Entrez les requêtes Hive. 

    L’éditeur Hive prend en charge IntelliSense. Data Lake Tools pour Visual Studio prend en charge le chargement des métadonnées distantes pendant la modification d’un script Hive. Par exemple, si vous tapez **SELECT * FROM**, IntelliSense répertorie tous les noms de table suggérés. Lorsqu’un nom de table est spécifié, IntelliSense répertorie les noms de colonne. Les outils prennent en charge la plupart des instructions DML, sous-requêtes et fonctions définies par l’utilisateur intégrées de Hive.
   
    ![Capture d’écran de l’exemple 1 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "IntelliSense U-SQL")
   
    ![Capture d’écran de l’exemple 2 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "IntelliSense U-SQL")
   
   > [!NOTE]
   > IntelliSense propose uniquement les métadonnées du cluster sélectionné dans la barre d’outils HDInsight.
   > 
   
4. Pour vérifier l’absence d’erreurs de syntaxe dans le script, cliquez sur **Valider le script** (optionnel).
   
    ![Capture d’écran de la validation locale de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Valider le script")
5. Sélectionnez **Envoyer** ou **Envoyer (Avancé)**. Si vous sélectionnez l’option d’envoi avancé, configurez les éléments **Nom de la tâche**, **Arguments**, **Configurations supplémentaires** et **Répertoire d’état** pour le script :
   
    ![Capture d’écran de la Requête HDInsight Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Soumettre des requêtes")
   
    Une fois la tâche soumise, la fenêtre **Résumé de tâche Hive** s’affiche.
   
    ![Capture d’écran d’un résumé d’une requête HDInsight Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Résumé de la tâche Hive")
6. Utilisez le bouton **Actualiser** pour mettre à jour l’état jusqu’à ce qu’il passe à **Terminé**.
7. Cliquez sur les liens situés dans la partie inférieure pour consulter la **requête de tâche**, la **sortie de la tâche**, le **journal de la tâche** ou le **Journal Yarn**.

Pour créer et exécuter une solution Hive :

1. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**.
2. Dans le volet gauche, sélectionnez **HDInsight**. Dans le volet central, sélectionnez **Application Hive**. Entrez les propriétés, puis sélectionnez **OK**.
   
    ![Capture d’écran d’un nouveau projet Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Créer des applications Hive à partir de Visual Studio")
3. Dans l’**Explorateur de solutions**, double-cliquez sur le script **Script.hql** pour l’ouvrir.
4. Pour valider le script Hive, sélectionnez le bouton **Valider le script**. Vous pouvez aussi cliquez avec le bouton droit sur le script dans l’éditeur Hive, puis sélectionnez **Valider le script** dans le menu contextuel.

### <a name="view-hive-jobs"></a>Afficher les tâches Hive
Vous pouvez afficher les requêtes, la sortie, le journal et le journal Yarn des tâches Hive. Pour plus d’informations, consultez la capture d’écran précédente.

Dans la version la plus récente des outils, vous pouvez consulter le contenu de vos tâches Hive en collectant et en exposant les journaux YARN. Le journal YARN peut vous aider à examiner les problèmes de performances. Pour plus d’informations sur la collection des journaux YARN par HDInsight, consultez [Accès par programme aux journaux des applications HDInsight](../hdinsight-hadoop-access-yarn-app-logs.md).

Pour afficher les tâches Hive :

1. Dans l’**Explorateur de serveurs**, développez **Azure**, puis **HDInsight**.
2. Cliquez avec le bouton droit sur un cluster HDInsight, puis sélectionnez **Afficher les tâches**. Une liste des tâches Hive exécutées sur le cluster s’affiche.  
3. Sélectionnez une tâche. Dans la fenêtre **Résumé de la tâche Hive**, sélectionnez l’un des éléments suivants :
    - **Requête de tâche**
    - **Sortie de travail**
    - **Journal de la tâche**  
    - **Journal YARN**
   
    ![Capture d’écran de la fenêtre d’affichage des tâches Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Afficher les tâches Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Plus grande rapidité d’exécution de Hive via HiveServer2
> [!NOTE]
> Cette fonctionnalité fonctionne uniquement dans un cluster dans la version 3.2 ou supérieure de HDInsight.
 
L’application Data Lake Tools envoyait des tâches Hive par le biais de [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (également appelé Templeton). Cette méthode d’envoi des tâches Hive prenait beaucoup de temps pour renvoyer les détails d’une tâche et les informations d’erreur.

Pour résoudre ce problème de performances, Data Lake Tools pour Visual Studio peut contourner RDP/SSH et exécuter des tâches Hive directement dans le cluster via HiveServer2.

Avec cette méthode, en plus de bénéficier de meilleures performances, vous pouvez aussi afficher Hive sur des graphiques Apache Tez et dans les détails de la tâche.

Dans un cluster dans HDInsight version 3.2 ou ultérieure, un bouton **Exécuter par le biais de HiveServer2** s’affiche :

![Capture d’écran de l’option Exécuter par le biais de HiveServer2 dans Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Exécuter des requêtes Hive avec HiveServer2")

Vous pouvez aussi consulter les journaux diffusés en temps réel. Vous pouvez aussi consulter les graphiques de tâche, si la requête Hive est exécutée dans Tez.

![Capture d’écran de la fenêtre Plus grande rapidité d’exécution de Hive via HiveServer2 dans Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Afficher les graphiques de tâches")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Exécuter des requêtes via HiveServer2 et envoyer des requêtes via WebHCat

Même si l’exécution de requêtes via HiveServer2 présente de nombreux avantages en termes de performances, cette méthode s’accompagne également de certaines restrictions. Certaines de ces restrictions rendent cette méthode inadaptée pour une utilisation en production. 

Le tableau suivant présente les différences entre l’exécution de requêtes via HiveServer2 et l’envoi de requêtes via WebHCat :

|  | Exécuter via HiveServer2 | Envoyer via WebHCat |
| --- | --- | --- |
| Exécuter des requêtes |Élimine la surcharge de WebHCat (qui lance une tâche MapReduce nommée TempletonControllerJob). |Si une requête est exécutée via WebHCat, WebHCat lance une tâche MapReduce qui introduira une latence supplémentaire. |
| Diffuser des journaux en continu |presque en temps réel. |Les journaux d’exécution de la tâche sont disponibles uniquement une fois la tâche terminée. |
| Afficher l’historique des tâches |Si une requête est exécutée par le biais de HiveServer2, son historique des tâches (journal des tâches, résultat de la tâche) n’est pas conservé. Vous pouvez consulter l’application dans l’interface utilisateur YARN, mais avec des informations limitées. |Si une requête est exécutée par le biais de WebHCat, son historique des tâches (journal des tâches, résultat de la tâche) est conservé. Vous pouvez consulter l’historique des tâches avec Visual Studio, le kit de développement logiciel (SDK) HDInsight ou PowerShell. |
| Fermer la fenêtre |L’exécution via HiveServer2 est *synchrone*. Si les fenêtres sont fermées, l’exécution de la requête est annulée. |L’envoi via WebHCat est *asynchrone*. Vous pouvez envoyer la requête via WebHCat puis fermer Visual Studio. Vous pouvez y revenir et consulter les résultats à tout moment. |

### <a name="tez-hive-job-performance-graph"></a>Graphique de performances des travaux Hive sur Tez
Dans Data Lake Tools pour Visual Studio, vous pouvez consulter des graphiques de performances pour les tâches Hive exécutées par le moteur d’exécution Tez. Pour plus d’informations sur l’activation de Tez, voir [Utilisation de Hive dans HDInsight](hdinsight-use-hive.md). 

Après avoir envoyé une tâche Hive dans Visual Studio, ce dernier affiche le graphique lorsque cette tâche est terminée. Il se peut que vous deviez sélectionner le bouton **Actualiser** pour afficher le dernier état de la tâche.

> [!NOTE]
> Cette fonctionnalité est uniquement disponible pour un cluster dans la version 3.2.4.593 ou supérieure de HDInsight. Cette fonctionnalité ne fonctionne qu’avec des tâches terminées. Vous devez aussi envoyer la tâche via WebHCat pour pouvoir utiliser cette fonctionnalité. L’image suivante s’affiche lorsque vous exécutez votre requête via HiveServer2 : 
> 
> ![Capture d’écran du graphique de performances Hadoop Hive Tez](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Statut de tâche")

Pour vous aider à mieux comprendre votre requête Hive, l’affichage d’opérateur Hive a été ajouté à cette version. Pour afficher tous les opérateurs à l’intérieur d’un vertex, double-cliquez sur les vertex du graphique de la tâche. Vous pouvez aussi pointer vers un opérateur spécifique pour afficher plus d’informations sur ce dernier.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Vue Exécution de la tâche pour Hive sur les tâches Tez
Vous pouvez utiliser la Vue Exécution de la tâche pour Hive sur les tâches Tez pour obtenir des informations structurées et visualisées sur les tâches Hive. Vous pouvez aussi obtenir plus de détails de la tâche. En cas de problèmes de performances, vous pouvez utiliser la vue pour obtenir plus d’informations sur le problème. Par exemple, vous pouvez obtenir des informations sur le fonctionnement de chaque tâche, et des informations détaillées sur chaque tâche (lecture et écriture de données, heure de planification, de début et de fin, etc). Utilisez ces informations pour ajuster les configurations de tâche ou l’architecture du système basée sur les informations affichées.

![Capture d’écran de la fenêtre Vue de l’exécution de la tâche Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Vue de l’exécution de la tâche")

## <a name="run-hive-batch-jobs"></a>Exécuter des programmes de traitement par lots Hive
Le test de script Hive contre un cluster HDInsight demande beaucoup de temps, à l’exception du cluster Interactive Query. Ce processus peut prendre plusieurs minutes, voire plus. Data Lake Tools pour Visual Studio peut valider le script Hive localement, sans connexion à un cluster activé. Pour en savoir plus sur l’exécution de requêtes interactives, consultez [Exécuter des requêtes Hive interactives](#run-interactive-hive-queries).

Vous pouvez utiliser Data Lake Tools pour Visual Studio pour consulter le contenu de la tâche Hive en collectant et en exposant les journaux YARN de tâches Hive données.

Pour en savoir plus sur l’exécution de programmes de traitement par lots, consultez [Exécuter des requêtes Hive interactives](#run-interactive-hive-queries). Les informations de cette section s’appliquent à l’exécution de programmes de traitement par lots Hive dont le temps d’exécution est plus long.

## <a name="run-pig-scripts"></a>Exécuter des scripts Pig
Vous pouvez utiliser Data Lake Tools pour Visual Studio pour créer et envoyer des scripts Pig aux clusters HDInsight. Commencez par créer un projet Pig à partir d’un modèle. Envoyez ensuite le script aux clusters HDInsight.

## <a name="feedback-and-known-issues"></a>Commentaires et problèmes connus
* Actuellement, les résultats HiveServer2 sont affichés dans un format de texte brut, ce qui n’est pas idéal. Microsoft s’emploie à résoudre ce problème.
* Un problème où les résultats démarrés avec des valeurs null ne s’affichaient pas a été résolu. Si vous êtes bloqué sur ce problème, contactez le support technique.
* Le script HQL créé par Visual Studio est encodé selon le paramètre de région locale de l’utilisateur. Le script ne s’exécute pas correctement si vous le chargez dans un cluster en tant que fichier binaire.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser le package Data Lake Tools pour Visual Studio pour vous connecter à des clusters HDInsight de Visual Studio. Vous avez aussi appris à exécuter une requête Hive. Pour plus d’informations, voir les articles suivants :

* [Utilisation de Hadoop Hive dans HDInsight](hdinsight-use-hive.md)
* [Prise en main de Hadoop dans HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Envoi de tâches Hadoop dans HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analyse des données Twitter avec Hadoop dans HDInsight](../hdinsight-analyze-twitter-data.md)

