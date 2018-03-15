---
title: "Créer une fonction dans Azure déclenchée par un webhook générique | Microsoft Docs"
description: "Utilisez Azure Functions pour créer une fonction sans serveur qui est appelée par un webhook dans Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: fafc10c0-84da-4404-b4fa-eea03c7bf2b1
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/08/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: cf86325971b607c7e1ec4b026f7df274e14c5e52
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="create-a-function-triggered-by-a-generic-webhook"></a>Créer une fonction déclenchée par un webhook générique

Azure Functions vous permet d’exécuter votre code dans un environnement sans serveur et sans avoir à créer une machine virtuelle ou à publier une application web au préalable. Par exemple, vous pouvez configurer qu’une fonction soit déclenchée par une alerte provenant d’Azure Monitor. Cette rubrique montre comment exécuter le code C# lorsqu’un groupe de ressources est ajouté à votre abonnement.   

![Fonction déclenchée par un webhook générique dans le portail Azure](./media/functions-create-generic-webhook-triggered-function/function-completed.png)

## <a name="prerequisites"></a>Prérequis 

Pour suivre ce didacticiel :

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Créez ensuite une fonction dans la nouvelle Function App.

## <a name="create-function"></a>Créer une fonction déclenchée par un webhook générique

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. Si cette fonction est la première de votre application de fonction, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-generic-webhook-triggered-function/add-first-function.png)

2. Dans la zone de recherche, saisissez `generic`, puis sélectionnez la langue souhaitée pour le modèle déclencheur de Webhook générique. Cette rubrique utilise une fonction C#.

     ![Choisir le modèle déclencheur de Webhook générique](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger.png)

2. Saisissez un **Nom** pour votre fonction, puis sélectionnez **Créer**. 

     ![Créer une fonction déclenchée par un webhook générique dans le portail Azure](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger-2.png) 

2. Dans votre nouvelle fonction, cliquez sur **</> Obtenir l’URL de fonction**, puis copiez et enregistrez la valeur. Vous utilisez cette valeur pour configurer le webhook. 

    ![Examiner le code de fonction](./media/functions-create-generic-webhook-triggered-function/functions-copy-function-url.png)
         
Créez ensuite un point de terminaison webhook dans une alerte de journal d’activité dans Azure Monitor. 

## <a name="create-an-activity-log-alert"></a>Créer une alerte de journal d’activité

1. Dans le portail Azure, accédez au service **Surveillance**, sélectionnez **Alertes**, puis cliquez sur **Ajouter une alerte de journal d’activité**.   

    ![Surveiller](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert.png)

2. Utilisez les paramètres spécifiés dans le tableau :

    ![Créer une alerte de journal d’activité](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings.png)

    | Paramètre      |  Valeur suggérée   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’alerte de journal d’activité** | resource-group-create-alert | Nom de l’alerte de journal d’activité. |
    | **Abonnement** | Votre abonnement | L’abonnement que vous utilisez pour ce didacticiel. | 
    |  **Groupe de ressources** | myResourceGroup | Le groupe de ressources dans lequel les ressources d’alerte sont déployées. L’utilisation du même groupe de ressources que votre application de fonction facilite le nettoyage une fois le didacticiel terminé. |
    | **Catégorie d’événement** | Administratif | Cette catégorie inclut les modifications apportées aux ressources Azure.  |
    | **Type de ressource** | Groupes de ressources | Filtre les alertes pour les activités du groupe de ressources. |
    | **Groupe de ressources**<br/>et **Ressource** | Tous | Surveille toutes les ressources. |
    | **Nom d’opération** | Créer un groupe de ressources | Filtre les alertes pour créer des opérations. |
    | **Niveau** | Informations | Incluez les alertes de niveau d’informations. | 
    | **État** | Succeeded | Filtre les alertes aux actions qui ont réussi. |
    | **Groupe d’actions** | Nouveau | Créez un nouveau groupe d’actions, qui définit l’action effectuée lorsqu’une alerte est renvoyée. |
    | **Nom du groupe d’actions** | function-webhook | Nom destiné à identifier le groupe d’actions.  | 
    | **Nom court** | funcwebhook | Nom court du groupe d’actions. |  

