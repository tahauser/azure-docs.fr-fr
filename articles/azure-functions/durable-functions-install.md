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
ms.openlocfilehash: 03326304e438f3b716c43d268f07d8e22cd15ea3
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Installer l’extension Fonctions durables et des exemples (Azure Functions)

L’extension [Fonctions durables](durable-functions-overview.md) d’Azure Functions est fournie dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Cet article explique comment installer le package et un ensemble d’exemples pour les environnements de développement suivants :

* Visual Studio 2017 (recommandé) 
* Portail Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Actuellement, Visual Studio fournit la meilleure expérience de développement d’applications qui utilisent l’extension Fonctions durables.  Vos fonctions peuvent être exécutées en local, et peuvent également être publiées dans Azure. Vous pouvez commencer par un projet vide, ou par un ensemble d’exemples de fonctions.

### <a name="prerequisites"></a>Composants requis

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

## <a name="azure-portal"></a>Portail Azure

Si vous préférez, vous pouvez utiliser le portail Azure du développement Fonctions durables.

### <a name="create-an-orchestrator-function"></a>Créer une fonction d’orchestrateur

1. Créez une nouvelle application de fonction sur le site [functions.azure.com](https://functions.azure.com/signin).
2. Configurez l’application de fonction pour qu’elle [utilise la version du runtime 2.0](functions-versions.md).
3. Créez une fonction, en choisissant « C# » pour le langage et « Tout » pour le scénario. Ensuite, sélectionnez le modèle **Durable Functions Orchestrator - C#**.
4. Sous **Extensions non installées**, cliquez sur **Installer** pour télécharger l’extension depuis le site NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Copier un exemple de code dans l’application de fonction

1. Téléchargez le fichier [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip).
2. Décompressez l’exemple de fichier dans le répertoire `D:\home\site\wwwroot` de l’application de fonction, à l’aide de Kudu ou de FTP.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter l’exemple de chaînage de fonction](durable-functions-sequence.md)
