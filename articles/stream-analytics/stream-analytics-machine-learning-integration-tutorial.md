---
title: "Azure Stream Analytics et intégration de Machine Learning | Microsoft Docs"
description: "Comment utiliser une fonction définie par l’utilisateur et Machine Learning dans un travail Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.openlocfilehash: d06681c687f5cd3eb10d375499266c7e78be1558
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analyse des sentiments à l’aide d’Azure Stream Analytics et Azure Machine Learning
Cet article explique comment configurer rapidement un travail Azure Stream Analytics simple avec Azure Machine Learning. Vous allez utiliser un modèle d’analyse des sentiments Machine Learning de la galerie Cortana Intelligence pour analyser la diffusion des données de texte et déterminer l’évaluation des sentiments en temps réel. Cortana Intelligence Suite vous permet d’accomplir cette tâche sans vous préoccuper de la complexité inhérente à la création d’un modèle d’analyse des sentiments.

Vous pourrez appliquer les instructions de cet article dans les scénarios suivants :

* Analyse des sentiments en temps réel sur la diffusion de données Twitter en continu.
* Analyse des enregistrements de conversations de clients avec le personnel de support.
* Évaluation des commentaires sur les forums, blogs et vidéos. 
* Nombreux autres scénarios de notation prédictive en temps réel.

Dans un scénario réel, vous recevriez les données directement à partir d’un flux de données Twitter. Pour simplifier ce didacticiel, le travail Stream Analytics reçoit les tweets à partir d’un fichier CSV du stockage Blob Azure. Vous pouvez créer votre propre fichier CSV, ou vous pouvez utiliser un exemple de fichier CSV, comme dans l’image suivante :

