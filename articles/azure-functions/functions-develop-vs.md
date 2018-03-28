---
title: Développer Azure Functions à l’aide de Visual Studio | Microsoft Docs
description: Apprenez à développer et à tester Azure Functions à l’aide d’Azure Functions Tools pour Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: glenga
ms.openlocfilehash: dddb35ea2ba1c02f78234fe33cdb832e9aacbff5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-functions-tools-for-visual-studio"></a>Azure Functions Tools pour Visual Studio  

Azure Functions Tools pour Visual Studio 2017 est une extension pour Visual Studio qui vous permet de développer, de tester et de déployer des fonctions C# sur Azure. S’il s’agit de votre première expérience avec Azure Functions, vous pouvez en apprendre davantage dans l’article [Présentation d’Azure Functions](functions-overview.md).

Azure Functions Tools propose les avantages suivants : 

* Modifier, générer et exécuter des fonctions sur votre ordinateur de développement local. 
* Publier votre projet Azure Functions directement sur Azure. 
* Utiliser des attributs WebJobs pour déclarer des liaisons de fonction directement dans le code C# au lieu de maintenir une fonction.json distincte pour les définitions de liaison.
* Développer et déployer des fonctions précompilées C#. Les fonctions précompilées offrent de meilleures performances de démarrage à froid que les fonctions basées sur un script C#. 
* Coder vos fonctions en C# tout en bénéficiant de tous les avantages du développement Visual Studio. 

Cette rubrique vous montre comment utiliser Azure Functions Tools pour Visual Studio 2017 afin de développer vos fonctions en C#. Vous apprenez également à publier votre projet sur Azure en tant qu’assembly .NET.

> [!IMPORTANT]
> Ne mélangez pas un développement local avec un développement de portail dans une même application de fonction. Quand vous publiez à partir d’un projet local dans une application de fonction, le processus de déploiement remplace toutes les fonctions que vous avez développées dans le portail.

## <a name="prerequisites"></a>Prérequis


Azure Functions Tools est inclus dans la charge de travail de développement Azure de [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/) et des versions ultérieures. Veillez à inclure la charge de travail de **développement Azure** lorsque vous installez Visual Studio 2017 :

