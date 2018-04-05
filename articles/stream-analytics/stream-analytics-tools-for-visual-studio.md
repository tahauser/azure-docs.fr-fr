---
title: Utiliser Azure Stream Analytics Tools pour Visual Studio | Microsoft Docs
description: Didacticiel pour bien démarrer avec les outils Azure Stream Analytics pour Visual Studio
keywords: visual studio
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ryanw
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sujie
ms.openlocfilehash: 23c4d058a1b045b0d66d8526a94e22774e6e6519
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Utiliser Azure Stream Analytics Tools pour Visual Studio
Les outils Azure Stream Analytics pour Visual Studio sont maintenant disponibles de manière générale. Ces outils permettent d’enrichir l’expérience de l’utilisateur Stream Analytics pour la résolution de problèmes, les requêtes complexes et même l’écriture de requêtes en local. Vous pouvez également exporter un travail Stream Analytics dans un projet Visual Studio.

## <a name="introduction"></a>Introduction
Dans ce didacticiel, vous apprenez à utiliser les outils Stream Analytics pour Visual Studio afin de créer, fournir du contenu, tester localement, gérer et déboguer vos travaux Stream Analytics. Après avoir créé la tâche, vous pouvez configurer un processus d’intégration et de déploiement continu dans Azure à l’aide du package Nuget CI/CD. Pour en savoir plus, consultez l’article [Stream Analytics VS outils pour configurer un pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md).

Après avoir effectué ce didacticiel, vous pourrez :

* bien connaître les outils Stream Analytics pour Visual Studio ;
* configurer et déployer un travail Stream Analytics ;
* tester votre travail en local avec des exemples de données locaux ;
* utiliser la surveillance pour résoudre les problèmes ;
* exporter des travaux existants dans des projets.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin des prérequis suivants :