![exemples de tweets dans un fichier CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

La tâche Streaming Analytics créée applique le modèle d’analyse des sentiments en tant que fonction définie par l’utilisateur (UDF) sur l’exemple de données de texte à partir du magasin d’objets blob. La sortie (le résultat de l’analyse des sentiments) est écrite dans le même magasin d’objets blob, mais dans un fichier CSV différent. 

La figure suivante illustre cette configuration. Comme nous l’avons indiqué, pour un scénario plus réaliste, vous pouvez remplacer le stockage d’objets blob par la diffusion de données Twitter en continu à partir d’une entrée Event Hub Azure. Vous pouvez également créer une visualisation [Microsoft Power BI](https://powerbi.microsoft.com/) en temps réel agrégée du sentiment.    

![Vue d’ensemble de l’intégration de Machine Learning dans Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Prérequis
Avant de commencer, veillez à disposer des éléments qui suivent :

* Un abonnement Azure actif.
* Un fichier CSV contenant des données. Vous pouvez télécharger le fichier indiqué précédemment à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv), ou créer votre propre fichier. Dans le cadre de cet article, nous supposons que vous utilisez le fichier de GitHub.

À un niveau général, pour effectuer les tâches décrites dans cet article, vous devez procédez comme suit :

1. Créez un compte de stockage Azure et un conteneur de stockage d’objets blob, et chargez un fichier d’entrée au format CSV dans le conteneur.
3. Ajoutez un modèle d’analyse des sentiments à partir de la galerie Cortana Intelligence à votre espace de travail Azure Machine Learning et déployez-le en tant que service web dans l’espace de travail Machine Learning.
5. Créez un travail Stream Analytics qui appelle ce service web en tant que fonction afin de déterminer le sentiment de saisie de texte.
6. Démarrez le travail Stream Analytics et observez le résultat.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Créer un conteneur de stockage et charger le fichier d’entrée CSV
Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, par exemple celui de GitHub.

1. Dans le portail Azure, cliquez sur **Créer une ressource** &gt; **Stockage** &gt; **Compte de stockage**.

   ![créer un compte de stockage](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. Indiquez un nom (`samldemo` dans l’exemple). Le nom, qui doit être unique dans Azure, ne peut contenir que des lettres minuscules et des chiffres. 

3. Spécifiez un groupe de ressources existant et un emplacement. Nous vous recommandons d’utiliser le même emplacement pour toutes les ressources créées lors de ce didacticiel.

    ![indiquer les détails du compte de stockage](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Dans le portail Azure, sélectionnez le compte de stockage. Dans le panneau du compte de stockage, cliquez sur **Conteneurs**, puis sélectionnez **+&nbsp;Conteneur** pour créer le stockage d’objets blob.

    ![créer un conteneur d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Indiquez ensuite le nom du conteneur (`azuresamldemoblob` dans cet exemple) et vérifiez que le **type d’accès** est défini sur **Blob**. Quand vous avez terminé, cliquez sur **OK**.

    ![spécifier les détails du conteneur d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. Dans le panneau **Conteneurs**, sélectionnez le nouveau conteneur pour ouvrir le panneau correspondant.

7. Cliquez sur **Télécharger**.

    ![Bouton ’Télécharger’ d’un conteneur](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Dans le panneau **Charger l’objet blob**, spécifiez le fichier CSV que vous souhaitez utiliser pour ce didacticiel. Dans la section **Type d’objet blob**, sélectionnez **objet blob de blocs** et définissez la taille de bloc sur 4 Mo, ce qui est suffisant pour ce didacticiel.

    ![charger le fichier blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. Cliquez sur le bouton **Charger** en bas du panneau.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Ajout du modèle d’analyse de sentiments de la galerie Cortana Intelligence

Maintenant que les exemples de données sont dans un objet blob, vous pouvez activer le modèle d’analyse des sentiments dans la galerie Cortana Intelligence.

1. Accédez au [modèle d’analyse prédictive des sentiments](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) dans la galerie Cortana Intelligence.  

2. Cliquez sur **Ouvrir dans Studio**.  
   
   ![Stream Analytics Machine Learning, ouvrir Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Connectez-vous pour accéder à l’espace de travail. Sélectionnez un emplacement.

4. Cliquez sur **Exécuter** en bas de la page. Le processus s’exécute. Cela dure environ une minute.

   ![exécuter une expérience dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Après l’exécution du processus, sélectionnez **Déployer un service web** au bas de la page.

   ![déployer l’expérience dans Machine Learning Studio en tant que service web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Pour vérifier que le modèle d’analyse des sentiments est prêt à être utilisé, cliquez sur le bouton **Tester**. Entrez du texte, par exemple « I love Microsoft ». 

   ![tester une expérience dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Si le test fonctionne, un résultat similaire au texte suivant s’affiche :

   ![résultats du test dans Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Dans la colonne **Applications**, cliquez sur le lien **Excel 2010 or earlier workbook** (Classeur Excel 2010 ou antérieur) pour télécharger un classeur Excel. Le classeur contient une clé d’API et l’URL dont vous aurez besoin pour configurer le travail Stream Analytics.

    ![Stream Analytics Machine Learning, aperçu rapide](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Créer une tâche d’analyse Stream Analytics qui utilise le modèle Machine Learning

Vous pouvez maintenant créer un travail Stream Analytics lisant les exemples de tweets à partir du fichier CSV dans le stockage d’objets blob. 

### <a name="create-the-job"></a>Créer le travail

1. Accédez au [portail Azure](https://portal.azure.com).  

2. Cliquez sur **Créer une ressource** > **Internet des objets** > **Travail Steam Analytics**. 

   ![Chemin d’accès au portail Azure pour accéder à un nouveau travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. Nommez le travail `azure-sa-ml-demo`, spécifiez un abonnement, spécifiez un groupe de ressources existant ou créez-en un nouveau et sélectionnez l’emplacement du travail.

   ![spécifier les paramètres du nouveau travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurer les entrées du travail
Le travail reçoit les entrées à partir du fichier CSV chargé précédemment dans le stockage d’objets blob.

1. Une fois le travail créé, accédez à la section **Topologie de la tâche** dans le panneau du travail et sélectionnez la zone **Entrées**.  
   
   ![zone 'Entrées' du panneau du travail dans Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. Dans le panneau **Entrées**, cliquez sur **+ Ajouter**.

   ![bouton ’Ajouter’ pour ajouter une entrée au travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. Indiquez les valeurs suivantes dans le panneau **Nouvelle entrée** :

    * **Alias d’entrée** : utilisez le nom `datainput`.
    * **Type de source** : sélectionnez **Flux de données**.
    * **Source** : sélectionnez **Stockage d’objets blob**.
    * **Option d’importation** : sélectionnez **Utiliser l’objet blob de stockage de l’abonnement actuel**. 
    * **Compte de stockage**. Sélectionnez le compte de stockage que vous avez préalablement créé.
    * **Conteneur**. Sélectionnez le conteneur que vous avez créé précédemment (`azuresamldemoblob`).
    * **Format de sérialisation de l’événement**. Sélectionnez **CSV**.

    ![Paramètres de la nouvelle entrée de travail](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Cliquez sur **Créer**.

### <a name="configure-the-job-output"></a>Configurer la sortie du travail
Le travail envoie les résultats vers le stockage d’objets blob d’où il a reçu les entrées. 

1. Dans la section **Topologie de la tâche** du panneau de travail, sélectionnez la zone **Sorties**.  
  
   ![Créer une sortie pour le travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. Dans le panneau **Sorties**, cliquez sur **+ Ajouter**, puis ajoutez une sortie avec l’alias `datamloutput`. 

3. Au niveau du paramètre **Récepteur**, sélectionnez **Stockage d’objets blob**. Renseignez ensuite le reste des paramètres de sortie en utilisant les valeurs que vous avez utilisées pour les entrées dans le stockage d’objets blob :

    * **Compte de stockage**. Sélectionnez le compte de stockage que vous avez préalablement créé.
    * **Conteneur**. Sélectionnez le conteneur que vous avez créé précédemment (`azuresamldemoblob`).
    * **Format de sérialisation de l’événement**. Sélectionnez **CSV**.

   ![Paramètres de la nouvelle sortie de travail](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Cliquez sur **Créer**.   


### <a name="add-the-machine-learning-function"></a>Ajouter la fonction Machine Learning 
Précédemment, vous avez publié un modèle Machine Learning en tant que service web. Dans notre scénario, lorsque le travail Stream Analysis s’exécute, il envoie chaque exemple de tweet d’entrée au service web pour l’analyse des sentiments. Le service web Machine Learning renvoie un sentiment (`positive`, `neutral` ou `negative`) et associe au tweet une probabilité qu’il soit positif. 

Dans cette section du didacticiel, vous allez définir une fonction dans le travail Stream Analysis. Cette fonction peut être appelée pour envoyer un tweet au service web et renvoyer la réponse. 

1. Assurez-vous que vous disposez de l’URL du service web et de la clé d’API téléchargée précédemment dans le classeur Excel.

2. Revenez au panneau de vue d’ensemble du travail.

3. Sous **Paramètres**, sélectionnez **Fonctions**, puis cliquez sur **+ Ajouter**.

   ![Ajouter une fonction au travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. Entrez `sentiment` comme alias de fonction et remplissez le reste du panneau à l’aide de ces valeurs :

    * **Type de fonction** : sélectionnez **Azure ML**.
    * **Option d’importation** : sélectionnez **Importer à partir d’un autre abonnement**. Cela vous permet d’entrer l’URL et la clé.
    * **URL** : coller l’URL du service web.
    * **Clé** : coller la clé d’API.
  
    ![Paramètres d’ajout d’une fonction Machine Learning au travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. Cliquez sur **Créer**.

### <a name="create-a-query-to-transform-the-data"></a>Créer une requête de transformation des données

Stream Analytics utilise une requête SQL déclarative pour examiner l’entrée et la traiter. Dans cette section, vous allez créer une requête lisant chaque tweet d’entrée, puis appelant la fonction Machine Learning pour effectuer l’analyse des sentiments. La requête envoie ensuite le résultat vers la sortie définie (stockage d’objets blob).

1. Revenez au panneau de vue d’ensemble du travail.

2.  Sous **Topologie de la tâche**, sélectionnez la zone **Requête**.

    ![Créer une requête pour le travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. Entrez la requête suivante :

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    La requête appelle la fonction créée précédemment (`sentiment`), qui effectue l’analyse des sentiments pour chaque tweet d’entrée. 

4. Cliquez sur **Enregistrer** pour enregistrer la feuille de calcul.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Démarrer le travail Stream Analytics et observer le résultat

Vous pouvez maintenant démarrer le travail Stream Analytics.

### <a name="start-the-job"></a>Démarrage du travail
1. Revenez au panneau de vue d’ensemble du travail.

2. Cliquez sur **Démarrer** dans la partie supérieure du panneau.

    ![Créer une requête pour le travail Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. Dans le panneau **Démarrer la tâche**, sélectionnez **Personnalisé**, puis sélectionnez un jour antérieur au moment où le fichier CSV a été chargé sur le stockage d’objets blob. Quand vous avez terminé, cliquez sur **Démarrer**.  


### <a name="check-the-output"></a>Consulter la sortie
1. Laissez quelques minutes au travail pour s’exécuter avant de constater une activité dans la zone **Surveillance**. 

2. Si vous utilisez habituellement un outil spécifique pour consulter le contenu du stockage d’objets blob, utilisez-le pour examiner le conteneur `azuresamldemoblob`. Vous pouvez également effectuer les étapes suivantes dans le portail Azure :

    1. Dans le portail, recherchez le compte de stockage `samldemo`, et dans ce compte, recherchez le conteneur `azuresamldemoblob`. Le conteneur présente deux fichiers : le fichier qui contient les exemples de tweets et un fichier CSV généré par le travail Stream Analytics.
    2. Cliquez sur le fichier généré avec le bouton droit de la souris, puis sélectionnez **Télécharger**. 

   ![Télécharger la sortie CSV du travail à partir du stockage d’objets blob](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Ouvrez le fichier CSV généré. Vous verrez quelque chose de similaire à l’exemple suivant :  
   
   ![Stream Analytics Machine Learning, vue CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Afficher les mesures
Vous pouvez également afficher les mesures liées à la fonction Azure Machine Learning. Les mesures relatives à la fonction ci-dessous s’affichent dans la zone **Surveillance** du panneau du travail :

* **Requêtes de fonction** indique le nombre de requêtes adressées au service web Machine Learning.  
* **Événements de fonction** indique le nombre d’événements dans la requête. Par défaut, chaque requête envoyée à un service web Machine Learning contient jusqu'à 1 000 événements.  


## <a name="next-steps"></a>étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Intégrer l’API REST et le service Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Références sur l'API REST de gestion d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)



