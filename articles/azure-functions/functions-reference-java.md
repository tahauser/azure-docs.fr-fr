---
title: "Information de référence pour les développeurs Java sur Azure Functions | Microsoft Docs"
description: "Découvrez comment développer des fonctions à l’aide de Java."
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, java"
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 09a48d61cb27b4db0778295565d167a0688cc99f
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="azure-functions-java-developer-guide"></a>Guide des développeurs Java sur Azure Functions
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>Modèle de programmation 

Votre fonction Azure doit être une méthode de classe sans état qui traite une entrée et produit une sortie. Bien que vous soyez autorisé à écrire des méthodes d’instance, votre fonction ne doit pas dépendre d’un champ d’instance de la classe. Toutes les méthodes de fonction doivent présenter un modificateur d’accès `public`.

## <a name="triggers-and-annotations"></a>Déclencheurs et annotations

Une fonction Azure est généralement appelée en raison d’un déclencheur externe. Votre fonction doit traiter ce déclencheur et les entrées associées, puis produire une ou plusieurs sorties.

Les annotations Java sont incluses dans le package `azure-functions-java-core`, de manière à lier des entrées et des sorties avec vos méthodes. Les déclencheurs d’entrée et les annotations de liaison de sortie pris en charge sont inclus dans le tableau suivant :

Liaison | Annotation
---|---
CosmosDB | Non applicable
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | Non applicable
Notification Hubs | Non applicable
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
File d’attente de stockage | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Table de stockage | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Minuteur | <ul><li>`TimerTrigger`</li></ul>
Twilio | Non applicable

