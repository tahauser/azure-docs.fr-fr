---
title: Gérer des applications logiques avec Visual Studio - Azure Logic Apps | Microsoft Docs
description: Gestion d’applications logiques et d’autres ressources à l’aide de Visual Studio Cloud Explorer
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
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: db847d5fa3d5f5b2b2f0293f1756226870a8b47e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-logic-apps-with-visual-studio"></a>Gérer des applications logiques avec Visual Studio

Même si vous pouvez créer, modifier, gérer et déployer des applications logiques dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, vous pouvez aussi utiliser Visual Studio pour ajouter des applications logiques au contrôle de code source, publier des versions différentes et créer des modèles [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour différents environnements de déploiement. Avec Visual Studio Cloud Explorer, vous pouvez rechercher et gérer vos applications logiques, ainsi que d’autres ressources Azure. Par exemple, vous pouvez ouvrir, télécharger, modifier, exécuter, désactiver et activer des applications logiques déjà déployées dans le portail Azure ou encore en afficher l’historique des exécutions. Si vous ne savez pas utiliser Azure Logic Apps dans Visual Studio, apprenez à [créer des applications logiques avec Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Le déploiement ou la publication d’une application logique à partir de Visual Studio remplace la version de cette application dans le portail Azure. Par conséquent, si vous apportez des modifications dans le portail Azure que vous souhaitez conserver, veillez à [actualiser l’application logique dans Visual Studio](#refresh) à partir du portail Azure avant son prochain déploiement ou sa prochaine publication à partir de Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Prérequis


* Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>.

* Téléchargez et installez ces outils, si vous ne les avez pas déjà : 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 ou Visual Studio 2015 - édition Community ou supérieure</a>. 
  Ce démarrage rapide utilise Visual Studio Community 2017, qui est gratuit.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">SDK Azure (2.9.1 ou version ultérieure)</a> et <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Outils Azure Logic Apps pour Visual Studio 2017</a> ou <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a> 
  
    Vous pouvez télécharger et installer les outils Azure Logic Apps directement à partir de Visual Studio Marketplace ou en apprendre davantage sur <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">l’installation de cette extension dans Visual Studio</a>. 
    Veillez à redémarrer Visual Studio après l’installation.

* Accès au web lors de l’utilisation du Concepteur d’application logique intégré

  Le Concepteur requiert une connexion Internet pour créer des ressources dans Azure et pour lire les propriétés et les données à partir de connecteurs dans votre application logique. 
  Par exemple, si vous utilisez le connecteur Dynamics CRM Online, le Concepteur recherche les propriétés par défaut et personnalisées disponibles dans votre instance CRM.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Trouver vos applications logiques

Dans Visual Studio, vous pouvez trouver toutes les applications logiques associées à votre abonnement Azure et déployées sur le portail Azure à l’aide de Cloud Explorer.

1. Ouvrez Visual Studio. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

2. Dans Cloud Explorer, choisissez **Gestion de compte**. Sélectionnez l’abonnement Azure associé à vos applications logiques, puis choisissez **Appliquer**. Par exemple : 

   ![Choisir « Gestion de compte »](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. Selon que vous effectuez une recherche par **groupe de ressources** ou **type de ressources**, suivez ces étapes :

   * **Groupes de ressources** : Sous votre abonnement Azure, Cloud Explorer montre tous les groupes de ressources associés à cet abonnement. 
   Développez le groupe de ressources qui contient votre application logique, puis sélectionnez cette dernière.

   * **Types de ressources** : Sous votre abonnement Azure, développez **Application logiques**. Une fois que Cloud Explorer montre toutes les applications logiques déployées associées à votre abonnement, sélectionnez la vôtre.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Ouvrir dans Visual Studio

Dans Visual Studio, vous pouvez ouvrir des applications logiques précédemment créées et déployées directement par le biais du portail Azure ou en tant que projets Azure Resource Manager avec Visual Studio.

1. Ouvrez Cloud Explorer, puis recherchez votre application logique. 

2. Dans le menu contextuel de l’application logique, sélectionnez **Ouvrir avec l’éditeur d’application logique**.

   Cet exemple montre les applications logiques par type de ressource, si bien qu’elles s’affichent sous la section **Applications logiques**.

  ![Ouvrir l’application logique déployée à partir du portail Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Une fois que l’application logique s’ouvre dans le Concepteur d’application logique, au bas du Concepteur, vous pouvez choisir **Mode Code** pour examiner la structure de définition d’application logique sous-jacente. 
   Pour créer un modèle de déploiement pour l’application logique, apprenez à [télécharger un modèle Azure Resource Manager](#download-logic-app) pour cette application logique. Découvrez-en plus sur les [modèles Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Télécharger à partir d’Azure

Vous pouvez télécharger des applications logiques à partir du <a href="https://portal.azure.com" target="_blank">portail Azure</a> et les enregistrer en tant que modèles [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Vous pouvez ensuite modifier localement les modèles avec Visual Studio et personnaliser les applications logiques pour différents environnements de déploiement. Le téléchargement d’applications logiques *paramètre* automatiquement leurs définitions dans des [modèles Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), qui utilisent également JSON (JavaScript Objet Notation).

1. Dans Visual Studio, ouvrez Cloud Explorer, puis recherchez et sélectionnez l’application logique à télécharger à partir d’Azure.

2. Dans le menu contextuel de l’application, sélectionnez **Ouvrir avec l’éditeur d’application logique**.

   Le Concepteur d’application logique s’ouvre et présente l’application logique. 
   Pour examiner la structure de définition sous-jacente de l’application logique, au bas du Concepteur, sélectionnez **Mode Code**. 

3. Dans la barre d’outils du Concepteur, choisissez **Télécharger**.

   ![Choisir « Télécharger »](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Lorsque vous êtes invité à entrer un emplacement, accédez à cet emplacement et enregistrez le modèle Resource Manager de la définition d’application logique au format de fichier JSON (.json). 

Votre définition d’application logique apparaît dans la sous-section `resources` à l’intérieur du modèle Resource Manager. Vous pouvez maintenant modifier la définition d’application logique et le modèle Resource Manager avec Visual Studio. Vous pouvez également ajouter le modèle en tant que projet Azure Resource Manager à une solution Visual Studio. Découvrez-en plus sur les [projets Resource Manager pour les applications logiques dans Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Actualiser à partir d’Azure

Si vous modifiez votre application logique dans le portail Azure et souhaitez conserver ces modifications, veillez à actualiser cette version de l’application dans Visual Studio. 

* Dans Visual Studio, dans la barre d’outils du Concepteur d’application logique, choisissez **Actualiser**.

  -ou-

* Dans Visual Studio Cloud Explorer, ouvrez le menu contextuel de l’application logique et sélectionnez **Actualiser**. 

![Actualiser l’application logique avec des mises à jour](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Publier des mises à jour d’application logique

Lorsque vous êtes prêt à déployer vos mises à jour d’application logique de Visual Studio vers Azure, dans la barre d’outils du Concepteur d’application logique, choisissez **Publier**.

![Publier une application logique mise à jour](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Exécuter manuellement votre application logique

Vous pouvez déclencher manuellement une application logique déployée dans Azure à partir de Visual Studio. Dans la barre d’outils du Concepteur d’application logique, choisissez **Déclencheur d’exécution**.

![Exécuter manuellement une application logique](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Examiner l’historique des exécutions

Pour vérifier l’état des exécutions d’application logique et résoudre les problèmes qui y sont liés, vous pouvez consulter les détails, comme les entrées et sorties, de ces exécutions dans Visual Studio.

1. Dans Cloud Explorer, ouvrez le menu contextuel de l’application logique et sélectionnez **Ouvrir l’historique des exécutions**.

   ![Ouvrir l’historique des exécutions](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Pour afficher les détails d’une exécution spécifique, double-cliquez dessus. Par exemple : 

   ![Historique des exécutions détaillé](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Pour trier la table par propriété, choisissez l’en-tête de colonne de cette propriété. 

3. Développez les étapes dont vous voulez examiner les entrées et sorties. Par exemple : 

   ![Afficher les entrées et sorties de chaque étape](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Activer ou désactiver une application logique

Sans supprimer votre application logique, vous pouvez arrêter l’exécution du déclencheur dès que la prochaine condition de déclenchement est remplie. La désactivation de votre application logique empêche le moteur de Logic Apps de créer et d’exécuter les futures instances de workflow de votre application logique.
Dans Cloud Explorer, ouvrez le menu contextuel de votre application logique et sélectionnez **Désactiver**.

![Désactiver votre application logique](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Lorsque vous voulez que votre application logique reprenne du service, vous pouvez la réactiver. Dans Cloud Explorer, ouvrez le menu contextuel de votre application logique et sélectionnez **Activer**.

![Activer votre application logique](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Supprimer votre application logique

Pour supprimer votre application logique à partir du portail Azure, dans Cloud Explorer, ouvrez le menu contextuel de votre application logique, puis sélectionnez **Supprimer**.

![Supprimer votre application logique](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment gérer des applications logiques déployées avec Visual Studio. À présent, découvrez-en plus sur la personnalisation des définitions d’application logique pour le déploiement :

> [!div class="nextstepaction"]
> [Créer des définitions d’application logique en JSON](../logic-apps/logic-apps-author-definitions.md)