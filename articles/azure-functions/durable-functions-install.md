---
title: "Installer l’extension Fonctions durables et des exemples : Azure"
description: "Découvrez comment installer l’extension Fonctions durables pour Azure Functions, pour le développement de portails ou le développement Visual Studio."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: e1e8b019fe4632b0b6ac02888b562d6718c14fcc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installer l’extension Fonctions durables et des exemples (Azure Functions)

L’extension [Fonctions durables](durable-functions-overview.md) d’Azure Functions est fournie dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Cet article explique comment installer le package et un ensemble d’exemples pour les environnements de développement suivants :

* Visual Studio 2017 (recommandé) 

* Portail Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Actuellement, Visual Studio fournit la meilleure expérience de développement d’applications qui utilisent l’extension Fonctions durables.  Vos fonctions peuvent être exécutées en local, et peuvent également être publiées dans Azure. Vous pouvez commencer par un projet vide, ou par un ensemble d’exemples de fonctions.

### <a name="prerequisites"></a>configuration requise

* Installez la [dernière version de Visual Studio](https://www.visualstudio.com/downloads/) (version 15.3 ou plus). Incluez la charge de travail de **développement Azure** dans vos options d’installation.

### <a name="start-with-sample-functions"></a>Commencer par des exemples de fonctions

1. Téléchargez [le fichier .zip de l’exemple d’application pour Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Vous n’avez pas besoin d’ajouter la référence NuGet, car l’exemple de projet en dispose déjà.
2. Installez et exécutez [l’émulateur de stockage Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 ou plus. Vous pouvez également mettre à jour le fichier *local.appsettings.json* en ajoutant les chaînes de connexion de stockage Azure réelles.
3. Ouvrez le projet dans Visual Studio 2017. 
4. Pour obtenir des instructions sur la façon d’exécuter l’exemple, commencez par [l’exemple de séquence hello/le chaînage de fonction](durable-functions-sequence.md). L’exemple peut être exécuté en local ou publié sur Azure.

### <a name="start-with-an-empty-project"></a>Commencez par un projet vide
 
Suivez les instructions permettant de commencer par l’exemple, mais procédez comme suit au lieu de télécharger le fichier *.zip* :

1. Créez un projet Function App.
2. Ajoutez la référence de packages NuGet suivante dans votre fichier *.csproj* :

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code fournit une expérience de développement local qui couvre toutes les principales plates-formes : Windows, macOS et Linux.  Vos fonctions peuvent être exécutées en local, et peuvent également être publiées dans Azure. Vous pouvez commencer par un projet vide, ou par un ensemble d’exemples de fonctions.

### <a name="prerequisites"></a>configuration requise

* Installer la [dernière version de Visual Studio Code](https://code.visualstudio.com/Download) 

* Suivez les instructions dans la section « Installer Azure Functions Core Tools » de la rubrique [Coder et tester Azure Functions localement](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Si vous possédez déjà les outils Azure Functions Cross Platform Tools, mettez-les à jour avec la dernière version disponible.

*  Installez et exécutez [l’émulateur de stockage Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) version 5.2 ou plus. Vous pouvez également mettre à jour le fichier *local.appsettings.json* en ajoutant la connexion de stockage Azure réelle. 


### <a name="start-with-sample-functions"></a>Commencer par des exemples de fonctions

1. Clonez le [référentiel des fonctions durables](https://github.com/Azure/azure-functions-durable-extension.git).
2. Sur votre machine, accédez au [dossier des exemples de script C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Installez Azure Functions Durable Extension en lançant la commande suivante dans une invite de commande/fenêtre de terminal :

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
4. Exécutez Azure Storage Emulator ou mettez à jour le fichier *local.appsettings.json* en ajoutant la chaîne de connexion de stockage Azure réelle.
3. Ouvrez le projet dans Visual Studio Code. 
5. Pour obtenir des instructions sur la façon d’exécuter l’exemple, commencez par [l’exemple de séquence hello/le chaînage de fonction](durable-functions-sequence.md). L’exemple peut être exécuté en local ou publié sur Azure.
6. Démarrez le projet en exécutant la commande suivante dans une invite de commande/fenêtre de terminal :
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Commencez par un projet vide
 
1. Dans l'invite de commande/fenêtre de terminal, accédez au dossier qui hébergera votre application de fonction.
2. Installez Azure Functions Durable Extension en lançant la commande suivante dans une invite de commande/fenêtre de terminal :

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.1.0-beta2
    ```
3. Créez un projet Function App en exécutant la commande suivante :

    ```bash
    func init
    ``` 
4. Exécutez Azure Storage Emulator ou mettez à jour le fichier *local.appsettings.json* en ajoutant la chaîne de connexion de stockage Azure réelle.
5. Créez ensuite une nouvelle fonction en exécutant la commande suivante, puis suivez les étapes de l'assistant :

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > Actuellement, le modèle Fonction durable n'est pas disponible, mais vous pouvez commencer par l'une des options prises en charge, avant de modifier le code. Utilisez pour référence les exemples [Client d’orchestration](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [Déclencheur d'orchestration](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence) et [Déclencheur d'activité](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Ouvrez le dossier du projet dans Visual Studio Code et continuez en modifiant le code du modèle. 
7. Démarrez le projet en exécutant la commande suivante dans une invite de commande/fenêtre de terminal :
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Portail Azure

Si vous préférez, vous pouvez utiliser le portail Azure du développement Fonctions durables.

### <a name="create-an-orchestrator-function"></a>Créer une fonction d’orchestrateur

1. Créez une nouvelle application de fonction sur le site [functions.azure.com](https://functions.azure.com/signin).

2. Configurez l’application de fonction pour qu’elle [utilise la version du runtime 2.0](set-runtime-version.md).

3. Créez une nouvelle fonction en sélectionnant **« créer votre propre fonction personnalisée »**.

4. Définissez les options **Langage** sur **C#**, **Scénario** sur **Durable Functions**, puis sélectionnez le modèle **Durable Functions Http Starter - C#**.

5. Sous **Extensions non installées**, cliquez sur **Installer** pour télécharger l’extension depuis le site NuGet.org. 

6. Une fois l'installation terminée, passez à la création d'une fonction de client d'orchestration **« HttpStart »** créée en sélectionnant le modèle **Durable Functions Http Starter - C#**.

7. Créez maintenant une fonction d'orchestration **« HelloSequence »** à partir du modèle **Durable Functions Orchestrator - C#**.

8. Et la dernière fonction s'appellera **« Bonjour »** à partir du modèle **Durable Functions Activity - C#**.

9. Accédez à la fonction **« HttpStart »** et copiez son URL.

10. Utilisez Postman ou cURL pour appeler la fonction durable. Avant de procéder au test, remplacez l'URL **{functionName}** par le nom de la fonction d'orchestrateur, **HelloSequence**.  Aucune donnée n'est requise, utilisez simplement le verbe POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Appelez ensuite le point de terminaison **« statusQueryGetUri »** pour afficher l'état actuel de la fonction Durable

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Continuez à appeler le point de terminaison **« statusQueryGetUri »** jusqu'à ce que le statut indique **« Completed »** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Félicitations ! Votre première fonction durable est maintenant opérationnelle dans le portail Azure !

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter l’exemple de chaînage de fonction](durable-functions-sequence.md)