![Installer Visual Studio 2017 avec la charge de travail de développement Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Vérifiez que Visual Studio est à jour et que vous utilisez la [dernière version](#check-your-tools-version) d’Azure Functions Tools.

### <a name="other-requirements"></a>Autres exigences

Pour créer et déployer des fonctions, vous avez également besoin des éléments suivants :

* Un abonnement Azure actif. Si tel n’est pas le cas, des [comptes gratuits](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) sont disponibles.

* Un compte de stockage Azure. Pour créer un compte de stockage, consultez [Créez un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Vérifier la version des outils

1. Dans le menu **Outils**, choisissez **Extensions et mises à jour**. Développez **Installé** > **Outils** et choisissez **Outils Azure Functions et Web Jobs**.

    ![Vérifier la version des outils Functions](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Notez la **Version** installée. Vous pouvez comparer cette version avec la dernière, indiquée [dans les notes de publication](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Si votre version est antérieure, mettez à jour vos outils dans Visual Studio en suivant les instructions de la section suivante.

### <a name="update-your-tools"></a>Mettre à jour les outils

1. Dans la boîte de dialogue **Extensions et mises à jour**, développez **Mises à jour** > **Visual Studio Marketplace**, choisissez **Outils Azure Functions et Web Jobs** et sélectionnez **Mettre à jour**.

    ![Mettre à jour la version des outils Functions](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Une fois la mise à jour des outils téléchargée, fermez Visual Studio pour déclencher la mise à jour des outils à l’aide du programme d’installation VSIX.

3. Dans le programme d’installation, choisissez **OK** pour démarrer, puis **Modifier** pour mettre à jour les outils. 

4. Une fois la mise à jour terminée, choisissez **Fermer** et redémarrez Visual Studio.

## <a name="create-an-azure-functions-project"></a>Créer un projet Azure Functions 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Le modèle de projet crée un projet C#, installe le package NuGet `Microsoft.NET.Sdk.Functions` et définit le framework cible. Functions 1.x cible le .NET Framework, et Functions 2.x cible .NET Standard. Le nouveau projet comporte les fichiers suivants :

* **host.json** : vous permet de configurer l’hôte Functions. Ces paramètres s’appliquent lors de l’exécution en local et dans Azure. Pour plus d’informations, consultez l’article de référence sur [host.json](functions-host-json.md).
    
* **local.settings.json** : maintient les paramètres utilisés lors de l’exécution des fonction en local. Ces paramètres ne sont pas utilisés par Azure, ils sont utilisés par [Azure Functions Core Tools](functions-run-local.md). Utilisez ce fichier pour spécifier des paramètres, tels que des chaînes de connexion vers d’autres services Azure. Ajoutez une clé au tableau **Valeurs** pour chaque connexion requise par les fonctions dans votre projet. Pour plus d’informations, consultez [Local settings file](functions-run-local.md#local-settings-file) (Fichier de paramètres local) dans la rubrique Procédure locale de codage et de test d’Azure Functions.

Pour plus d’informations, consultez [Projet de bibliothèque de classes Azure Functions](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Configurer le projet pour un développement local

Le runtime de Functions utilise un compte de stockage Azure en interne. Pour tous les types de déclencheur autres que HTTP et webhooks, vous devez définir la clé **Values.AzureWebJobsStorage** sur une chaîne de connexion de compte de stockage Azure valide. 

[!INCLUDE [Note on local storage](../../includes/functions-local-settings-note.md)]

 Pour définir la chaîne de connexion de compte de stockage :

1. Dans Visual Studio, ouvrez **Cloud Explorer**, développez **Compte de stockage** > **Votre compte de stockage**, puis sélectionnez **Propriétés** et copiez la valeur **Chaîne de connexion principale**.   

2. Dans votre projet, ouvrez le fichier local.settings.json et définissez la valeur de la clé **AzureWebJobsStorage** sur la chaîne de connexion que vous avez copiée.

3. Répétez l’étape précédente pour ajouter des clés uniques au tableau **Valeurs** pour les autres connexions requises par vos fonctions.  

## <a name="create-a-function"></a>Créer une fonction

Dans les fonctions précompilées, les liaisons utilisées par la fonction sont définies en appliquant des attributs dans le code. Lorsque vous utilisez Azure Functions Tools pour créer vos fonctions à partir des modèles fournis, ces attributs sont appliqués pour vous. 

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nœud de projet et sélectionnez **Ajouter** > **Nouvel élément**. Sélectionnez **Fonction Azure**, tapez un **nom** pour la classe, puis cliquez sur **Ajouter**.

2. Choisissez votre déclencheur, définissez les propriétés de liaison, puis cliquez sur **Créer**. L’exemple suivant montre les paramètres lors de la création d’une fonction déclenchée par le stockage File d’attente. 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    Cet exemple de déclencheur utilise une chaîne de connexion avec une clé nommée **QueueStorage**. Ce paramètre de chaîne de connexion doit être défini dans le fichier local.settings.json. 
 
3. Examinez la classe qui vient d’être ajoutée. Vous voyez une méthode statique **Run**, qui est attribuée avec l’attribut **FunctionName**. Cet attribut indique que la méthode est le point d’entrée de la fonction. 

    Par exemple, la classe C# suivante représente une fonction de stockage déclenchée par le stockage File d’attente :

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    Un attribut spécifique à la liaison est appliqué à chaque paramètre de liaison fourni à la méthode de point d’entrée. L’attribut accepte les informations de liaison en tant que paramètres. Dans l’exemple précédent, un attribut **QueueTrigger** est appliqué au premier paramètre, indiquant ainsi la fonction déclenchée par la file d’attente. Le nom de la file d’attente et le nom du paramètre de la chaîne de connexion sont transmis en tant que paramètres à l’attribut **QueueTrigger**.

## <a name="testing-functions"></a>Tester les fonctions

Azure Functions Core Tools vous permet d’exécuter un projet Azure Functions sur votre ordinateur de développement local. Vous êtes invité à installer ces outils la première fois que vous démarrez une fonction dans Visual Studio.  

Pour tester votre fonction, appuyez sur F5. Si vous y êtes invité, acceptez la requête dans Visual Studio pour télécharger et installer Azure Functions Core (CLI) Tools. Vous devrez peut-être activer une exception de pare-feu afin de permettre aux outils de prendre en charge les requêtes HTTP.

Avec le projet en cours d’exécution, vous pouvez tester votre code comme vous testeriez la fonction déployée. Pour plus d’informations, consultez [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md). Lors de l’exécution en mode débogage, les points d’arrêt sont atteints dans Visual Studio comme prévu. 

Pour obtenir un exemple montrant comment tester une fonction déclenchée par une file d’attente, consultez le [didacticiel de démarrage rapide sur une fonction déclenchée par une file d’attente](functions-create-storage-queue-triggered-function.md#test-the-function).  

Pour en savoir plus sur l’utilisation d’Azure Functions Core Tools, consultez [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md).

## <a name="publish-to-azure"></a>Publication dans Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Paramètres Function App   

Les paramètres que vous avez ajoutés au fichier local.settings.json doivent être également ajoutés à l’application de fonction dans Azure. Ces paramètres ne sont pas chargés automatiquement quand vous publiez le projet. 

Le moyen le plus simple de charger les paramètres obligatoires sur votre application de fonctions dans Azure consiste à utiliser le lien **Gérer les paramètres d’application...** qui apparaît une fois votre projet correctement publié. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Vous accédez ainsi à la boîte de dialogue **Paramètres de l’application** de l’application de fonctions, où vous pouvez ajouter de nouveaux paramètres d’application ou modifier des paramètres existants.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Vous pouvez également gérer les paramètres d’application d’une des manières suivantes :

* [Utilisation du portail Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Utilisation de l’option de publication `--publish-local-settings` dans Azure Functions Core Tools](functions-run-local.md#publish).
* [Utilisation de l’interface CLI Azure](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Functions Tools, consultez la section Common Questions (Questions courantes) de l’article de blog [Visual Studio 2017 Tools for Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) (Visual Studio 2017 Tools pour Azure Functions).

Pour en savoir plus sur Azure Functions Core Tools, consultez [Procédure locale de codage et de test d’Azure Functions](functions-run-local.md).

Pour en savoir plus sur le développement de fonctions en tant que bibliothèques de classes, consultez [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md). Cette rubrique fournit également des liens vers des exemples d’utilisation d’attributs pour déclarer les différents types de liaisons pris en charge par Azure Functions.    
