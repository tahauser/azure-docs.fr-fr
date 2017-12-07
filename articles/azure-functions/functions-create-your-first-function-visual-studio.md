---
title: "Créer votre première fonction dans Azure à l’aide de Visual Studio | Microsoft Docs"
description: "Créez et publiez une fonction HTTP déclenchée simple vers Azure à l’aide d’Azure Functions Tools pour Visual Studio 2017."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, functions, traitement des événements, calcul, architecture sans serveur"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/1/2017
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 7f71ecb2b58728f466371c7aa6d2aac965177863
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="create-your-first-function-using-visual-studio"></a>Créer votre première fonction à l’aide de Visual Studio

Azure Functions vous permet d’exécuter votre code dans un environnement [sans serveur](https://azure.microsoft.com/overview/serverless-computing/) et sans avoir à créer une machine virtuelle ou à publier une application web au préalable.

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

Dans cette rubrique, vous allez apprendre à vous servir des outils Visual Studio 2017 pour Azure Functions afin de créer et tester en local une fonction « Hello World ». Vous allez ensuite publier le code de la fonction vers Azure. Ces outils sont disponibles dans le cadre de la charge de travail de développement Azure dans Visual Studio 2017version 15.3 ou ultérieure.

![Code Azure Functions dans un projet Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, installez le logiciel suivant :

* [Visual Studio 2017 version 15.4](https://www.visualstudio.com/vs/) ou version ultérieure, y compris la charge de travail de **développement Azure**.

    ![Installer Visual Studio 2017 avec la charge de travail de développement Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)
    
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Créer un projet Azure Functions dans Visual Studio

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Maintenant que vous avez créé le projet, vous pouvez créer votre première fonction.

## <a name="create-the-function"></a>Création de la fonction

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nœud de projet et sélectionnez **Ajouter** > **Nouvel élément**. Sélectionnez **Azure Function**, saisissez `HttpTriggerCSharp.cs` dans le champ **Nom** et cliquez sur **Ajouter**.

2. Sélectionnez **HttpTrigger**, sélectionnez **Anonyme** pour les **Droits d’accès**, puis cliquez sur **OK**. La fonction créée est accessible par une requête HTTP à partir de n’importe quel client. 

    ![Créer une fonction Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

    Un fichier de code est ajouté au projet qui contient une classe implémentant votre code de fonction. Ce code est basé sur un modèle, qui reçoit une valeur de nom, puis la renvoie. L’attribut **FunctionName** définit le nom de votre fonction. L’attribut **HttpTrigger** indique le message qui déclenche la fonction. 

    ![Fichier du code de fonction](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Maintenant que vous avez créé une fonction HTTP déclenchée, vous pouvez la tester sur votre ordinateur local.

## <a name="test-the-function-locally"></a>Tester la fonction en local

Azure Functions Core Tools vous permet d’exécuter un projet Azure Functions sur votre ordinateur de développement local. Vous êtes invité à installer ces outils la première fois que vous démarrez une fonction dans Visual Studio.  

1. Pour tester votre fonction, appuyez sur F5. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer Azure Functions Core (CLI) Tools.  Vous devrez peut-être activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

2. Copiez l’URL de votre fonction à partir de la sortie runtime Azure Functions.  

    ![Azure runtime local](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Collez l’URL de la demande HTTP dans la barre d’adresses de votre navigateur. Ajoutez la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande locale GET : 

    ![Réponse de la fonction localhost dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Pour arrêter le débogage, cliquez sur le bouton **Arrêter** dans la barre d’outils de Visual Studio.

Après avoir vérifié que la fonction s’exécute correctement sur votre ordinateur local, il est temps de publier le projet sur Azure.

## <a name="publish-the-project-to-azure"></a>Publication du projet sur Azure

Vous devez disposer d’une application de fonction dans votre abonnement Azure avant de pouvoir publier votre projet. Vous pouvez créer une application de fonction directement à partir de Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Tester votre fonction dans Azure

1. Copiez l’URL de base de l’application de fonction à partir de la page de profil de publication. Remplacez la partie `localhost:port` de l’URL que vous avez utilisée lors du test en local de la fonction par la nouvelle URL de base. Comme auparavant, assurez-vous d’ajouter la chaîne de requête `?name=<yourname>` à cette URL et exécutez la demande.

    L’URL qui appelle votre fonction HTTP déclenchée ressemble à cela :

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Collez cette nouvelle URL de requête HTTP dans la barre d’adresse de votre navigateur. La capture d’écran suivante du navigateur montre la requête renvoyée par la fonction suite à la demande distante GET : 

    ![Réponse de la fonction dans le navigateur](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>Étapes suivantes

Vous avez utilisé Visual Studio pour créer une application de fonction C# à l’aide d’une fonction HTTP déclenchée simple. 

+ Pour savoir comment configurer votre projet de façon qu’il prenne en charge d’autres types de déclencheurs et liaisons, consultez la section [Configurer le projet pour un développement local](functions-develop-vs.md#configure-the-project-for-local-development) de [Azure Functions Tools pour Visual Studio](functions-develop-vs.md).
+ Pour en savoir plus sur le test et le débogage local à l’aide d’Azure Functions Core Tools, consultez la page [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md). 
+ Pour en savoir plus sur le développement de fonctions en tant que bibliothèques de classes .NET, consultez [Utilisation de bibliothèques de classes .NET avec Azure Functions](functions-dotnet-class-library.md). 

