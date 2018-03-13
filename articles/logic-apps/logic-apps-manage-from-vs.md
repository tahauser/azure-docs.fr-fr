---
title: "Gestion d’applications logiques dans Visual Studio - Azure Logic Apps | Microsoft Docs"
description: "Gestion d’applications logiques et d’autres ressources à l’aide de Visual Studio Cloud Explorer"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: 34ce65ec272c9fe5be8f95371179de092c5df2f3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gestion de vos applications logiques à l’aide de Visual Studio Cloud Explorer

Bien que le [Portail Azure](https://portal.azure.com/) vous offre un excellent moyen de concevoir et de gérer des applications logiques Azure, vous pouvez utiliser Visual Studio Cloud Explorer pour gérer un grand nombre de vos ressources Azure, y compris les applications logiques. Visual Studio Cloud Explorer vous permet de parcourir, de gérer, de modifier et de télécharger des applications logiques publiées. Les tâches de gestion incluent notamment l’activation, la désactivation et l’affichage de l’historique des exécutions. 

Avant de pouvoir accéder à vos applications logiques et les gérer dans Visual Studio, installez et configurez ces outils Visual Studio pour Azure Logic Apps. 

## <a name="prerequisites"></a>configuration requise

* [Visual Studio 2015 ou Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Dernier kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou supérieur)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accès au web lors de l’utilisation du concepteur intégré

## <a name="install-visual-studio-tools-for-logic-apps"></a>Installer les outils Visual Studio pour Logic Apps

Après avoir installé les prérequis, téléchargez et installez les outils Azure Logic Apps pour Visual Studio.

1. Ouvrez Visual Studio. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour**.
2. Développez la catégorie **En ligne** pour effectuer une recherche en ligne dans la galerie Visual Studio.
3. Parcourez les résultats ou recherchez **Logic Apps** jusqu’à ce que vous trouviez **Outils Azure Logic Apps pour Visual Studio**.
4. Pour télécharger et installer l’extension, cliquez sur **Télécharger**.
5. Redémarrez Visual Studio après l’installation.

> [!NOTE]
> Pour télécharger directement les outils Azure Logic Apps pour Visual Studio, accédez à [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Recherche d’applications logiques dans Cloud Explorer

1.  Pour ouvrir Cloud Explorer, sur le menu **Affichage**, choisissez **Cloud Explorer**.
2.  Recherchez votre application logique, par groupe de ressources ou par type de ressource. 

    * Si vous les parcourez par type de ressource, sélectionnez un abonnement Azure, développez la section **Logic Apps**, puis sélectionnez une application logique. 
    * Si vous y accédez par groupe de ressources, développez le groupe de ressources qui contient votre application logique, puis sélectionnez votre application logique.

    Pour afficher les commandes de votre application logique, cliquez avec le bouton droit sur votre application logique, ou en bas de Cloud Explorer, choisissez le menu **Actions**.

    ![Accès à votre application logique](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Modifier votre application logique avec le Concepteur d’applications logiques

Dans Cloud Explorer, vous pouvez ouvrir une application logique actuellement déployée dans le même concepteur que celui que vous utilisez dans le portail Azure. 

* Pour modifier votre application logique, dans Cloud Explorer, cliquez avec le bouton droit sur votre application logique, puis sélectionnez **Ouvrir avec l'éditeur d'application logique**. 

* Pour publier vos mises à jour dans le cloud, choisissez **Publier**. 

* Pour démarrer une nouvelle exécution, choisissez **Déclencheur d'exécution**.

![Concepteur Logic Apps](./media/logic-apps-manage-from-vs/designer.png)

Dans le concepteur, vous pouvez également **Télécharger** une application logique. Cette action paramètre automatiquement la définition de l’application logique et l’enregistre sous la forme d’un modèle de déploiement Azure Resource Manager. Vous pouvez ajouter ce modèle de déploiement à votre projet de groupe de ressources Azure.

## <a name="browse-your-logic-app-run-history"></a>Parcourir votre historique des exécutions d’une application logique

Pour afficher l’historique des exécutions de votre application logique, cliquez avec le bouton droit sur votre application logique et sélectionnez **Ouvrir l’historique des exécutions**. Pour réorganiser votre historique des exécutions en fonction de l’une des propriétés affichées, sélectionnez l’en-tête de colonne.

![Historique d’exécution](media/logic-apps-manage-from-vs/runs.png)

Pour afficher l’historique des exécutions d’une instance et voir les résultats de l’exécution, notamment les entrées et sorties de chaque étape, double-cliquez sur l’une des instances d’exécution.

![Exécution des résultats de l’historique, des entrées et des sorties](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créez votre première application logique](quickstart-create-first-logic-app-workflow.md)
* [Concevoir, créer et déployer des applications logiques dans Visual Studio](logic-apps-deploy-from-vs.md)
* [Afficher des exemples et des scénarios courants](logic-apps-examples-and-scenarios.md)
* [Vidéo : Automatisez vos processus métiers avec Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694)
* [Vidéo : Intégrez vos systèmes à Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