3. Dans **Actions**, ajoutez une action à l’aide des paramètres spécifiés dans la table : 

    ![Ajouter un groupe d’actions](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings-2.png)

    | Paramètre      |  Valeur suggérée   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | CallFunctionWebhook | Nom de l’action. |
    | **Type d’action** | webhook | La réponse à l’alerte est qu’une URL de webhook est appelée. |
    | **Détails** | URL de la fonction | Collez l’URL de webhook de la fonction que vous avez copiée précédemment. |v

4. Cliquez sur **OK** pour créer l’alerte et le groupe d’actions.  

Le webhook est maintenant appelé lorsqu’un groupe de ressources est créé dans votre abonnement. Ensuite, vous mettez à jour le code dans votre fonction pour gérer les données de journal JSON dans le corps de la requête.   

## <a name="update-the-function-code"></a>Mettre à jour le code de fonction

1. Revenez à votre application de fonction sur le portail et développez votre fonction. 

2. Remplacez le code de script C# dans la fonction sur le portail par le code suivant :

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"Webhook was triggered!");
    
        // Get the activityLog object from the JSON in the message body.
        string jsonContent = await req.Content.ReadAsStringAsync();
        JToken activityLog = JObject.Parse(jsonContent.ToString())
            .SelectToken("data.context.activityLog");
    
        // Return an error if the resource in the activity log isn't a resource group. 
        if (activityLog == null || !string.Equals((string)activityLog["resourceType"], 
            "Microsoft.Resources/subscriptions/resourcegroups"))
        {
            log.Error("An error occurred");
            return req.CreateResponse(HttpStatusCode.BadRequest, new
            {
                error = "Unexpected message payload or wrong alert received."
            });
        }
    
        // Write information about the created resource group to the streaming log.
        log.Info(string.Format("Resource group '{0}' was {1} on {2}.",
            (string)activityLog["resourceGroupName"],
            ((string)activityLog["subStatus"]).ToLower(), 
            (DateTime)activityLog["submissionTimestamp"]));
    
        return req.CreateResponse(HttpStatusCode.OK);    
    }
    ```

Vous pouvez maintenant tester la fonction en créant un nouveau groupe de ressources dans votre abonnement.

## <a name="test-the-function"></a>Tester la fonction

1. Cliquez sur l’icône du groupe de ressources à gauche du portail Azure, sélectionnez **+ Ajouter**, saisissez un **Nom de groupe de ressources**, puis sélectionnez **Créer** pour créer un groupe de ressources vide.
    
    ![Créez un groupe de ressources.](./media/functions-create-generic-webhook-triggered-function/functions-create-resource-group.png)

2. Revenez à votre fonction et développez la fenêtre **Journaux**. Une fois le groupe de ressources créé, l’alerte de journal d’activité déclenche le webhook et la fonction est exécutée. Le nom du nouveau groupe de ressources est écrit dans les journaux.  

    ![Ajoutez un paramètre d’application de test.](./media/functions-create-generic-webhook-triggered-function/function-view-logs.png)

3. (Facultatif) Revenez en arrière et supprimez le groupe de ressources que vous avez créé. Notez que cette activité ne déclenche pas la fonction. Ceci est dû au fait que les opérations de suppression sont filtrées par l’alerte. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute lorsqu’une requête est reçue à partir d’un webhook générique. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour en savoir plus sur les déclencheurs webhook, consultez la page [Liaisons HTTP et webhook d’Azure Functions](functions-bindings-http-webhook.md). Pour en savoir plus sur le développement de fonctions en C#, consultez [Informations de référence pour les développeurs de scripts C# Azure Functions](functions-reference-csharp.md).

