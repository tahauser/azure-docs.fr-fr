---
title: Automatiser des tâches et des processus avec Visual Studio et Azure Logic Apps | Microsoft Docs
description: Ce démarrage rapide montre comment créer des workflows qui automatisent des tâches et des processus avec Azure Logic Apps dans Visual Studio
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 02e19de97654d751dc0cd557791a61a863a9a4e0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-automate-tasks-and-processes-with-azure-logic-apps---visual-studio"></a>Démarrage rapide : Automatiser des tâches et des processus avec Azure Logic Apps - Visual Studio

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vous pouvez créer des workflows qui automatisent les tâches et les processus d’intégration des applications, des données, des systèmes et des services dans les entreprises et organisations. Ce démarrage rapide montre comment vous pouvez concevoir et générer ces workflows en créant des applications logiques dans Visual Studio et en déployant ces applications sur <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> dans le cloud. Et bien que vous puissiez effectuer ces tâches dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, Visual Studio vous permet d’ajouter des applications logiques pour le contrôle de code source, la publication de différentes versions et la création de modèles Azure Resource Manager pour divers environnements de déploiement. 

Si vous êtes débutant avec Azure Logic Apps et si vous souhaitez seulement connaître les concepts de base, essayez plutôt le [démarrage rapide pour créer une application logique dans le portail Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md). Le Concepteur d’application logique dans le portail Azure et dans Visual Studio fonctionne de façon similaire. 

Ici, vous créez la même application logique que dans le démarrage rapide de portail Azure, mais avec Visual Studio. Cette application logique surveille les flux RSS d’un site web et envoie un e-mail chaque fois qu’un élément est publié sur le site. Lorsque vous avez terminé, votre application logique ressemble au workflow de niveau élevé suivant :