Les entrées et sorties des déclencheurs peuvent également être définies dans le fichier [function.json](/azure/azure-functions/functions-reference#function-code) de votre application.

> [!IMPORTANT] 
> Vous devez configurer un compte de stockage Azure dans votre fichier [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) pour exécuter les déclencheurs de table, de file d’attente ou Azure Storage Blob en local.

Exemple d’utilisation des annotations :

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

La même fonction, écrite sans annotation :

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

avec le fichier `function.json` correspondant :

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>Types de données

Vous êtes libre d’utiliser tous les types de données Java pour les données d’entrée et de sortie, y compris les types natifs, les types Java personnalisés et les types Azure spécialisés définis dans le package `azure-functions-java-core`. Le runtime Azure Functions tente de convertir l’entrée reçue selon le type demandé par votre code.

### <a name="strings"></a>Chaînes

Les valeurs passées dans les méthodes de fonction seront transtypées en chaînes si le type de paramètre d’entrée de la fonction présente le type `String`. 

### <a name="plain-old-java-objects-pojos"></a>POJO (Plain Old Java Objects)

Les chaînes mises au format JSON seront transtypées en types Java si l’entrée de la méthode de fonction attend ce type. Cette conversion vous permet de passer des entrées JSON dans vos fonctions et d’utiliser des types Java dans votre code, sans avoir à implémenter cette conversion dans ce code.

Les types POJO utilisés en tant qu’entrées des fonctions doivent présenter le même modificateur d’accès `public` que les méthodes de fonction dans lesquelles ils sont utilisés. Vous n’êtes pas obligé de déclarer des champs de classe POJO comme `public`. Par exemple, une chaîne JSON `{ "x": 3 }` peut être convertie en type POJO suivant :

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Données binaires

Les données binaires sont représentées en tant qu’élément `byte[]` dans votre code Azure Functions. Liez des entrées et sorties binaires à vos fonctions en définissant le champ `dataType` du fichier function.json sur `binary` :

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Ensuite, utilisez-le dans le code votre fonction :

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Utilisez le type `OutputBinding<byte[]>` pour effectuer une liaison de sortie binaire.


## <a name="function-method-overloading"></a>Surcharge de la méthode de fonction

Vous êtes autorisé à surcharger des méthodes de fonction présentant le même nom, mais des types différents. Par exemple, vous pouvez avoir les paramètres `String echo(String s)` et `String echo(MyType s)` dans une classe, et le runtime Azure Functions détermine lequel appeler en examinant le type d’entrée (pour l’entrée HTTP, le type MIME `text/plain` mène à `String` alors que l’élément `application/json` représente `MyType`).

## <a name="inputs"></a>Entrées

Les entrées sont réparties en deux catégories dans Azure Functions : l’une correspond à l’entrée du déclencheur et l’autre, à l’entrée supplémentaire. Bien qu’elles soient différentes dans `function.json`, leur utilisation est identique dans le code Java. L’extrait de code suivant en est un exemple :

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

L’annotation `@BindingName` accepte une propriété `String` qui représente le nom de la liaison/du déclencheur défini dans `function.json` :

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Par conséquent, lorsque cette fonction est appelée, la charge utile des requêtes HTTP transmet un élément `String` facultatif pour l’argument `in` et un type `MyObject` du service Stockage Table Azure transmis à l’argument `obj`. Utilisez le type `Optional<T>` pour gérer les entrées dans vos fonctions qui peuvent être de valeur Null.

## <a name="outputs"></a>Outputs

Les sorties peuvent être exprimées en tant que paramètres de sortie ou de valeur renvoyée. S’il n’existe qu’une seule sortie, nous vous recommandons d’utiliser la valeur renvoyée. Lorsqu’il y a plusieurs sorties, vous devez utiliser les paramètres de sortie.

La valeur renvoyée correspond à la forme de sortie la plus simple : vous renvoyez simplement la valeur de n’importe quel type, et le runtime Azure Functions tente de la marshaler vers le type réel (comme une réponse HTTP). Dans `functions.json`, vous utilisez l’élément `$return` en tant que nom de la liaison de sortie.

Pour générer plusieurs valeurs de sortie, utilisez le type `OutputBinding<T>` défini dans le package `azure-functions-java-core`. Si vous devez créer une réponse HTTP et envoyer (push) un message vers une file d’attente, vous pouvez écrire quelque chose comme ce qui suit :

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

qui doit définir la liaison de sortie dans le fichier `function.json` :

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Types spécialisés

Parfois, une fonction doit disposer d’un contrôle détaillé sur les entrées et les sorties. Les types spécialisés du package `azure-functions-java-core` vous permettent de manipuler les informations des demandes et d’adapter l’état de retour d’un déclencheur HTTP :

| Type spécialisé      |       Cible        | Utilisation classique                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Déclencheur HTTP     | Obtention des requêtes, en-têtes et méthodes |
| `HttpResponseMessage<T>` | Liaison de sortie HTTP | État de retour autre que 200   |

> [!NOTE] 
> Vous pouvez également utiliser l’annotation `@BindingName` pour obtenir des requêtes et en-têtes HTTP. Par exemple, `@BindingName("name") String query` itère les en-têtes et requêtes des demandes HTTP et passe cette valeur à la méthode. Par exemple, l’élément `query` aura la valeur `"test"` si l’URL de la demande est la suivante : `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Métadonnées

Les métadonnées proviennent de différentes sources, telles que les en-têtes HTTP, les requêtes HTTP et les [métadonnées de déclencheur](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Utilisez l’annotation `@BindingName` avec le nom des métadonnées pour obtenir cette valeur.

Par exemple, l’élément `queryValue` de l’extrait de code suivant est `"test"` si l’URL demandée est `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Contexte d’exécution des fonctions

Vous interagissez avec l’environnement d’exécution Azure Functions via l’objet `ExecutionContext` défini dans le package `azure-functions-java-core`. Utilisez l’objet `ExecutionContext` pour exploiter les informations des appels et du runtime Functions dans votre code.

### <a name="logging"></a>Journalisation

Vous pouvez accéder à l’enregistreur d’événements du runtime Functions via l’objet `ExecutionContext`. Cet enregistreur est lié à Azure Monitor et vous permet de signaler les avertissements et erreurs générés lors de l’exécution de la fonction.

L’exemple de code suivant enregistre un message d’avertissement lorsque le corps de demande reçue est vide.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)
