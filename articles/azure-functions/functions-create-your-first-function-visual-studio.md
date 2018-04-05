---
title: Créer votre première fonction dans Azure à l’aide de Visual Studio | Microsoft Docs
description: Créez et publiez une fonction HTTP déclenchée simple vers Azure à l’aide d’Azure Functions Tools pour Visual Studio 2017.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, functions, traitement des événements, calcul, architecture sans serveur
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/13/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: a1f0a022b4620b15e2d76a127ed48e5472e4a5bb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-your-first-function-using-visual-studio"></a>Créer votre première fonction à l’aide de Visual Studio

Azure Functions vous permet d’exécuter votre code dans un environnement [sans serveur](https://azure.microsoft.com/overview/serverless-computing/) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable.

Dans cet article, vous allez découvrir comment utiliser les outils Visual Studio 2017 pour Azure Functions afin de créer et de tester en local une fonction « Hello World ». Vous allez ensuite publier le code de la fonction dans Azure. Ces outils sont disponibles dans le cadre de la charge de travail de développement Azure dans Visual Studio 2017.

![Code Azure Functions dans un projet Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

Cette rubrique inclut [une vidéo](#watch-the-video) qui présente les mêmes étapes de base.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

* Installez [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/) ou une version ultérieure comprenant la charge de travail **Développement Azure**.

    ![Installer Visual Studio 2017 avec la charge de travail de développement Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

    Si vous avez déjà installé Visual Studio, assurez-vous que vous n’avez pas de mises à jour en attente. 

* Si vous avez installé la charge de travail Développement Azure avec Visual Studio 2017 version 15.4 ou antérieure, vous devrez peut-être également [mettre à jour vos outils Azure Functions](functions-develop-vs.md#check-your-tools-version). 
    
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-a-function-app-project"></a>Créer un projet d’application de fonction

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio crée un projet qui contient une classe qui présente un code réutilisable pour le type de fonction sélectionné. L’attribut **FunctionName** de la méthode définit le nom de la fonction. L’attribut **HttpTrigger** spécifie que la fonction est déclenchée par une requête HTTP. Le code réutilisable envoie une réponse HTTP qui inclut une valeur de la chaîne de requête ou du corps de requête. Vous pouvez ajouter des liaisons d’entrée et de sortie à une fonction en appliquant les attributs appropriés à la méthode. Pour plus d’informations, voir la section [Déclencheurs et liaisons](functions-dotnet-class-library.md#triggers-and-bindings) de l’article [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md).

![Fichier du code de fonction](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Maintenant que vous avez créé un projet de fonction et une fonction déclenchée via HTTP, vous pouvez la tester sur votre ordinateur local.

## <a name="test-the-function-locally"></a>Tester la fonction en local

Azure Functions Core Tools vous permet d’exécuter un projet Azure Functions sur votre ordinateur de développement local. Vous êtes invité à installer ces outils la première fois que vous démarrez une fonction dans Visual Studio.  

1. Pour tester votre fonction, appuyez sur F5. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer Azure Functions Core (CLI) Tools. Vous devrez peut-être activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

2. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.  

    ![Azure runtime local](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande locale GET : 

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Pour arrêter le débogage, appuyez sur MAJ + F5.

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Vous devez disposer d’une application de fonction dans votre abonnement Azure avant de pouvoir publier votre projet. Vous pouvez créer une application de fonction directement à partir de Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Tester votre fonction dans Azure

1. Copiez l’URL de base de l’application de fonction à partir de la page de profil de publication. Remplacez la partie `localhost:port` de l’URL que vous avez utilisée lors du test en local de la fonction par la nouvelle URL de base. Comme auparavant, assurez-vous d’ajouter la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande.

    L’URL qui appelle la fonction déclenchée via HTTP doit être au format suivant :

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande distante GET : 

    ![Réponse de la fonction dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)

## <a name="watch-the-video"></a>Regarder la vidéo

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio pour créer et publier une application de fonction C# à l’aide d’une fonction HTTP déclenchée simple. 

+ Pour savoir comment configurer votre projet de façon qu’il prenne en charge d’autres types de déclencheurs et liaisons, consultez la section [Configurer le projet pour un développement local](functions-develop-vs.md#configure-the-project-for-local-development) de [Azure Functions Tools pour Visual Studio](functions-develop-vs.md).
+ Pour en savoir plus sur le test et le débogage local à l’aide d’Azure Functions Core Tools, consultez la page [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md). 
+ Pour en savoir plus sur le développement de fonctions en tant que bibliothèques de classes .NET, consultez [Utilisation de bibliothèques de classes .NET avec Azure Functions](functions-dotnet-class-library.md). 

