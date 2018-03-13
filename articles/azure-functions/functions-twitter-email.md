---
title: "Créer une fonction qui s’intègre avec Azure Logic Apps | Documents Microsoft"
description: "Créez une fonction qui s’intègre à Azure Logic Apps et à Azure Cognitive Services pour catégoriser les sentiments de tweets et envoyer des notifications quand le sentiment est peu favorable."
services: functions, logic-apps, cognitive-services
keywords: "flux de travail, applications cloud, services cloud, processus d’entreprise, intégration de systèmes, intégration d’applications d’entreprise, IAE"
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 9e9369d9dc9f7298b93927b49685f4e24de8a7fd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Créer une fonction qui s’intègre avec Azure Logic Apps

Azure Functions s’intègre avec Azure Logic Apps dans le Concepteur d’applications logiques. Cette intégration vous permet d’utiliser la puissance des fonctions dans les orchestrations avec d’autres services Azure et services tiers. 

Ce didacticiel vous montre comment utiliser des fonctions avec Logic Apps et Microsoft Cognitive Services sur Azure pour analyser les sentiments de billets Twitter. Une fonction déclenchée via HTTP classe les tweets en vert, jaune ou rouge selon le score de sentiments. Un courrier électronique est envoyé lors de la détection de sentiments négatifs. 