![Application logique terminée](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

Avant de commencer, vérifiez que vous disposez des éléments ci-après :

* Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Téléchargez et installez ces outils, si vous ne les avez pas déjà : 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 ou Visual Studio 2015 - édition Communauté ou supérieure</a>. 
  Ce démarrage rapide utilise Visual Studio Community 2017, qui est gratuit.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Kit de développement logiciel (SDK) Azure (2.9.1 ou version ultérieure)</a> et <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Outils Azure Logic Apps pour Visual Studio 2017</a> ou <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a>
  
    Vous pouvez télécharger et installer les outils Azure Logic Apps directement à partir de Visual Studio Marketplace ou en apprendre davantage sur <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">l’installation de cette extension dans Visual Studio</a>. 
    Veillez à redémarrer Visual Studio après l’installation.

* Un compte de messagerie pris en charge par Logic Apps, par exemple Office 365 Outlook, Outlook.com ou Gmail. Pour les autres fournisseurs, <a href="https://docs.microsoft.com/connectors/" target="_blank">passez en revue la liste des connecteurs ici</a>. Cette application logique utilise Office 365 Outlook. Si vous utilisez un autre fournisseur, les étapes générales sont identiques, mais votre interface utilisateur peut être légèrement différente.

* Accès au web lors de l’utilisation du Concepteur d’application logique intégré

  Le Concepteur requiert une connexion Internet pour créer des ressources dans Azure et pour lire les propriétés et les données à partir de connecteurs dans votre application logique. 
  Par exemple, si vous utilisez le connecteur Dynamics CRM Online, le Concepteur recherche les propriétés par défaut et personnalisées disponibles dans votre instance CRM.

## <a name="create-azure-resource-group-project"></a>Créer un projet de groupe de ressources Azure

Pour commencer, créez un [projet de groupe de ressources Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). En savoir plus sur [les ressources et groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).

1. Démarrez Visual Studio et connectez-vous avec votre compte Azure.

2. Dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**. (Clavier : Ctrl + Maj + N)

   ![Dans le menu « Fichier », sélectionnez « Nouveau » > « Projet »](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

3. Sous **Installé**, sélectionnez **Visual C#** ou **Visual Basic**. Sélectionnez **Cloud** > **Groupe de ressources Azure**. Nommez votre projet, par exemple :

   ![Créer un projet de groupe de ressources Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

4. Sélectionnez le modèle **Application logique**. 

   ![Sélectionner le modèle Application logique](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Une fois votre projet créé par Visual Studio, l’Explorateur de solutions s’ouvre et affiche votre solution. 

   ![L’Explorateur de solutions affiche la nouvelle solution d’application logique et le fichier de déploiement](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

   Dans votre solution, le fichier **LogicApp.json** ne stocke pas seulement la définition de votre application logique, mais est également un modèle Azure Resource Manager que vous pouvez configurer pour le déploiement.

## <a name="create-blank-logic-app"></a>Créer une application logique vide

Après avoir créé votre projet de groupe de ressources Azure, créez et générez votre application logique à partir du modèle **Application logique vide**.

1. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour le fichier **LogicApp.json**. Sélectionnez **Open With Logic App Designer** (Ouvrir avec le Concepteur d’application logique). (Clavier : Ctrl + L)

   ![Ouvrir le fichier .json d’application logique avec le Concepteur d’application logique](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

2. Dans **Abonnement**, sélectionnez l’abonnement Azure que vous voulez utiliser. Pour **Groupe de ressources**, sélectionnez **Créer nouveau...**, afin de créer un groupe de ressources Azure. 

   ![Sélectionner un abonnement Azure, un groupe de ressources et un emplacement de ressource](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   Visual Studio a besoin de votre abonnement Azure et d’un groupe de ressources pour créer et déployer des ressources associées à votre application logique et à vos connexions. 

   | Paramètre | Exemple de valeur | Description | 
   | ------- | ------------- | ----------- | 
   | Liste de profils utilisateur | Contoso <br> jamalhartnett@contoso.com | Par défaut, le compte que vous avez utilisé pour vous connecter | 
   | **Abonnement** | Pay-As-You-Go <br> (jamalhartnett@contoso.com) | Le nom de votre abonnement Azure et le compte associé |
   | **Groupe de ressources** | MyLogicApp-RG <br> (Ouest des États-Unis) | Le groupe de ressources Azure et l’emplacement de stockage et de déploiement des ressources pour votre application logique | 
   | **Lieu** | MyLogicApp-RG2 <br> (Ouest des États-Unis) | Un autre emplacement si vous ne souhaitez pas utiliser l’emplacement du groupe de ressources |
   ||||

3. Le Concepteur d’application logique s’ouvre et affiche une page contenant une vidéo de présentation et les déclencheurs couramment utilisés. Faites défiler la vidéo et les déclencheurs. Sous **Modèles**, sélectionnez **Application logique vide**.

   ![Sélectionner « Application logique vide »](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Générer un workflow d’application logique

Ensuite, ajoutez un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui s’active lorsqu’un nouvel élément de flux RSS apparaît. Chaque application logique doit commencer avec un déclencheur, qui s’active quand des critères spécifiques sont remplis. Chaque fois que le déclencheur est activé, le moteur Logic Apps crée une instance d’application logique qui exécute votre workflow.

1. Dans le Concepteur d’application logique, entrez « rss » dans la zone de recherche. Sélectionnez le déclencheur suivant : **RSS - Lors de la publication d’un élément de flux**

   ![Générer votre application logique en ajoutant un déclencheur et des actions](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

   Le déclencheur s’affiche désormais dans le Concepteur :

   ![Le déclencheur RSS s’affiche dans le Concepteur d’application logique](./media/quickstart-create-logic-apps-with-visual-studio/rss-trigger-logic-app.png)

2. Pour terminer de générer l’application logique, suivez les étapes du workflow dans le [démarrage rapide du portail Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), puis revenez à cet article.

   Une fois que vous avez terminé, votre application logique ressemble à cet exemple : 

   ![Application logique terminée](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

3. Pour enregistrer votre application logique, enregistrez votre solution Visual Studio. (Clavier : Ctrl + S)

Maintenant, avant de pouvoir tester votre application logique, déployez votre application dans Azure.

## <a name="deploy-logic-app-to-azure"></a>Déployer l’application logique dans Azure

Avant de pouvoir exécuter votre application logique, déployez l’application à partir de Visual Studio dans Azure ; pour ce faire, seules quelques étapes doivent être effectuées.

1. Dans l’Explorateur de solutions, dans le menu contextuel de votre projet, sélectionnez **Déployer** > **Nouveau...**. Si vous y êtes invité, connectez-vous à votre compte Azure.

   ![Créer le déploiement de l’application logique](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

2. Pour ce déploiement, gardez l’abonnement Azure, le groupe de ressources et les autres paramètres par défaut. Quand vous êtes prêt, choisissez **Déployer**. 

   ![Déployer l’application logique dans un groupe de ressources Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

3. Si le champ **Modifier les paramètres** s’affiche, indiquez le nom de la ressource pour l’application logique à utiliser au moment du déploiement, puis enregistrez vos paramètres, par exemple :

   ![Indiquer le nom du déploiement de l’application logique](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Lorsque le déploiement commence, l’état du déploiement de votre application s’affiche dans la fenêtre **Sortie** de Visual Studio. 
   Si l’état n’apparaît pas, ouvrez la liste **Afficher la sortie à partir de** et sélectionnez votre groupe de ressources Azure.

   ![Sortie d’état du déploiement](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Une fois le déploiement terminé, votre application logique est en ligne dans le portail Azure et vérifie le flux RSS en fonction de la planification que vous avez spécifiée (chaque minute). 
   Si le flux RSS a de nouveaux éléments, votre application logique envoie un e-mail pour chaque nouvel élément. 
   Dans le cas contraire, votre application logique attend jusqu’à l’intervalle suivant avant de procéder à une nouvelle vérification. 

   Par exemple, voici des e-mails classiques envoyés par cette application logique. 
   Si vous ne recevez aucun e-mail, vérifiez votre dossier Courrier indésirable. 

   ![Outlook envoie un e-mail pour chaque nouvel élément RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

   Techniquement, lorsque le déclencheur vérifie le flux RSS et trouve de nouveaux éléments, le déclencheur s’active et le moteur Logic Apps crée une instance de workflow d’application logique qui exécute les actions dans le workflow.
   Si le déclencheur ne trouve pas de nouveaux éléments, il ne s’active pas et « ignore » l’instanciation du workflow.

Félicitations, vous avez maintenant correctement généré et déployé votre application logique avec Visual Studio ! Pour gérer votre application logique et examiner son historique des exécutions, consultez [Manage logic apps with Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) (Gérer des applications logiques avec Visual Studio).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources qui contient votre application logique et les ressources associées.

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a> avec le même compte que celui utilisé pour créer votre application logique. 

2. Dans le menu Azure principal, choisissez **Groupes de ressources**. Sélectionnez le groupe de ressources pour votre application logique.

3. Choisissez **Supprimer un groupe de ressources**. Confirmez le nom du groupe de ressources, puis choisissez **Supprimer**.

   ![« Groupes de ressources » > « Vue d’ensemble » > « Supprimer un groupe de ressources »](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

4. Supprimez la solution Visual Studio de votre ordinateur local.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum Azure Logic Apps</a>.
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le <a href="http://aka.ms/logicapps-wish" target="_blank">site de commentaires des utilisateurs Logic Apps</a>.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez généré, déployé et exécuté votre application logique avec Visual Studio. Pour en savoir plus sur la gestion et l’exécution du déploiement avancé des applications logiques avec Visual Studio, consultez les articles suivants :

> [!div class="nextstepaction"]
> [Gérer des applications logiques avec Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)