* Accomplissez les étapes qui précèdent « Créer une tâche Stream Analytics » du didacticiel [Créer une solution IoT à l’aide de Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Installez Visual Studio 2017, Visual Studio 2015 ou Visual Studio 2013 Update 4. Les éditions Enterprise (Ultimate/Premium), Professional et Community sont prises en charge. L’édition Express n’est pas prise en charge. 
* Suivez les [instructions d’installation](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) pour installer les outils Stream Analytics pour Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Créer un projet Stream Analytics
Dans Visual Studio, sélectionnez **Fichier** > **Nouveau projet**. Dans la liste des modèles sur la gauche, sélectionnez **Stream Analytics**, puis **Application Azure Stream Analytics**.
En bas de la page, entrez le **nom** du projet, l’**emplacement** et le **nom de la solution**, comme vous le feriez pour d’autres projets.

![Création de projet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Le projet **Toll** est généré dans l’**Explorateur de solutions**.

![Projet Toll dans l’Explorateur de solutions](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Choisir le bon abonnement
1. Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de serveurs**.

2. Connectez-vous à votre compte Azure. 

## <a name="define-input-sources"></a>Définir les sources d’entrée
1. Dans l’**Explorateur de solutions**, développez le nœud **Entrées** et renommez **Input.json** en **EntryStream.json**. Double-cliquez sur **EntryStream.json**.

2. Pour l’**alias d’entrée**, saisissez **EntryStream**. Notez que l’alias d’entrée est utilisé dans le script de requête.

3. Pour **Type de source**, sélectionnez **Flux de données**.

4. Pour **Source**, sélectionnez **Event Hub**.

5. Pour **Espace de noms Service Bus**, sélectionnez l’option **TollData** dans la liste déroulante.

6. Pour **Nom du hub d’événements**, sélectionnez **entry**.

7. Pour **Nom de la stratégie du hub d’événements**, sélectionnez **RootManageSharedAccessKey** (la valeur par défaut).

8. Pour **Format de sérialisation d’événement**, sélectionnez **Json** et, pour **Encodage**, sélectionnez **UTF8**.
   
   Vos paramètres ressemblent à ceci :
   
   ![Paramètres d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. Cliquez sur **Enregistrer** en bas de la page pour terminer l’exécution de l’Assistant. Vous pouvez maintenant ajouter une autre source d’entrée pour créer le flux de sortie. Cliquez avec le bouton droit sur le nœud **Entrées** et sélectionnez **Nouvel élément**.
   
   ![Nouvel élément](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. Dans la fenêtre contextuelle, choisissez **Entrée Azure Stream Analytics** et remplacez le **nom** par **ExitStream.json**. Sélectionnez **Ajouter**.
   
    ![Ajouter un nouvel élément](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Double-cliquez sur **ExitStream.json** dans le projet et suivez la même procédure que le flux d’entrée pour renseigner les champs. Pour **Nom du hub d’événements**, veillez à entrer **exit**, comme indiqué sur la capture d’écran suivante :
   
    ![Paramètres ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Vous avez maintenant défini deux flux d’entrée.
   
   ![Deux flux d’entrée](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   À présent, vous ajoutez une entrée de données de référence pour le fichier blob contenant les données d’inscription des véhicules.
   
12. Cliquez avec le bouton droit sur le nœud **Entrées** dans le projet, puis suivez la même procédure que pour les entrées de flux de données. Pour **Alias d’entrée**, entrez **Registration** et, pour **Type de Source**, sélectionnez **Reference data**.
   
    ![Paramètres d’inscription](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Sélectionnez le compte de **stockage** qui contient l’option avec **TollData**. Le nom de conteneur doit être **TollData**, et le **Modèle de chemin d’accès** doit être **registration.json**. Ce nom de fichier respecte la casse et doit être en minuscules.

14. Sélectionnez **Enregistrer** pour terminer l’exécution de l’Assistant.

À présent, toutes les entrées sont définies.

## <a name="define-output"></a>Définir la sortie
1. Dans l’**Explorateur de solutions**, développez le nœud **Entrées** et double-cliquez sur **Output.json**.

2. Pour **Alias de sortie**, entrez **output**. Pour **Récepteur**, sélectionnez **SQL Database**.

3. Pour le nom de la **base de données**, entrez **TollDataDB**.

4. Pour **Nom d’utilisateur**, entrez **tolladmin**. Pour **Mot de passe**, entrez **123toll!**. Pour **Table**, entrez **TollDataRefJoin**.

5. Sélectionnez **Enregistrer**.

   ![Paramètres de sortie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Requête Stream Analytics
Ce didacticiel tente de répondre à différentes questions liées aux données de péage. Nous avons également élaboré des requêtes utilisables dans Stream Analytics pour fournir des réponses pertinentes. Avant de commencer votre premier travail Stream Analytics, examinons un scénario simple et la syntaxe de requête.

### <a name="introduction-to-stream-analytics-query-language"></a>Présentation du langage de requête de Stream Analytics
Supposons que nous devons compter le nombre de véhicules qui entrent dans un poste de péage. Comme il s’agit d’un flux continu d’événements, nous devons définir une période. Modifions la question en « Combien de véhicules entrent en poste de péage en trois minutes ? ». On appelle communément cette mesure le « nombre bascule ».

Examinons la requête Stream Analytics répondant à cette question :

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Comme vous pouvez le voir, Stream Analytics utilise un langage de requête semblable à SQL. Il ajoute quelques extensions pour spécifier les aspects temporels de la requête.

Pour plus de détails, consultez les constructions de [gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et de [fenêtrage](https://msdn.microsoft.com/library/azure/dn835019.aspx) utilisées dans la requête à partir de MSDN.

Une fois votre première requête Stream Analytics écrite, testez-la à l’aide des exemples de fichiers de données figurant dans votre dossier TollApp au chemin suivant :

**..\TollApp\TollApp\Data**

Ce dossier contient les fichiers suivants :

* Entry.json
* Exit.json
* registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Question : nombre de véhicules arrivant à un poste de péage
Dans le projet, double-cliquez sur **Script.asaql** pour ouvrir le script dans le l’éditeur. Collez le script de la section précédente dans l’éditeur. L’éditeur de requête prend en charge IntelliSense, la coloration syntaxique et le marquage d’erreurs.

![Éditeur de requête](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Tester localement les requêtes Stream Analytics
Vous pouvez tout d’abord compiler la requête pour voir si elle comporte une erreur de syntaxe.

1. Pour valider cette requête par rapport aux exemples de données, utilisez les exemples de données locaux en double-cliquant sur l’entrée et en sélectionnant **Ajouter une entrée locale**.
   
   ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. Dans la fenêtre indépendante, sélectionnez l’exemple de données à partir de votre chemin local. Sélectionnez **Enregistrer**.
   
   ![Ajouter une entrée locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Un fichier nommé **local_EntryStream.json** est automatiquement ajouté à votre dossier d’entrées.
   
   ![Liste des fichiers du dossier d’entrées locales](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Dans l’éditeur de requête, sélectionnez **Exécution locale**. Ou vous pouvez appuyer sur F5.
   
   ![Exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Vous pouvez trouver le chemin de sortie à partir de la sortie de console. Appuyez sur n’importe quelle touche pour ouvrir le dossier des résultats.
   
   ![Exécution locale](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Vérifiez les résultats dans le dossier local.
   
   ![Résultat du dossier local](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Exemple d’entrée
Vous pouvez également constituer des exemples de données d’entrée à partir de sources d’entrée dans un fichier local. Cliquez avec le bouton droit sur le fichier de configuration d’entrée et sélectionnez **Exemple de données**. 

![Exemple de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Pour l’instant, vous pouvez uniquement échantillonner les hubs d’événements ou les hubs IoT. Les autres sources d’entrée ne sont pas prises en charge. Dans la boîte de dialogue contextuelle, entrez le chemin local utilisé pour enregistrer les exemples de données. Sélectionnez **Exemple**.

![Configuration des exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Vous pouvez voir la progression de l’opération dans la fenêtre **Sortie**. 

![Sortie des exemples de données](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Envoyer une requête Stream Analytics sur Azure
1. Dans l’**éditeur de requête**, sélectionnez **Envoyer sur Azure** dans l’éditeur de script.

   ![Envoyer sur Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Sélectionnez **Créer une tâche Azure Stream Analytics**. Pour **Nom du travail**, saisissez **TollApp**. Choisissez le bon **abonnement** dans la liste déroulante. Sélectionnez **Envoyer**.

   ![Envoi du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Démarrage du travail
Dès lors que votre travail est créé, sa vue s’ouvre automatiquement. 
1. Pour commencer ce travail, cliquez sur le bouton à la flèche verte.

   ![Bouton de démarrage du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Choisissez le paramètre par défaut, puis sélectionnez **Démarrer**.
 
   ![Démarrage du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Vous pouvez voir que l’état du travail est passé à **En cours d’exécution** et qu'il existe des événements d’entrée/de sortie.

   ![Mesures et résumé du travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Vérifier les résultats dans Visual Studio
1. Ouvrez l’Explorateur de serveurs Visual Studio, puis cliquez avec le bouton droit sur la table **TollDataRefJoin** .

2. Sélectionnez **Afficher les données de la table** pour voir le résultat de votre tâche.
   
   ![Affichage des données de table](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Affichage des mesures de travail
Certaines statistiques des travaux de base se trouvent dans **Métriques de tâche**. 

![Mesures de travail](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Répertorier la tâche dans l’Explorateur de serveurs
Dans l’**Explorateur de serveurs**, sélectionnez **Tâches Stream Analytics**, puis **Actualiser**. Le travail s’affiche sous **Tâches Stream Analytics**.

![Liste des tâches](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Ouvrir la vue de la tâche
Développez le nœud de votre travail et double-cliquez sur le nœud **Vue des travaux** pour ouvrir une vue des travaux.

![Vue des travaux](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exporter un travail existant dans un projet
Il existe deux méthodes pour exporter un travail existant vers un projet.
* Dans l’**Explorateur de serveurs**, sous le nœud **Tâches Stream Analytics Jobs**, cliquez avec le bouton droit sur le nœud du travail. Sélectionnez **Exporter vers un nouveau projet Stream Analytics**.
   
   ![Exporter vers un nouveau projet Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Le projet généré apparaît dans **l’Explorateur de solutions**.
   
    ![Travail de l’Explorateur de solutions](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* Dans la vue des travaux, sélectionnez **Générer le projet**.
   
   ![Générer le projet](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations
 
* Le test local ne fonctionne pas si votre requête contient des fonctions géospatiales.
* L’éditeur n’est pas pris en charge pour l’ajout ou la modification de fonctions définies par l'utilisateur JavaScript.
* Le test local ne prend pas en charge l’enregistrement de la sortie au format JSON. 
* Les sorties Power BI et les sorties ADLS ne sont pas prises en charge.



## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Bien démarrer avec Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


