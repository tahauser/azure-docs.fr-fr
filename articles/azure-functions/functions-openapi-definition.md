---
title: Créer une définition OpenAPI pour une fonction | Microsoft Docs
description: Créez une définition OpenAPI permettant aux autres applications et services d’appeler votre fonction dans Azure.
services: functions
keywords: OpenAPI, Swagger, applications cloud, services cloud
documentationcenter: ''
author: mgblythe
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: mblythe; glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 8caea30196a7ecdd4226b18963cd8b2040dc7e35
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="create-an-openapi-definition-for-a-function"></a>Créer une définition OpenAPI pour une fonction
Les API REST utilisent souvent une définition OpenAPI (anciennement, fichier [Swagger](http://swagger.io/)). Cette définition contient des informations sur les opérations qui sont disponibles dans une API et sur la façon dont les données de demande et de réponse de l’API doivent être structurées.

Dans ce didacticiel, vous allez créer une fonction qui détermine si la réparation d’urgence d’une éolienne est rentable. Ensuite, vous allez créer une définition OpenAPI pour l’application de la fonction pour que la fonction puisse être appelée à partir d’autres applications et services.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une fonction dans Azure
> * Générer une définition OpenAPI à l’aide des outils OpenAPI
> * Modifier la définition pour fournir des métadonnées supplémentaires
> * Tester la définition en appelant la fonction

## <a name="create-a-function-app"></a>Créer une application de fonction

Vous devez disposer d’une Function App pour héberger l’exécution de vos fonctions. Une application de fonctions vous permet de regrouper des fonctions en une unité logique pour faciliter la gestion, le déploiement et le partage des ressources. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]


## <a name="create-the-function"></a>Création de la fonction

Ce didacticiel utilise une fonction HTTP déclenchée qui accepte deux paramètres : le temps estimé de la réparation (en heures) et la capacité de l’éolienne (exprimée en kilowatts). La fonction calcule ensuite le coût de la réparation et les revenus engendrés par 24 heures de fonctionnement de l’éolienne.

1. Développez votre Function App, puis sélectionnez le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction. 

    ![Page de démarrage rapide des fonctions sur le portail Azure](media/functions-openapi-definition/add-first-function.png)

2. Dans le champ Rechercher, tapez `http`, puis choisissez **C#** pour le modèle de déclencheur HTTP. 
 
    ![Choisir le déclencheur HTTP](./media/functions-openapi-definition/select-http-trigger-portal.png)

3. Tapez `TurbineRepair` comme **Nom** de fonction, choisissez `Function` comme **[Niveau d’authentification](functions-bindings-http-webhook.md#http-auth)**, puis sélectionnez **Créer**.  

    ![Créer une fonction déclenchée par le biais de HTTP](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Remplacez le contenu du fichier run.csx par le code suivant, puis cliquez sur **Enregistrer** :

    ```csharp
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Le code de cette fonction retourne un message `Yes` ou `No` indiquant si la réparation d’urgence est rentable, en comparant l’opportunité de revenus que représente la réparation de l’éolienne et le coût de la réparation. 

1. Pour tester la fonction, cliquez sur **Test** tout à droite pour développer l’onglet de test. Entrez la valeur suivante pour le **corps de la requête**, puis cliquez sur **Exécuter**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testez la fonction dans le portail Azure](media/functions-openapi-definition/test-function.png)

    La valeur suivante est retournée dans le corps de la réponse.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Vous disposez maintenant d’une fonction qui détermine la rentabilité des réparations d’urgence. Ensuite, générez et modifiez une définition OpenAPI pour l’application de fonction.

## <a name="generate-the-openapi-definition"></a>Générer la définition OpenAPI

Vous êtes maintenant prêt à générer la définition OpenAPI. Cette définition peut être utilisée par d’autres technologies Microsoft, telles que API Apps, [PowerApps](functions-powerapps-scenario.md) et [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), ainsi que des outils de développement tiers comme [Postman](https://www.getpostman.com/docs/importing_swagger) et [bien d’autres packages](http://swagger.io/tools/).

1. Sélectionnez uniquement les *verbes* que votre API prend en charge (dans ce cas, POST). Cela rend la définition d’API générée plus claire.

    1. Sous l’onglet **Intégrer** de votre nouvelle fonction de déclencheur HTTP, remplacez **Méthodes HTTP autorisées**par **Méthodes sélectionnées**

    1. Dans **Méthodes HTTP sélectionnées**, décochez toutes les options sauf **POST**, puis cliquez sur **Enregistrer**.

        ![Méthodes HTTP sélectionnées](media/functions-openapi-definition/selected-http-methods.png)
        
1. Cliquez sur le nom de l’application de fonction (par exemple, **function-demo-energy**) > **Fonctionnalités de la plateforme** > **Définition de l’API**.

    ![Définition de l’API](media/functions-openapi-definition/api-definition.png)

1. Sous l’onglet **Définition de l’API**, cliquez sur **Fonction**.

    ![Source de la définition de l’API](media/functions-openapi-definition/api-definition-source.png)

    Cette étape active une suite d’options OpenAPI pour votre application de fonction, notamment un point de terminaison pour héberger un fichier OpenAPI à partir du domaine de votre application de fonction, une copie intégrée de [l’éditeur OpenAPI](http://editor.swagger.io) et un générateur de modèles de définitions d’API.

1. Cliquez sur **Générer le modèle de définition d’API** > **Enregistrer**.

    ![Générer le modèle de définition d’API](media/functions-openapi-definition/generate-template.png)

    Azure analyse votre application de fonction pour trouver des fonctions de déclencheur HTTP, et utilise les informations contenues dans functions.json pour générer une définition OpenAPI. Voici la définition générée :

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Cette définition est décrite comme un _modèle_, car elle nécessite davantage de métadonnées pour être une définition OpenAPI complète. Dans l’étape suivante, vous allez modifier la définition.

## <a name="modify-the-openapi-definition"></a>Modifier la définition OpenAPI
Maintenant que vous avez une définition de modèle, vous la modifiez pour fournir des métadonnées supplémentaires concernant les opérations et les structures de données de l’API. Dans **Définition de l’API**, supprimez la définition générée de l’élément `post` jusqu’en bas de la définition, collez le contenu ci-dessous, puis cliquez sur **Enregistrer**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

Dans ce cas, il vous suffit de coller les métadonnées mises à jour, mais il est important de comprendre les types de modifications que nous avons apportées au modèle par défaut :

+ Nous avons spécifié que l’API génère et consomme des données au format JSON.

+ Nous avons spécifié les paramètres obligatoires (nom et type de données).

+ Nous avons spécifié les valeurs de retour correspondant à une réponse valide (nom et type de données).

+ Nous avons fourni des récapitulatifs et des descriptions claires pour l’API, ses opérations et ses paramètres. Ceci est important pour les personnes qui vont utiliser cette fonction.

+ Nous avons ajouté x-ms-summary et x-ms-visibility, qui sont utilisés dans l’interface utilisateur pour Microsoft Flow et Logic Apps. Pour plus d’informations, consultez [Extensions OpenAPI pour les connecteurs personnalisés dans Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Nous avons conservé la méthode d’authentification par défaut de la clé API dans la définition de la sécurité. Nous aurions modifié cette section de la définition si nous avions utilisé un autre type d’authentification.

Pour plus d’informations sur la définition des opérations d’API, consultez les [spécifications d’OpenAPI](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Tester la définition OpenAPI
Avant d’utiliser la définition de l’API, il est conseillé de la tester dans l’interface utilisateur Azure Functions.

1. Sous l’onglet **Gérer** de votre fonction, sous **Clés de l’hôte**, copiez la clé **Par défaut**.

    ![Copier la clé API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Cette clé peut être utilisée à des fins de test, et lorsque vous appelez l’API à partir d’une application ou d’un service.

1. Revenez à la définition de l’API : **function-demo-energy** > **Fonctionnalités de la plateforme** > **Définition de l’API**.

1. Dans le volet droit, cliquez sur **Authentifier**, entrez la clé API que vous avez copiée, puis cliquez sur **Authentifier**.

    ![S’authentifier avec la clé API](media/functions-openapi-definition/authenticate-api-key.png)

1. Faites défiler la page et cliquez sur **Try this operation** (Essayer cette opération).

    ![Option Try this operation (Essayer cette opération)](media/functions-openapi-definition/try-operation.png)

1. Entrez des valeurs pour les **heures** et la **capacité**.

    ![Saisie des paramètres](media/functions-openapi-definition/parameters.png)

    Notez que l’interface utilisateur utilise les descriptions de la définition de l’API.

1. Cliquez sur **Send Request** (Envoyer une demande), puis cliquez sur l’onglet **Pretty** (Impression automatique) pour afficher la sortie.

    ![Option Send a request (Envoyer une demande)](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer une fonction dans Azure
> * Générer une définition OpenAPI à l’aide des outils OpenAPI
> * Modifier la définition pour fournir des métadonnées supplémentaires
> * Tester la définition en appelant la fonction

Passez à la rubrique suivante pour apprendre à créer une application PowerApps qui utilise la définition OpenAPI que vous avez créée.
> [!div class="nextstepaction"]
> [Appeler une fonction dans PowerApps](functions-powerapps-scenario.md)
