---
title: "Analyse des sentiments Twitter en temps réel avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez comment utiliser Stream Analytics pour l’analyse de sentiments Twitter en temps réel. Aide pas à pas allant de la génération d’événements à la gestion des données sur un tableau de bord en direct."
keywords: "analyse de tendances twitter en temps réel, analyse de sentiments, analyse des réseaux sociaux, exemple d’analyse de tendances"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: samacha
ms.openlocfilehash: 96a169343481f1cdf43af82a7768cfe08cbd4886
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse de sentiments Twitter en temps réel dans Azure Stream Analytics

Apprenez à créer une solution d’analyse de sentiments pour l’analyse des réseaux sociaux en intégrant des événements Twitter dans Azure Event Hubs. Vous pouvez ensuite écrire une requête Azure Stream Analytics pour analyser les données, puis stocker les résultats pour une utilisation ultérieure ou utiliser un tableau de bord et [Power BI](https://powerbi.com/) pour fournir des informations en temps réel.

Les outils d’analyse des réseaux sociaux aident les organisations à comprendre les tendances. Les sujets populaires sont les sujets significatifs et avis apparaissant dans un grand nombre de billets sur les réseaux sociaux. L’analyse de sentiments, aussi appelée *exploration d’opinions*, utilise des outils d’analyse de réseaux sociaux pour déterminer les attitudes envers un produit, une idée, etc. 

L’analyse de tendances Twitter en temps réel constitue un excellent exemple d’outil d’analyse, car le modèle d’abonnement mot-dièse vous permet de suivre des mots-clés spécifiques (mots-dièse) et de développer l’analyse des sentiments sur le flux.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénario : analyse de sentiments sur les réseaux sociaux en temps réel

Une entreprise qui dispose d’un site web de médias souhaite obtenir un avantage sur ses concurrents en présentant des contenus immédiatement pertinents pour ses lecteurs. Elle utilise l’analyse des réseaux sociaux sur des sujets pertinents pour ses lecteurs en effectuant une analyse de sentiments en temps réel des données de Twitter.

Pour identifier les tendances en temps réel sur Twitter, l’entreprise doit analyser en temps réel le volume et les sentiments des tweets relatifs aux principaux sujets. En d’autres termes, nous avons besoin d’un moteur d’analyse de sentiments basé sur le flux de ce réseau social.

## <a name="prerequisites"></a>Prérequis
Dans ce didacticiel, vous utilisez une application cliente qui se connecte à Twitter, puis recherchez des tweets contenant certains mots-dièse (que vous pouvez définir). Pour exécuter l’application et analyser les tweets à l’aide d’Azure Stream Analytics, vous devez disposer des éléments suivants :

* Abonnement Azure
* un compte Twitter ; 
* Une application Twitter et le [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) pour cette application. Plus loin, nous indiquons des instructions générales relatives à la création d’une application Twitter.
* L’application TwitterWPFClient, qui lit le flux Twitter. Pour obtenir cette application, téléchargez le fichier [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) à partir de GitHub, puis décompressez le package dans un dossier sur votre ordinateur. Pour afficher le code source et exécuter l’application dans un débogueur, vous pouvez obtenir le code source de l’application dans [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Créer un concentrateur Event Hub pour l’entrée Stream Analytics

L’exemple d’application génère des événements et les transmet à un concentrateur Azure Event Hub. Les concentrateurs Azure Event Hub constituent la méthode favorite pour la réception des événements dans Stream Analytics. Pour plus d’informations, voir la [Documentation relative aux concentrateurs Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Créer un espace de noms Event Hub et un concentrateur Event Hub
Dans cette procédure, vous allez commencer par créer un espace de noms Event Hub, puis ajouter un concentrateur Event Hub à cet espace de noms. Les espaces de noms Event Hub sont utilisés pour regrouper logiquement des instances Event Hub associées. 

1. Connectez-vous au portail Azure, puis cliquez sur **Créer une ressource** > **Internet des objets** > **Event Hub**. 

2. Dans le panneau **Créer un espace de noms**, entrez un nom d’espace de noms, par exemple `<yourname>-socialtwitter-eh-ns`. Vous pouvez utiliser n’importe quel nom pour l’espace de noms, mais il doit être valide pour une URL et unique dans Azure. 
    
3. Sélectionnez un abonnement, créez ou choisissez un groupe de ressources, puis cliquez sur **Créer**. 

    ![Créer un espace de noms Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Lorsque le déploiement de l’espace de noms est terminé, recherchez l’espace de noms Event Hub dans la liste de ressources Azure. 

5. Cliquez sur le nouvel espace de noms, puis, dans le panneau d’espace de noms, cliquez sur **+&nbsp;Event Hub**. 

    ![Bouton Ajouter un hub d’événements permettant de créer un concentrateur Event Hub ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nommez le nouveau concentrateur Event Hub `socialtwitter-eh`. Vous pouvez utiliser un autre nom. Le cas échéant, prenez-en note, car vous devrez l’utiliser ultérieurement. Vous n’avez pas besoin de définir d’autres options pour le concentrateur Event Hub.

    ![Panneau de création d’un concentrateur Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Cliquez sur **Créer**.


### <a name="grant-access-to-the-event-hub"></a>Accorder un accès au concentrateur Event Hub

Pour qu’un processus puisse envoyer des données à un concentrateur Event Hub, ce concentrateur doit disposer d’une stratégie autorisant un accès approprié. La stratégie d’accès génère une chaîne de connexion qui inclut des informations d’autorisation.

1.  Dans le panneau d’espace de noms, cliquez sur **Event Hubs**, puis sur le nom de votre nouveau concentrateur Event Hub.

2.  Dans le panneau Event Hub, cliquez sur **Stratégies d’accès partagé**, puis sur **+&nbsp;Ajouter**.

    >[!NOTE]
    >Veillez à utiliser le concentrateur Event Hub et pas l’espace de noms Event Hub.

3.  Ajoutez la stratégie nommée `socialtwitter-access` et, pour **Revendication**, sélectionnez **Gérer**.

    ![Panneau de création d’une stratégie d’accès au concentrateur Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Cliquez sur **Créer**.

5.  Une fois la stratégie déployée, cliquez dessus dans la liste des stratégies d’accès partagé.

6.  Recherchez la zone intitulée **CHAÎNE DE CONNEXION-CLÉ PRIMAIRE**, puis cliquez sur le bouton de copie situé à côté de la chaine de connexion. 
    
    ![Copie de la clé primaire de la chaîne de connexion à partir de la stratégie d’accès](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Collez la chaîne de connexion dans un éditeur de texte. Vous aurez besoin de cette chaîne de connexion pour la section suivante, une fois que vous lui aurez apporté quelques légères modifications.

    La chaîne de connexion ressemble à ce qui suit :

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Notez que la chaîne de connexion contient plusieurs paires clé-valeur, séparées par des points-virgules : `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` et `EntityPath`.  

    > [!NOTE]
    > Pour des raisons de sécurité, des parties de la chaîne de connexion indiquée dans l’exemple ont été supprimées.

8.  Dans l’éditeur de texte, supprimez la paire `EntityPath` de la chaîne de connexion (n’oubliez pas de supprimer le point-virgule qui la précède). À l’issue de cette opération, la chaîne de connexion ressemble à ce qui suit :

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application client Twitter
L’application cliente obtient les événements de tweet directement à partir de Twitter. Pour ce faire, elle a besoin de l’autorisation d’appeler les API de diffusion Twitter. Pour configurer cette autorisation, créez une application dans Twitter, qui génère des informations d’identification uniques (par exemple, un jeton OAuth). Vous pouvez ensuite configurer l’application cliente pour qu’elle utilise ces informations d’identification lorsqu’elle émet des appels d’API. 

### <a name="create-a-twitter-application"></a>Création d'une application Twitter
Si vous ne possédez pas encore une application Twitter que vous pouvez utiliser pour ce didacticiel, vous pouvez en créer une. Vous devez déjà posséder un compte Twitter.

> [!NOTE]
> Dans Twitter, il est possible que le processus exact pour créer une application et obtenir les clés, secrets ainsi que le jeton soit différent. Si les instructions qui suivent ne correspondent pas ce que vous voyez sur le site Twitter, consultez la documentation pour développeurs Twitter.

1. Accédez à la [page de gestion des applications Twitter](https://apps.twitter.com/). 

2. Créez une application. 

    * Spécifiez une URL valide pour le site web. Il ne doit pas s’agir d’un site actif. (Vous ne pouvez pas spécifier simplement `localhost`.)
    * Laissez le champ de rappel vide. L’application cliente que vous utilisez pour ce didacticiel ne nécessite pas de rappels.

    ![Création d’une application dans Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Modifiez éventuellement les autorisations de l’application pour les définir en lecture seule.

4. Lorsque l’application est créée, accédez à la page **Clés et jetons d’accès**.

5. Cliquez sur le bouton pour générer un jeton d’accès et le secret correspondant.

Conservez ces informations à portée de main, car vous en aurez besoin dans la procédure suivante.

>[!NOTE]
>Les clés et secrets de l’application Twitter permettent d’accéder à votre compte Twitter. Considérez que ces informations sont sensibles, tout comme votre mot de passe Twitter. Par exemple, ne les intégrez pas à une application que vous proposerez à d’autres utilisateurs. 


### <a name="configure-the-client-application"></a>Configurer l’application cliente
Nous avons créé une application cliente qui se connecte aux données de Twitter par le biais des [API de diffusion Twitter](https://dev.twitter.com/streaming/overview) pour collecter des événements de tweet sur un ensemble de sujets spécifique. L’application utilise l’outil open source [Sentiment140](http://help.sentiment140.com/) qui affecte la valeur de sentiment suivante à chaque tweet :

* 0 = négatif
* 2 = neutre
* 4 = positif

Une fois qu’une valeur de sentiment a été attribuée aux événements de tweet, ceux-ci sont transmis au concentrateur Event Hub que vous avez créé précédemment.

Pour que l’application s’exécute, vous devez indiquer certaines informations telles que les clés Twitter et la chaîne de connexion du concentrateur Event Hub. Vous pouvez fournir les informations de configuration des façons suivantes :

* Exécutez l’application, puis utilisez son interface utilisateur pour entrer les clés, les secrets et la chaîne de connexion. Si vous procédez ainsi, les informations de configuration sont utilisées pour votre session active, mais elles ne sont pas enregistrées.
* Modifiez le fichier .config de l’application et définissez-y les valeurs. Cette approche permet de conserver les informations de configuration, mais implique également que ces informations potentiellement sensibles sont stockées en texte brut sur votre ordinateur.

La procédure suivante décrit les deux approches. 

1. Veillez à avoir téléchargé et décompressé l’application [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), comme indiqué dans les conditions préalables.

2. Pour définir les valeurs à l’exécution (et uniquement pour la session active), exécutez l’application `TwitterWPFClient.exe`. Lorsque l’application vous y invite, entrez les valeurs suivantes :

    * Clé du client Twitter (clé API).
    * Secret du client Twitter (secret API).
    * Jeton d’accès Twitter.
    * Secret du jeton d’accès Twitter.
    * Informations de la chaîne de connexion que vous avez enregistrées précédemment. Assurez-vous d’utiliser la chaîne de connexion dans laquelle vous avez supprimé la paire clé-valeur `EntityPath`.
    * Mots-clés Twitter pour lesquels vous souhaitez déterminer les sentiments.

   ![Application TwitterWpfClient en cours d’exécution, affichant des paramètres masqués](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Pour définir les valeurs de manière permanente, ouvrez le fichier TwitterWpfClient.exe.config dans un éditeur de texte. Ensuite, dans l’élément `<appSettings>`, effectuez les actions suivantes :

    * Définissez `oauth_consumer_key` sur la clé du client Twitter (clé API). 
    * Définissez `oauth_consumer_secret` sur le secret du client Twitter (secret API).
    * Définissez `oauth_token` sur le jeton d’accès Twitter.
    * Définissez `oauth_token_secret` sur le secret du jeton d’accès Twitter.

    Plus loin dans l’élément `<appSettings>`, apportez ces modifications :

    * Définissez `EventHubName` sur le nom du concentrateur Event Hub (autrement dit, la valeur du chemin d’accès de l’entité).
    * Définissez `EventHubNameConnectionString` sur la chaîne de connexion. Assurez-vous d’utiliser la chaîne de connexion dans laquelle vous avez supprimé la paire clé-valeur `EntityPath`.

    La section `<appSettings>` ressemble à l’exemple qui suit. (Pour des raisons de clarté et de sécurité, nous avons inséré des retours automatiques de ligne et supprimé des caractères.)

    ![Fichier de configuration de l’application TwitterWpfClient dans un éditeur de texte affichant les clés et secrets Twitter, ainsi que les informations de la chaîne de connexion du concentrateur Event Hub](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Si vous n’avez pas encore démarré l’application, exécutez TwitterWpfClient.exe maintenant. 

5. Cliquez sur le bouton de démarrage vert pour recueillir les sentiments sur les réseaux sociaux. Vous voyez s’afficher les événements Tweet tandis que les valeurs **CreatedAt**, **Topic** et **SentimentScore** sont transmises à votre hub d’événements.

    ![Application TwitterWpfClient en cours d’exécution, affichant une liste de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Si vous constatez des erreurs et ne voyez pas de flux de tweets dans la partie inférieure de la fenêtre, vérifiez les clés et les secrets. Vérifiez également la chaîne de connexion (assurez-vous qu’elle n’inclut pas la clé `EntityPath` ni la valeur.)


## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics

Maintenant que nous avons un flux d’événements de tweet diffusé en temps réel depuis Twitter, vous pouvez configurer un travail Stream Analytics pour analyser ces événements en temps réel.

1. Dans le portail Azure, cliquez sur **Créer une ressource** > **Internet des objets** > **Travail Stream Analytics**.

2. Nommez le travail `socialtwitter-sa-job`, puis spécifiez un abonnement, un groupe de ressources et un emplacement.

    Il est judicieux de placer le travail et le concentrateur Event Hub dans la même région afin d’optimiser les performances. Ce faisant, vous ne payez pas pour transférer des données entre les régions.

    ![Création d’un travail Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Cliquez sur **Créer**.

    Le travail est créé, et le portail en affiche les détails.


## <a name="specify-the-job-input"></a>Spécification de l’entrée de travail

1. Dans votre travail Stream Analytics, dans **Topologie du travail** au milieu du panneau du travail, cliquez sur **Entrées**. 

2. Dans le panneau **Entrées**, cliquez sur **+&nbsp;Ajouter**, puis renseignez le panneau avec les valeurs suivantes :

    * **Alias d’entrée** : utilisez le nom `TwitterStream`. Si vous utilisez un autre nom, prenez-en note, car vous en aurez besoin ultérieurement.
    * **Type de source** : sélectionnez **Flux de données**.
    * **Source** : sélectionnez **Event Hub**.
    * **Option d’importation** : sélectionnez **Utiliser le hub d’événements de l’abonnement actuel**. 
    * **Espace de noms Service Bus** : sélectionnez l’espace de noms Event Hub que vous avez créé précédemment (`<yourname>-socialtwitter-eh-ns`).
    * **Event Hub** : sélectionnez le concentrateur Event Hub que vous avez créé précédemment (`socialtwitter-eh`).
    * **Nom de la stratégie du hub d’événements** : sélectionnez la stratégie d’accès que vous avez créée précédemment (`socialtwitter-access`).

    ![Créer une entrée pour le travail Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Cliquez sur **Créer**.


## <a name="specify-the-job-query"></a>Spécification de la requête de travail

Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel aborde la création et le test de plusieurs requêtes sur des données Twitter.

Pour comparer le nombre de mentions entre les sujets, vous pouvez utiliser une [fenêtre bascule](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par sujet toutes les cinq secondes.

1. Si ce n’est déjà fait, fermez le panneau **Entrées**.

2. Dans le panneau du travail, cliquez sur la zone **Requête**. Azure répertorie les entrées et sorties qui sont configurées pour le travail. Vous pouvez également utiliser Azure pour créer une requête qui vous permettra de transformer le flux d’entrée lorsqu’il est envoyé vers la sortie.

3. Assurez-vous que l’application TwitterWpfClient est en cours d’exécution. 

3. Dans le panneau **Requête**, cliquez sur les points situés à côté de l’entrée `TwitterStream`, puis sélectionnez **Exemple de données de l’entrée**.

    ![Options du menu permettant d’utiliser des exemples de données pour l’entrée du travail Stream Analytics avec l’option Exemple de données de l’entrée sélectionnée](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Cette opération ouvre un panneau qui vous permet de spécifier la quantité d’exemples de données à obtenir, définie en termes de durée de lecture du flux d’entrée.

4. Définissez l’option **Minutes** sur 3, puis cliquez sur **OK**. 
    
    ![Options d’échantillonnage du flux d’entrée, avec la valeur « 3 minutes » sélectionnée.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    À partir du flux d’entrée, Azure échantillonne les données pour une valeur correspondant à une durée de 3 minutes, et vous informe lorsque l’échantillon est prêt. (Cette opération prend quelques instants.) 

    L’exemple de données est stocké temporairement et disponible tant que la fenêtre de requête est ouverte. Si vous la fermez, l’exemple de données est abandonné, et vous devez en créer un autre. 

5. Dans l’éditeur de code, modifiez la requête comme suit :

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Si n’avez pas utilisé `TwitterStream` comme alias de l’entrée, remplacez votre alias par `TwitterStream` dans la requête.  

    Cette requête utilise le mot clé **TIMESTAMP BY** pour spécifier un champ d’horodatage dans la charge utile à utiliser dans le calcul temporel. Si ce champ n’est pas spécifié, l’opération de fenêtrage est réalisée en utilisant l’heure d’arrivée de chaque événement dans le hub d’événements. Pour en savoir plus, consultez la section « Heure d’arrivée par rapport à l’heure de l’application » de la page [Référence du langage de requête d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Cette requête permet également d’accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp**.

5. Cliquez sur **Test**. La requête s’exécute sur les données que vous avez échantillonnées.
    
6. Cliquez sur **Enregistrer**. Cette opération enregistre la requête dans le cadre du travail Stream Analytics. (Elle n’enregistre pas l’exemple de données.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Utilisation de différents champs du flux 

Le tableau suivant répertorie les champs qui font partie des données de diffusion en continu au format JSON. N’hésitez pas à faire des essais dans l’éditeur de requête.

|Propriété JSON | Définition|
|--- | ---|
|CreatedAt | L’heure à laquelle le tweet a été créé|
|Rubrique | La rubrique qui correspond au mot-clé spécifié|
|SentimentScore | Le score des sentiments à partir de Sentiment140|
|Auteur | Le nom d’utilisateur Twitter qui a envoyé le tweet|
|Texte | Le corps complet du tweet|


## <a name="create-an-output-sink"></a>Créer un récepteur de sortie

Vous avez défini un flux d’événements, une entrée de concentrateur Event Hub pour ingérer des événements, et une requête pour effectuer une transformation sur le flux. La dernière étape consiste à définir un récepteur de sortie pour le travail.  

Dans ce didacticiel, vous écrivez les événements de tweet agrégés de la requête de travail dans un stockage Blob Azure.  Selon les besoins de votre application, vous pouvez également transmettre vos résultats à une base de données Azure SQL Database, un stockage Table Azure, Event Hubs ou Power BI.

## <a name="specify-the-job-output"></a>Spécification de la sortie du travail

1. Dans la section **Topologie de la tâche**, cliquez sur la zone **Sortie**. 

2. Dans le panneau **Sorties**, cliquez sur **+&nbsp;Ajouter**, puis renseignez le panneau avec les valeurs suivantes :

    * **Alias de sortie** : utilisez le nom `TwitterStream-Output`. 
    * **Sink** : sélectionnez **Stockage d’objets blob**.
    * **Options d’importation** : sélectionnez **Utiliser l’objet blob de stockage de l’abonnement actuel**.
    * **Compte de stockage**. sélectionnez **Créer un compte de stockage**.
    * **Compte de stockage** (seconde zone) : entrez `YOURNAMEsa`, où `YOURNAME` représente votre nom ou une autre chaîne unique. Le nom, qui doit être unique dans Azure, ne peut contenir que des lettres minuscules et des chiffres. 
    * **Conteneur** : Entrez `socialtwitter`.
    Le nom du compte de stockage et le nom du conteneur sont utilisés ensemble pour fournir un URI pour le stockage d’objets blob, comme suit : 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Panneau Nouvelle sortie pour le travail Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Cliquez sur **Créer**. 

    Azure crée le compte de stockage et génère une clé automatiquement. 

5. Fermez le panneau **Sorties**. 


## <a name="start-the-job"></a>Démarrage du travail

Une entrée de travail, une requête et une sortie sont spécifiées. Vous êtes prêt à démarrer le travail Stream Analytics.

1. Assurez-vous que l’application TwitterWpfClient est en cours d’exécution. 

2. Dans le panneau du travail, cliquez sur **Démarrer**.

    ![Démarrage de la tâche Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. Dans le panneau **Démarrer le travail**, sélectionnez **Maintenant** pour l’option **Heure de début de la sortie de la tâche**, puis cliquez sur **Démarrer**. 

    ![Panneau Démarrer le travail pour le travail Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure vous avertit lorsque le travail a démarré. Dans le panneau du travail, l’état **En cours d’exécution** s’affiche.

    ![Travail en cours d’exécution](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Afficher la sortie de l’analyse de sentiments

Une fois que l’exécution du travail a commencé et qu’il traite le flux Twitter en temps réel, vous pouvez afficher la sortie pour l’analyse des sentiments.

Pour afficher la sortie du travail en temps réel, vous pouvez utiliser un outil tel que [Azure Storage Explorer](https://http://storageexplorer.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). À ce stade, vous pouvez utiliser [Power BI](https://powerbi.com/) pour étendre les fonctionnalités de votre application afin d’ajouter un tableau de bord personnalisé comme celui présenté dans la capture d’écran suivante :

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Créer une autre requête pour identifier les tendances

Une autre requête que vous pouvez utiliser pour comprendre les sentiments Twitter est basée sur une [fenêtre glissante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Pour identifier les tendances, vous recherchez des sujets dépassant une valeur de seuil pour les mentions dans un laps de temps spécifié.

Pour les besoins de ce didacticiel, vous allez consulter les rubriques mentionnées plus de 20 fois au cours des 5 dernières secondes.

1. Dans le panneau du travail, cliquez sur **Arrêter** pour arrêter le travail. 

2. Dans la section **Topologie de la tâche**, cliquez sur la zone **Requête**. 

3. Modifiez la requête comme suit :

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Cliquez sur **Enregistrer**.

5. Assurez-vous que l’application TwitterWpfClient est en cours d’exécution. 

6. Cliquez sur **Démarrer** pour redémarrer le travail à l’aide de la nouvelle requête.


## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
