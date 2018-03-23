---
title: Création d’une application sans serveur dans Visual Studio | Documents Microsoft
description: Prenez en main votre première application sans serveur avec ce guide sur la création, le déploiement et la gestion de l’application dans Visual Studio.
keywords: ''
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: d3cd509a0073e45bf910e9a74a279d678ff03b63
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Création d’une application dans Visual Studio à l’aide de Logic Apps et Functions

Les outils et fonctionnalités sans serveur dans Azure permettent un développement rapide et le déploiement d’applications cloud.  Ce document se concentre sur la prise en main de la création d’une application sans serveur dans Visual Studio.  [Vous trouverez dans cet article](logic-apps-serverless-overview.md) une vue d’ensemble de la création d’une application sans serveur dans Azure.

## <a name="getting-everything-ready"></a>Tout préparer

Voici les conditions préalables nécessaires à la création d’une application sans serveur à partir de Visual Studio :

* [Visual Studio 2017](https://www.visualstudio.com/vs/) ou Visual Studio 2015 : Communauté, Professionnel ou Entreprise
* [Outils Logic Apps pour Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) pour déboguer les fonctions localement
* Accès au web lors de l’utilisation du concepteur d’application logique intégré

## <a name="getting-started-with-a-deployment-template"></a>Prise en main avec un modèle de déploiement

La gestion des ressources dans Azure est effectuée au sein d’un groupe de ressources.  Un groupe de ressources est un regroupement logique de ressources.  Les groupes de ressources permettent le déploiement et la gestion d’une collecte de ressources.  Pour une application sans serveur dans Azure, notre groupe de ressources comprend Azure Logic Apps et Azure Functions.  Avec le projet de groupe de ressources dans Visual Studio, nous pouvons développer, gérer et déployer l’application complète en tant que ressource unique.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Création d’un projet de groupe de ressources dans Visual Studio

1. Dans Visual Studio, cliquez sur l’option **Nouveau projet** pour en ajouter un.
1. Dans la catégorie **Cloud**, sélectionnez l’option permettant de créer un projet **Groupe de ressources** Azure.  
 * Si la catégorie ou le projet n’apparaissent pas dans la liste, assurez-vous que le Kit de développement logiciel Azure est installé pour Visual Studio.
1. Nommez le projet et choisissez un emplacement, puis sélectionnez **Ok** afin de créer des invites Visual Studio pour sélectionner un modèle.  Vous pouvez sélectionner l’option permettant de démarrer à partir de l’espace vide, démarrer avec une application logique ou une autre ressource.  Toutefois, dans ce cas nous utilisons un modèle de démarrage rapide Azure pour commencer avec une application sans serveur.
1. Sélectionnez l’option pour afficher les modèles de **Démarrage rapide Azure** ![Sélectionner les modèles de démarrage rapide Azure][1]
1. Sélectionnez le modèle de démarrage rapide sans serveur : **101-logic-app-and-function-app** et cliquez sur **Ok**

Le modèle de démarrage rapide crée un modèle de déploiement dans votre projet de groupe de ressources.  Le modèle comprend une application logique simple qui appelle une fonction Azure et renvoie le résultat.  Si vous ouvrez le fichier `azuredeploy.json` dans l’explorateur de solutions, vous pouvez voir les ressources de l’application sans serveur.

## <a name="deploying-the-serverless-application"></a>Déploiement de l’application sans serveur

Pour pouvoir ouvrir le concepteur visuel d’application logique dans Visual Studio, un groupe de ressources Azure doit être pré-déployé.  Le concepteur peut ainsi créer et utiliser des connexions aux services et aux ressources dans l’application logique.  Pour commencer, nous devons simplement déployer la solution créée.

1. Cliquez avec le bouton droit de la souris sur le projet dans Visual Studio, sélectionnez **Déployer** et créez un **nouveau** déploiement. ![Sélectionner un nouveau déploiement de ressources][2]
1. Sélectionnez un abonnement Azure et un groupe de ressources valides
1. Sélectionnez l’option permettant de **Déployer** la solution.
1. Entrez le nom de l’application logique et de l’application Azure Function.  Le nom de la fonction Azure Function doit être unique à l’échelle mondiale.

La solution sans serveur est déployée dans le groupe de ressources spécifié.  Si vous examinez la **sortie** dans Visual Studio, vous pouvez voir l’état du déploiement.

## <a name="editing-the-logic-app-in-visual-studio"></a>Modification de l’application logique dans Visual Studio

Une fois que la solution a été déployée dans un groupe de ressources, le concepteur visuel peut modifier et apporter des modifications à l’application logique.

1. Avec le bouton droit de la souris, cliquez sur le fichier `azuredeploy.json` dans l’explorateur de solutions et sélectionnez **Ouvrir avec le concepteur de Logic Apps**.
1. Sélectionnez le **groupe de ressources** et l’**emplacement** dans lesquels la solution a été déployée, puis cliquez sur **OK**.

Le concepteur visuel d’application logique doit désormais être visible dans Visual Studio.  Vous pouvez continuer à ajouter des étapes, modifier le flux de travail et enregistrer les modifications.  Vous pouvez également créer des applications logiques à partir de Visual Studio.  Si vous cliquez avec le bouton droit de la souris sur les **Ressources** dans le navigateur de modèle, vous pouvez choisir d’ajouter une **application logique** au projet.  Les applications logiques vides sont chargées dans le concepteur visuel sans avoir été pré-déployées dans un groupe de ressources.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Gestion et affichage de l’historique d’exécution d’une application logique déployée

Vous pouvez également gérer et afficher l’historique d’exécution des applications logiques déployées dans Azure.  Si vous ouvrez l’outil **Cloud Explorer** dans Visual Studio, vous pouvez cliquer avec le bouton droit de la souris sur n’importe quelle application logique et choisir de modifier, désactiver, afficher les propriétés ou afficher l’historique d’exécution.  En cliquant sur Modifier, vous pouvez également télécharger une application logique publiée dans un projet de groupe de ressources Visual Studio.  Ainsi, même si vous avez démarré la création de votre application logique dans le portail Azure, vous pouvez toujours l’importer et la gérer dans Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Développement d’une fonction Azure Function dans Visual Studio

Le modèle de déploiement déploie toutes les fonctions Azure Function de la solution pour le référentiel git indiqué dans les variables `azuredeploy.json`.  Si vous créez un projet de fonction au sein de la solution, archivez-le dans le contrôle de code source (GitHub, Visual Studio Team Services, etc.) et mettez à jour la variable `repo` ; le modèle déploiera la fonction Azure Function.

### <a name="creating-an-azure-function-project"></a>Création d’un projet Azure Function

Si vous utilisez JavaScript, Python, F#, Bash, Batch ou PowerShell, suivez les [étapes qui se trouvent dans l’interface CLI de fonctions](../azure-functions/functions-run-local.md) pour créer un projet.  Si vous développez une fonction dans C#, vous pouvez utiliser une [bibliothèque de classes C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) dans la solution actuelle pour la fonction Azure Function.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir comment créer un tableau de bord social sans serveur](logic-apps-scenario-social-serverless.md)
* [Gestion d’une application logique à partir de Visual Studio Cloud Explorer](manage-logic-apps-with-visual-studio.md)
* [Langage de définition de flux de travail d’application logique](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
