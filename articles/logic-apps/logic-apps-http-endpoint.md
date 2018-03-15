---
title: "Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP - Azure Logic Apps | Microsoft Docs"
description: "Configurer des points de terminaison HTTP pour appeler, déclencher ou imbriquer des workflows pour Azure Logic Apps"
services: logic-apps
keywords: workflows, points de terminaison HTTP
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: de4f4ee086fbf3799fcac1f1b008d9237b5e7a09
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques

Vous pouvez exposer en mode natif les points de terminaison HTTP synchrones en tant que déclencheurs sur des applications logiques, afin que vous puissiez déclencher ou appeler vos applications logiques via une URL. Vous pouvez également imbriquer des workflows dans vos applications logiques à l’aide d’un modèle de points de terminaison pouvant être appelés.

Pour créer des points de terminaison HTTP, vous pouvez ajouter ces déclencheurs afin que vos applications logiques puissent recevoir des requêtes entrantes :

* [Requête](../connectors/connectors-native-reqres.md)

* [Déclencheur ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [Déclencheur HTTPWebhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Bien que ces exemples utilisent le déclencheur de **requête**, vous pouvez utiliser l’un des déclencheurs HTTP répertoriés, et tous les principes s’appliquent de la même façon aux autres types de déclencheurs.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Configurer un point de terminaison HTTP pour votre application logique

Pour créer un point de terminaison HTTP, ajoutez un déclencheur qui peut recevoir des requêtes entrantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). Accédez à votre application logique et ouvrez le Concepteur d’application logique.

2. Ajoutez un déclencheur qui permet à votre application logique de recevoir des requêtes entrantes. Par exemple, ajoutez le déclencheur **Requête** à votre application logique.

3.  Sous **Schéma JSON du corps de la requête**, vous pouvez éventuellement entrer un schéma JSON pour la charge utile (données) que le déclencheur est susceptible de recevoir.

    Le concepteur utilise ce schéma pour générer des jetons que votre application logique peut utiliser pour consommer, analyser et transmettre des données à partir du déclencheur via votre workflow. 
    Cliquez sur le lien renvoyant à la section [Jetons générés à partir de schémas JSON pour votre application logique](#generated-tokens) pour en savoir plus.

    Pour cet exemple, entrez le schéma affiché dans le concepteur :

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![Ajouter l’action de la requête][1]

    > [!TIP]
    > 
    > Vous pouvez générer un schéma pour un exemple de charge utile JSON à partir d’un outil tel que [jsonschema.net](http://jsonschema.net/), ou dans le déclencheur de **requête** en choisissant **Utiliser l’exemple de charge utile pour générer le schéma**. 
    > Entrez votre exemple de charge utile et choisissez **Terminé**.

    Par exemple, cet exemple de charge utile :

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    génère ce schéma :

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Enregistrez votre application logique. Sous **POST HTTP pour cette URL**, vous devez maintenant rechercher une URL de rappel générée, comme dans cet exemple :

    ![URL de rappel générée pour le point de terminaison](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Cette URL contient une clé de signature d’accès partagé (SAP) dans les paramètres de requête utilisés pour l’authentification. 
    Vous pouvez également obtenir l’URL de point de terminaison HTTP à partir de la vue d’ensemble de votre application logique dans le portail Azure. Sous **Historique du déclencheur**, sélectionnez votre déclencheur :

    ![Obtenir l’URL de point de terminaison HTTP à partir du portail Azure][2]

    Ou vous pouvez obtenir l’URL en appelant :

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Modifier la méthode HTTP pour votre déclencheur

Par défaut, le déclencheur de **requête** attend une requête HTTP POST, mais vous pouvez utiliser une méthode HTTP différente. 

> [!NOTE]
> Vous pouvez spécifier un seul type de méthode.

1. Sur votre déclencheur de **requête**, choisissez **Afficher les options avancées**.

2. Ouvrez la liste **Méthode**. Dans cet exemple, sélectionnez **GET** pour pouvoir tester ultérieurement l’URL de votre point de terminaison HTTP.

    > [!NOTE]
    > Vous pouvez sélectionner n’importe quelle autre méthode HTTP, ou spécifier une méthode personnalisée pour votre propre application logique.

    ![Changer de méthode HTTP](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Accepter les paramètres via votre URL de point de terminaison HTTP

Lorsque vous souhaitez que votre URL de point de terminaison HTTP accepte des paramètres, personnalisez le chemin d’accès relatif de votre déclencheur.

1. Sur votre déclencheur de **requête**, choisissez **Afficher les options avancées**. 

2. Sous **Méthode**, spécifiez la méthode HTTP que vous souhaitez que votre requête utilise. Dans cet exemple, sélectionnez la méthode **GET**, si vous ne l’avez pas déjà fait, pour pouvoir tester l’URL de votre point de terminaison HTTP.

      > [!NOTE]
      > Lorsque vous spécifiez un chemin d’accès relatif pour votre déclencheur, vous devez également spécifier explicitement une méthode HTTP pour votre déclencheur.

3. Sous **Chemin d’accès relatif**, spécifiez le chemin d’accès relatif du paramètre que votre URL doit accepter, par exemple `customers/{customerID}`.

    ![Spécifier la méthode HTTP et le chemin d’accès relatif pour le paramètre](./media/logic-apps-http-endpoint/relativeurl.png)

4. Pour utiliser le paramètre, ajoutez une action **Response** à votre application logique. (Sous votre déclencheur, choisissez **Nouvelle étape** > **Ajouter une action** > **Response**) 

5. Pour la valeur **Corps** de votre réponse, incluez le jeton pour le paramètre que vous avez spécifié dans le chemin d’accès relatif de votre déclencheur.

    Par exemple, pour renvoyer `Hello {customerID}`, mettez à jour la valeur **Corps** de votre réponse avec `Hello {customerID token}`. 
    La liste de contenu dynamique doit apparaître et afficher le jeton `customerID` à sélectionner.

    ![Ajouter un paramètre au corps de la réponse](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    Votre valeur **Corps** doit ressembler à cet exemple :

    ![Corps de la réponse avec le paramètre](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Enregistrez votre application logique. 

    Votre URL de point de terminaison HTTP inclut désormais le chemin d’accès relatif, par exemple : 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. Pour tester votre point de terminaison HTTP, copiez et collez l’URL mise à jour dans une autre fenêtre de navigateur, mais remplacez `{customerID}` par `123456`, puis appuyez sur ENTRÉE.

    Votre navigateur doit afficher le texte suivant : 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>Jetons générés à partir de schémas JSON pour votre application logique

Lorsque vous fournissez un schéma JSON dans votre déclencheur de **requête**, le Concepteur d’application logique génère des jetons pour les propriétés de ce schéma. Vous pouvez ensuite utiliser ces jetons pour transmettre des données au workflow de votre application logique.

Pour cet exemple, si vous ajoutez les propriétés `title` et `name` à votre schéma JSON, leurs jetons sont désormais disponibles pour être utilisés dans les étapes ultérieures du workflow. 

Voici le schéma JSON complet :

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>Créer des workflows imbriqués pour les applications logiques

Vous pouvez imbriquer des workflows dans votre application logique en ajoutant d’autres applications logiques qui peuvent recevoir des requêtes. Pour inclure ces applications logiques, ajoutez l’action **Azure Logic Apps - Choisir un workflow Logic Apps** à votre déclencheur. Vous pouvez ensuite choisir entre des applications logiques éligibles.

![Ajouter une autre application logique](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Appeler ou déclencher des applications logiques via des points de terminaison HTTP

Une fois que vous avez créé votre point de terminaison HTTP, vous pouvez déclencher votre application logique via une méthode `POST` vers l’URL complète. Les applications logiques ont une prise en charge intégrée pour les points de terminaison à accès direct.

> [!NOTE] 
> Pour exécuter manuellement une application logique à tout moment, dans la barre d’outils Concepteur d’application logique ou Affichage du code de l’application logique, choisissez **Exécuter**.

## <a name="reference-content-from-an-incoming-request"></a>Référencer le contenu à partir d’une requête entrante

Si le type de contenu est `application/json`, vous pouvez référencer des propriétés à partir de la requête entrante. Sinon, le contenu est traité comme une seule unité binaire que vous pouvez transmettre à d’autres API. Pour référencer ce contenu dans le workflow, vous devez le convertir. Par exemple, si vous transmettez le contenu `application/xml`, vous pouvez utiliser `@xpath()` pour une extraction XPath, ou `@json()` pour effectuer la conversion de XML vers JSON. Découvrez plus en détail comment [utiliser les types de contenu](../logic-apps/logic-apps-content-type.md).

Pour obtenir la sortie à partir d’une requête entrante, vous pouvez utiliser la fonction `@triggerOutputs()`. La sortie peut ressembler à cet exemple :

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Pour accéder à la propriété `body`, vous pouvez utiliser le raccourci `@triggerBody()`. 

## <a name="respond-to-requests"></a>Répondre aux requêtes

Vous souhaitez peut-être répondre à certaines requêtes qui démarrent une application logique en renvoyant du contenu à l’appelant. Pour créer le code d’état, l’en-tête et le corps de votre réponse, vous pouvez utiliser l’action **Response**. Cette action peut apparaître n’importe où dans votre application logique, et pas seulement à la fin de votre workflow.

> [!NOTE] 
> Si votre application logique n’inclut pas d’action **Response**, le point de terminaison HTTP répond *immédiatement* avec un état **202 - Accepté**. En outre, pour que la requête d’origine obtienne la réponse, toutes les étapes nécessaires pour la réponse doivent être terminées avant la [limite du délai d’expiration de la requête](./logic-apps-limits-and-config.md), sauf si vous appelez le workflow en tant qu’application logique imbriquée. Si aucune réponse n’est produite avant cette limite, la requête entrante expire et reçoit la réponse HTTP **408 - Dépassement du délai d’expiration par le client**. Pour les applications logiques imbriquées, l’application logique parente continue à attendre une réponse jusqu’à ce qu’elle se termine, quelle que soit la durée de l’opération.

### <a name="construct-the-response"></a>Construire la réponse

Vous pouvez inclure plusieurs en-têtes et n’importe quel type de contenu dans le corps de la réponse. Dans l’exemple de réponse, l’en-tête spécifie que la réponse a le type de contenu `application/json`. et que le corps contient `title` et `name`, selon le schéma JSON mis à jour précédemment pour le déclencheur de **requête**.

![Action HTTP Response][3]

Les réponses ont ces propriétés :

| Propriété | DESCRIPTION |
| --- | --- |
| statusCode |Indique le code d’état HTTP pour répondre à la requête entrante. Ce code peut être tout code d’état valide commençant par 2xx, 4xx ou 5xx. Cependant, les codes d’état 3xx ne sont pas autorisés. |
| headers |Définit un nombre quelconque d’en-têtes à inclure dans la réponse. |
| body |Indique un objet corps qui peut être une chaîne, un objet JSON ou même du contenu binaire référencé à partir d’une étape précédente. |

Voici à quoi ressemble désormais le schéma JSON pour l’action **Response** :

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> Pour afficher la définition JSON complète de votre application logique, choisissez **mode Code** dans le Concepteur d’application logique.

## <a name="q--a"></a>Questions et réponses

#### <a name="q-what-about-url-security"></a>Q : Qu’en est-il de la sécurité de l’URL ?

R : Les URL de rappel de l’application logique sont générées de façon sécurisée par Azure via une signature d’accès partagé (SAP). Cette signature est transmise directement comme paramètre de requête et doit être validée avant que votre application logique puisse être déclenchée. Azure génère cette signature via la combinaison unique d’une clé secrète par application logique, du nom du déclencheur et de l’opération qui est effectuée. Ainsi, à moins que quelqu’un ait accès à la clé secrète de l’application logique, personne ne peut générer de signature valide.

   > [!IMPORTANT]
   > Pour les systèmes de production et sécurisés, nous vous déconseillons fortement d’appeler votre application logique directement à partir du navigateur, car :
   > 
   > * la clé d’accès partagé s’affiche dans l’URL ;
   > * vous ne pouvez pas gérer de stratégies de contenu sécurisé en raison du partage de domaines entre les clients de l’application logique.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Q : Puis-je configurer des points de terminaison HTTP de façon plus poussée ?

R : Oui, les points de terminaison HTTP prennent en charge une configuration plus avancée via [**Gestion des API**](../api-management/api-management-key-concepts.md). Ce service vous offre également la possibilité de gérer toutes vos API de façon systématique, y compris les applications logiques, de configurer les noms de domaines personnalisés, d’utiliser plus de méthodes d’authentification et bien plus encore, comme par exemple :

* [Modification de la méthode de la requête](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Modification des segments d’URL de la requête](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Configuration de vos domaines Gestion des API dans le [portail Azure](https://portal.azure.com/ "portail Azure")
* Configuration d’une stratégie pour vérifier l’authentification de base

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>Q : Qu’est-ce qui a changé suite à la migration du schéma depuis la version préliminaire du 1er décembre 2014 ?

R : Voici un résumé des modifications apportées :

| Version préliminaire du 1er décembre 2014 | 1er juin 2016 |
| --- | --- |
| Cliquez sur l’application API **Écouteur HTTP** |Cliquez sur **Déclenchement manuel** (aucune application API nécessaire) |
| Paramètre d’écouteur HTTP «*Envoie une réponse automatiquement*» |Incluez ou non une action **Response** dans la définition du workflow |
| Configurez l’authentification de base ou OAuth |via la gestion des API |
| Configurer la méthode HTTP |Sous **Afficher les options avancées**, choisissez une méthode HTTP |
| Configurer le chemin d’accès relatif |Sous **Afficher les options avancées**, ajoutez un chemin d’accès relatif |
| Référencez le corps entrant par le biais de `@triggerOutputs().body.Content` |Référencez via `@triggerOutputs().body` |
| **Envoyer une réponse HTTP** sur l’écouteur HTTP |Cliquez sur **Répondre à la requête HTTP** (aucune application API nécessaire) |

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions ou y répondre et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

* [Créer des définitions d’application logique](./logic-apps-author-definitions.md)
* [Gérer les erreurs et exceptions](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png