---
title: Créer votre première fonction dans Azure avec Java et Maven | Microsoft Docs
description: Créez et publiez une fonction simple déclenchée par HTTP dans Azure avec Java et Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, traitement des événements, calcul, architecture sans serveur
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 81d9d8790a750f34133f3f00dafc15c56185d7b1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Créer votre première fonction dans Azure avec Java et Maven (aperçu)

> [!NOTE] 
> Java pour Azure Functions est actuellement disponible en préversion.

Ce démarrage rapide vous guide dans la création d’un projet de fonction [sans serveur](https://azure.microsoft.com/overview/serverless-computing/) avec Maven, dans le test de ce projet localement et son déploiement sur Azure Functions. Quand vous avez terminé, vous disposez d’une application de fonction déclenchée par HTTP qui s’exécute dans Azure.

![Accéder à une fonction Hello World à partir de la ligne de commande avec cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour développer une application de fonction avec Java, les éléments suivants doivent être installés :

-  [Java Developer Kit (JDK)](https://www.azul.com/downloads/zulu/) version 8.
-  [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.
-  [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

[Azure Functions Core Tools 2.0](https://www.npmjs.com/package/azure-functions-core-tools) fournit un environnement de développement local pour l’écriture, l’exécution et le débogage des fonctions Azure Functions. 

Pour installer, consultez la section [Installation](https://github.com/azure/azure-functions-core-tools#installing) et recherchez les instructions spécifiques à votre système d’exploitation de prédilection (Windows, Linux, Mac).

Vous pouvez également l’installer manuellement avec [npm](https://www.npmjs.com/), y compris avec [Node.js](https://nodejs.org/), après avoir installé les éléments requis suivants :

-  La version la plus récente de [.NET Core](https://www.microsoft.com/net/core).
-  [Node.js](https://nodejs.org/download/) version 8.6 ou ultérieure.

Pour procéder à une installation basée sur npm, exécutez :

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Si vous avez des difficultés pour installer Azure Functions Core Tools version 2.0, consultez [Runtime de la version 2.x](/azure/azure-functions/functions-run-local#version-2x-runtime).

## <a name="generate-a-new-functions-project"></a>Générer un nouveau projet Functions

Dans un dossier vide, exécutez la commande suivante pour générer le projet Functions à partir d’un [archétype Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven vous invite à entrer les valeurs nécessaires pour terminer la génération du projet. Pour les valeurs de _groupId_, _artifactId_ et _version_, consultez les informations de référence sur les [conventions de nommage de Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). La valeur de _appName_ doit être unique dans Azure : par défaut, Maven génère donc un nom d’application basé sur la valeur précédemment entrée pour _artifactId_. La valeur de _packageName_ détermine le package Java pour le code de fonction généré.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven crée les fichiers projet dans un nouveau dossier avec le nom de _artifactId_. Le code généré dans le projet est une fonction simple [déclenchée par HTTP](/azure/azure-functions/functions-bindings-http-webhook) qui retourne le corps de la demande :

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

Accédez au dossier du projet nouvellement créé, puis générez et exécutez la fonction avec Maven :

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Si vous rencontrez cette exception : `javax.xml.bind.JAXBException` avec Java 9, consultez la solution de contournement sur [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Vous voyez cette sortie lorsque la fonction est en cours d’exécution :

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Déclenchez la fonction à partir de la ligne de commande en utilisant cURL dans un nouveau terminal :

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Utilisez `Ctrl-C` dans le terminal pour arrêter le code de la fonction.

## <a name="deploy-the-function-to-azure"></a>Déployer la fonction sur Azure

Le processus de déploiement sur Azure Functions utilise les informations d’identification du compte provenant d’Azure CLI. [Connectez-vous avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), puis déployez votre code dans une nouvelle application de fonction en utilisant la cible Maven `azure-functions:deploy`.

```
az login
mvn azure-functions:deploy
```

Quand le déploiement est terminé, vous voyez l’URL que vous pouvez utiliser pour accéder à votre application de fonction Azure :

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Testez l’application de fonction en cours d’exécution sur Azure avec cURL :

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une application de fonction Java avec un déclencheur HTTP simple et vous l’avez déployée sur Azure Functions.

- Pour plus d’informations sur le développement de fonctions Java, consultez le [Guide du développeur de fonctions Java](functions-reference-java.md).
- Ajoutez des fonctions supplémentaires avec différents déclencheurs à votre projet en utilisant la cible Maven `azure-functions:add`.
- Déboguez les fonctions localement avec Visual Studio Code. Avec le [pack d’extension Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) installé et votre projet de fonctions ouvert dans Visual Studio Code, [attachez le débogueur](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) au port 5005. Ensuite, définissez un point d’arrêt dans l’éditeur et déclenchez votre fonction alors qu’elle s’exécute localement : ![Déboguer des fonctions dans Visual Studio Code](media/functions-create-java-maven/vscode-debug.png)
- Déboguez les fonctions à distance avec Visual Studio Code. Pour obtenir des instructions, consultez la documentation relative à [l’écriture d’applications Java sans serveur](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud).