![Image : deux premières étapes de l’application dans le Concepteur d’applications logiques](media/functions-twitter-email/designer1.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez une ressource API Cognitive Services.
> * Créer une fonction qui classe le sentiment des tweets.
> * Créer une application logique qui se connecte à Twitter.
> * Ajouter la détection des sentiments à l’application logique. 
> * Connecter l’application logique à la fonction.
> * Envoyer un courrier électronique en fonction de la réponse de la fonction.

## <a name="prerequisites"></a>Prérequis

+ Un compte [Twitter](https://twitter.com/) actif. 
+ Un compte [Outlook.com](https://outlook.com/) (pour l’envoi de notifications).
+ Cette rubrique utilise comme point de départ les ressources créées dans [Créer votre première fonction à partir du portail Azure](functions-create-first-azure-function.md).  
Si vous ne l’avez pas déjà fait, suivez ces étapes pour créer votre Function App.

## <a name="create-a-cognitive-services-resource"></a>Créer une ressource Cognitive Services

Les API Cognitive Services sont disponibles dans Azure en tant que ressources individuelles. Utilisez l’API Analyse de texte pour détecter le sentiment des tweets en cours d’analyse.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

3. Cliquez sur **AI + Analytics** > **API Analyse de texte**. Puis, utilisez les paramètres comme indiqué dans le tableau, acceptez les termes du contrat et cochez **Épingler au tableau de bord**.

    ![Créer une page de ressource Cognitive](media/functions-twitter-email/cog_svcs_resource.png)

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | --- |
    | **Name** | MyCognitiveServicesAccnt | Choisissez un nom de compte unique. |
    | **Lieu** | États-Unis de l’Ouest | Utilisez l’emplacement le plus proche de vous. |
    | **Niveau tarifaire** | F0 | Démarrez avec le niveau le plus bas. Si vous manquez d’appels, choisissez un niveau plus élevé.|
    | **Groupe de ressources** | myResourceGroup | Utilisez le même groupe de ressources pour tous les services de ce didacticiel.|

4. Cliquez sur **Créer** pour créer votre ressource. Une fois la ressource créée, cliquez sur votre nouvelle ressource Cognitive Services épinglé au tableau de bord. 

5. Dans la colonne de navigation gauche, cliquez sur **Clés**, puis copiez la valeur de **Clé 1** et enregistrez-la. Cette clé vous permet de connecter l’application logique à votre API Cognitive Services. 
 
    ![Clés](media/functions-twitter-email/keys.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-the-function-app"></a>Créer l’application de fonction

Les fonctions offrent un excellent moyen de se décharger des tâches de traitement dans un flux de travail d’applications logiques. Ce didacticiel utilise une fonction déclenchée via HTTP pour traiter des scores de sentiments de tweet à partir de Cognitive Services et renvoie une valeur de catégorie.  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>Créer une fonction déclenchée via HTTP  

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](media/functions-twitter-email/add-first-function.png)

2. Dans le champ Rechercher, tapez `http`, puis choisissez **C#** pour le modèle de déclencheur HTTP. 

    ![Choisir le déclencheur HTTP](./media/functions-twitter-email/select-http-trigger-portal.png)

3. Tapez un **nom** à donner à votre fonction, choisissez `Function` pour le **[niveau d’authentification](functions-bindings-http-webhook.md#http-auth)**, puis sélectionnez **Créer**. 

    ![Créer une fonction déclenchée via HTTP](./media/functions-twitter-email/select-http-trigger-portal-2.png)

    Cette opération crée une fonction de script C# à l’aide du modèle HTTP déclencheur. Votre code s’affiche dans une nouvelle fenêtre sous la forme `run.csx`.

4. Remplacez le contenu du fichier `run.csx` par le code suivant, puis cliquez sur **Enregistrer** :

    ```csharp
    using System.Net;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // The sentiment category defaults to 'GREEN'. 
        string category = "GREEN";
    
        // Get the sentiment score from the request body.
        double score = await req.Content.ReadAsAsync<double>();
        log.Info(string.Format("The sentiment score received is '{0}'.",
                    score.ToString()));
    
        // Set the category based on the sentiment score.
        if (score < .3)
        {
            category = "RED";
        }
        else if (score < .6)
        {
            category = "YELLOW";
        }
        return req.CreateResponse(HttpStatusCode.OK, category);
    }
    ```
    Le code de cette fonction renvoie une catégorie de couleur en fonction du score des sentiments reçu dans la requête. 

4. Pour tester la fonction, cliquez sur **Test** tout à droite pour développer l’onglet de test. Tapez la valeur `0.2` pour le **corps de la requête**, puis cliquez sur **Exécuter**. La valeur **RED** est renvoyée dans le corps de la réponse. 

    ![Testez la fonction dans le portail Azure](./media/functions-twitter-email/test.png)

Vous disposez maintenant d’une fonction permettant de classer les scores des sentiments. Ensuite, créez une application logique qui intègre votre fonction dans vos API Twitter et Cognitive Services. 

## <a name="create-a-logic-app"></a>Créer une application logique   

1. Dans le portail Azure, cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail.

2. Cliquez sur **Intégration d’entreprise** > **Application logique**. Puis, utilisez les paramètres comme indiqué dans le tableau, cochez **Épingler au tableau de bord** et cliquez sur **Créer**.
 
4. Puis, saisissez un **Nom** comme `TweetSentiment`, utilisez les paramètres comme indiqué dans le tableau, acceptez les termes du contrat et cochez **Épingler au tableau de bord**.

    ![Créer une application logique dans le portail Azure](./media/functions-twitter-email/new_logic_app.png)

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | ----------------- | ------------ | ------------- |
    | **Name** | TweetSentiment | Choisissez un nom approprié pour votre application. |
    | **Groupe de ressources** | myResourceGroup | Choisissez le même groupe de ressources existant que précédemment. |
    | **Lieu** | Est des États-Unis | Choisissez un emplacement proche de vous. |    

4. Choisissez **Épingler au tableau de bord** puis cliquez sur **Créer** pour créer votre application logique. 

5. Une fois que l’application est créée, cliquez sur votre nouvelle application logique épinglée au tableau de bord. Dans le Concepteur d’applications logiques, faites défiler vers le bas, puis cliquez sur le modèle **Application logique vide**. 

    ![Modèle d’applications logiques vides](media/functions-twitter-email/blank.png)

Vous pouvez maintenant utiliser le Concepteur d’applications logiques pour ajouter des services et des déclencheurs à votre application.

## <a name="connect-to-twitter"></a>Se connecter à Twitter

Tout d’abord, créez une connexion à votre compte Twitter. L’application logique interroge les tweets qui déclenchent l’exécution de l’application.

1. Dans le concepteur, cliquez sur le service **Twitter**, puis cliquez sur le déclencheur **Lorsqu’un nouveau tweet est publié**. Connectez-vous à votre compte Twitter et autorisez Logic Apps à utiliser votre compte.

2. Utilisez les paramètres de déclencheur Twitter spécifiés dans le tableau. 

    ![Paramètres du connecteur Twitter](media/functions-twitter-email/azure_tweet.png)

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | ----------------- | ------------ | ------------- |
    | **Texte de recherche** | #Azure | Utilisez un mot-dièse suffisamment populaire pour générer de nouveaux tweets dans l’intervalle sélectionné. Lors de l’utilisation du niveau Gratuit, si votre mot-dièse est trop populaire, vous pouvez rapidement épuiser le quota de transactions de votre API Cognitive Services. |
    | **Fréquence** | Minute | L’unité de fréquence utilisée pour l’interrogation de Twitter.  |
    | **Intervalle** | 15 | Le temps écoulé entre les requêtes de Twitter, en unités de fréquence. |

3.  Cliquez sur **Enregistrer** pour vous connecter à votre compte Twitter. 

Votre application est maintenant connectée à Twitter. Ensuite, connectez-vous à l’analyse de texte pour détecter les sentiments des tweets collectés.

## <a name="add-sentiment-detection"></a>Ajouter la détection de sentiments

1. Cliquez sur **Nouvelle étape**, puis sélectionnez **Ajouter une action**.

    ![Nouvelle étape, puis Ajouter une action](media/functions-twitter-email/new_step.png)

2. Dans **Choisir une action**, cliquez sur **Analyse de texte**, puis cliquez sur l’action **Détecter le sentiment**.

    ![Detect Sentiment (Détecter le sentiment)](media/functions-twitter-email/detect_sent.png)

3. Tapez un nom de connexion comme `MyCognitiveServicesConnection`, collez la clé de votre API Cognitive Services enregistrée précédemment, puis cliquez sur **Créer**.  

4. Cliquez sur **Texte à analyser** > **Texte du tweet**, puis cliquez sur **Enregistrer**.  

    ![Detect Sentiment (Détecter le sentiment)](media/functions-twitter-email/ds_tta.png)

Maintenant que la détection de sentiment est configurée, vous pouvez ajouter une connexion à votre fonction qui utilise la sortie du score de sentiments.

## <a name="connect-sentiment-output-to-your-function"></a>Connecter la sortie des sentiments à votre fonction

1. Dans le Concepteur d’applications logiques, cliquez sur **Nouvelle étape** > **Ajouter une action**, puis cliquez sur **Azure Functions**. 

2. Cliquez sur **Choisir une fonction Azure**, sélectionnez la fonction **CategorizeSentiment** que vous avez créée précédemment.  

    ![Zone Azure Functions montrant Choisir une fonction Azure](media/functions-twitter-email/choose_fun.png)

3. Dans **Corps de la requête**, cliquez sur **Score**, puis sur **Enregistrer**.

    ![Score](media/functions-twitter-email/trigger_score.png)

À présent, votre fonction est déclenchée lorsqu’un score de sentiment est envoyé à partir de l’application logique. La fonction renvoie une catégorie de couleur à l’application logique. Ensuite, ajoutez une notification par courrier électronique qui est envoyée lorsqu’une valeur de sentiment **RED** est renvoyée par la fonction. 

## <a name="add-email-notifications"></a>Ajouter des notifications par courrier électronique

La dernière partie du flux de travail consiste à déclencher un courrier électronique lorsque le sentiment est évalué comme _RED_. Cette rubrique utilise un connecteur Outlook.com. Vous pouvez effectuer des étapes similaires pour utiliser un connecteur Gmail ou Office 365 Outlook.   

1. Dans le Concepteur d’applications logiques, cliquez sur **Nouvelle étape** > **Ajouter une condition**. 

2. Cliquez sur **Choisir une valeur**, puis cliquez sur **Corps**. Sélectionnez **Est égal à**, cliquez sur **Choisir une valeur** et saisissez `RED`, puis cliquez sur **Enregistrer**. 

    ![Ajouter une condition à l’application logique.](media/functions-twitter-email/condition.png)

3. Dans **SI OUI**, cliquez sur **Ajouter une action**, recherchez `outlook.com`, cliquez sur **Envoyer un e-mail**, puis connectez-vous à votre compte Outlook.com.
    
    ![Choisir une action pour la condition.](media/functions-twitter-email/outlook.png)

    > [!NOTE]
    > Si vous n’avez pas de compte Outlook.com, vous pouvez choisir un autre connecteur, comme Gmail ou Office 365 Outlook

4. Dans l’action **Envoyer un courrier électronique**, utilisez les paramètres de messagerie indiqués dans le tableau. 

    ![Configurez le courrier électronique pour l’action Envoyer un courrier électronique.](media/functions-twitter-email/send_email.png)

    | Paramètre      |  Valeur suggérée   | Description  |
    | ----------------- | ------------ | ------------- |
    | **To** | Saisissez votre adresse de messagerie | L’adresse de messagerie qui reçoit la notification. |
    | **Objet** | Sentiment de tweet négatif détecté  | La ligne d’objet de la notification par courrier électronique.  |
    | **Corps** | Texte du tweet, Emplacement | Cliquez sur les paramètres **Texte du tweet** et **Emplacement**. |

5.  Cliquez sur **Enregistrer**.

Maintenant que le flux de travail est terminé, vous pouvez activer l’application logique et observer la fonction en action.

## <a name="test-the-workflow"></a>Tester le flux de travail

1. Dans le Concepteur d’application logique, cliquez sur **Exécuter** pour démarrer l’application.

2. Dans la colonne de gauche, cliquez sur **Vue d’ensemble** pour connaître l’état de l’application logique. 
 
    ![État d’exécution de l’application logique](media/functions-twitter-email/over1.png)

3. (Facultatif) Cliquez sur une des exécutions pour afficher les détails de l’exécution.

4. Accédez à votre fonction, affichez les journaux et vérifiez que les valeurs des sentiments ont été reçues et traitées.
 
    ![Affichez les journaux de fonction](media/functions-twitter-email/sent.png)

5. Lorsqu’un sentiment potentiellement négatif est détecté, vous recevez un courrier électronique. Si vous n’avez pas reçu de courrier électronique, vous pouvez modifier le code de fonction pour renvoyer RED à chaque fois :

        return req.CreateResponse(HttpStatusCode.OK, "RED");

    Après avoir vérifié les notifications par courrier électronique, rétablissez le code d’origine :

        return req.CreateResponse(HttpStatusCode.OK, category);

    > [!IMPORTANT]
    > Une fois que vous avez terminé ce didacticiel, désactivez l’application logique. En désactivant l’application, vous évitez d’être facturé pour les exécutions et d’épuiser les transactions dans votre API Cognitive Services.

Maintenant, vous savez à quel point il est facile d’intégrer des fonctions dans un flux de travail Logic Apps.

## <a name="disable-the-logic-app"></a>Désactiver l’application logique

Pour désactiver l’application logique, cliquez sur **Vue d’ensemble**, puis cliquez sur **Désactiver** en haut de l’écran. Cela arrête l’application logique et évite d’entraîner des frais, sans supprimer l’application. 

![Journaux de fonction](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créez une ressource API Cognitive Services.
> * Créer une fonction qui classe le sentiment des tweets.
> * Créer une application logique qui se connecte à Twitter.
> * Ajouter la détection des sentiments à l’application logique. 
> * Connecter l’application logique à la fonction.
> * Envoyer un courrier électronique en fonction de la réponse de la fonction.

Passez au didacticiel suivant pour apprendre à créer une API sans serveur pour votre fonction.

> [!div class="nextstepaction"] 
> [Créer une API sans serveur à l’aide d’Azure Functions](functions-create-serverless-api.md)

Pour plus d’informations sur Logic Apps, voir [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

