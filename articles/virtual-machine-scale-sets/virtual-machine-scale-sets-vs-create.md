---
title: "Déployer un groupe de machines virtuelles identiques à l’aide de Visual Studio | Microsoft Docs"
description: "Déployer des jeux de mise à l'échelle de machines virtuelles à l'aide de Visual Studio et d’un modèle Resource Manager"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Création d’un jeu de mise à l’échelle de machines virtuelles avec Visual Studio
Cet article vous indique comment déployer un jeu de mise à l’échelle de machines virtuelles Azure à l'aide d'un déploiement de groupe de ressources Visual Studio.

Les [groupes de machines virtuelles identiques Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) représentent une ressource de calcul Azure qui permet de déployer et de gérer une collection de machines virtuelles similaires via des options de mise à l'échelle automatique et d'équilibrage de charge. Vous pouvez configurer et déployer des groupes de machines virtuelles identiques à l’aide de [modèles Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Les modèles Azure Resource Manager peuvent être déployés à l’aide de l’interface de ligne de commande (CLI) Azure, de PowerShell, de REST et directement à partir de Visual Studio. Visual Studio fournit des exemples de modèles qui peuvent être déployés dans le cadre d’un projet de déploiement de groupe de ressources Azure.

Les déploiements de groupe de ressources Azure vous permettent de regrouper et de publier un ensemble de ressources Azure connexes dans une même opération de déploiement. Pour en savoir plus, consultez la rubrique [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## <a name="pre-requisites"></a>Conditions préalables
Pour commencer le déploiement de groupes de machines virtuelles identiques dans Visual Studio, vous devez disposer des éléments suivants :

* Visual Studio 2013 ou une version ultérieure
* Kit de développement logiciel (SDK) Azure 2.7, 2.8 ou 2.9

>[!NOTE]
>Ces instructions partent du principe que vous utilisez Visual Studio avec le [Kit de développement logiciel (SDK) Azure 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Création d’un projet
1. Créez un projet dans Visual Studio en sélectionnant **Fichier | Nouveau | Projet**.
   
    ![Fichier Nouveau][file_new]

2. Sous **Visual C# | Cloud**, sélectionnez **Azure Resource Manager** pour créer un projet de déploiement d’un modèle Azure Resource Manager.
   
    ![Créer un projet][create_project]

3. Depuis la liste des modèles, sélectionnez le modèle de jeux de mise à l'échelle de machines virtuelles Windows ou Linux.
   
   ![Sélectionner un modèle][select_Template]

4. Une fois votre projet créé, des scripts de déploiement PowerShell, un modèle Azure Resource Manager et un fichier de paramètres pour le groupe de machines virtuelles identiques s’affichent.
   
    ![Explorateur de solutions][solution_explorer]

## <a name="customize-your-project"></a>Personnalisation de votre projet
Vous pouvez modifier dès à présent le modèle pour le personnaliser en fonction des besoins de votre application, en ajoutant par exemple des propriétés d'extension de machines virtuelles ou en modifiant les règles d'équilibrage de charge. Par défaut, les modèles de groupe de machines virtuelles identiques sont configurés pour déployer l’extension AzureDiagnostics qui permet d’ajouter très facilement des règles de mise à l’échelle automatique. Il déploie également un équilibreur de charge avec une adresse IP publique, configuré avec les règles NAT entrantes. 

L’équilibrage de charge vous permet de vous connecter aux instances de machine virtuelle avec SSH (Linux) ou RDP (Windows). La plage de ports frontaux commence à 50000. Pour Linux, cela signifie que si vous utilisez SSH pour le port 50000, vous êtes redirigé vers le port 22 de la première machine virtuelle dans le groupe identique. La connexion au port 50001 est acheminée vers le port 22 de la deuxième machine virtuelle et ainsi de suite.

 Un bon moyen de modifier vos modèles avec Visual Studio consiste à utiliser le plan JSON pour organiser des paramètres, des variables et des ressources. En comprenant le schéma, Visual Studio peut signaler les erreurs figurant dans votre modèle avant de le déployer.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>Déployer le projet
1. Déployez le modèle Azure Resource Manager pour créer la ressource du groupe de machines virtuelles identiques. Cliquez avec le bouton droit de la souris sur le nœud du projet et sélectionnez **Déployer | Nouveau déploiement**.
   
    ![Déployer un modèle][5deploy_Template]
    
2. Sélectionnez votre abonnement dans la boîte de dialogue « Déploiement vers un groupe de ressources ».
   
    ![Déployer un modèle][6deploy_Template]

3. À ce stade, vous pouvez créer un groupe de ressources Azure sur lequel déployer votre modèle.
   
    ![Nouveau groupe de ressources][new_resource]

4. Ensuite, cliquez sur **Modifier les paramètres** pour entrer des paramètres qui sont transmis à votre modèle. Entrez le nom d’utilisateur et le mot de passe pour le système d’exploitation, requis pour créer le déploiement. Si les outils PowerShell pour Visual Studio ne sont pas installés, il est recommandé d’activer l’option **Enregistrer les mots de passe** afin d’éviter une invite de ligne de commande PowerShell masquée, ou utilisez le [support KeyVault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Modifier les paramètres][edit_parameters]

5. Cliquez maintenant sur **Déployer**. La fenêtre **Sortie** affiche la progression du déploiement. Notez que l’action exécute le script **Deploy-AzureResourceGroup.ps1**.
   
   ![Fenêtre Sortie][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Exploration de votre groupe de machines virtuelles identiques
Une fois le déploiement terminé, vous pouvez afficher le nouveau groupe de machines virtuelles identiques dans le **Cloud Explorer** de Visual Studio (actualisez la liste). Cloud Explorer vous permet de gérer des ressources Azure dans Visual Studio lors du développement d'applications. Vous pouvez également afficher votre groupe identique de machines virtuelles dans le [portail Azure](https://portal.azure.com) et [l’Explorateur de ressources Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 Le portail vous explique comment gérer visuellement votre infrastructure Azure par le biais d’un navigateur web, tandis qu’Azure Resource Explorer vous permet d’explorer et de déboguer très facilement des ressources Azure, en vous donnant un aperçu de la « vue d’instance » et en vous indiquant également les commandes PowerShell pour les ressources que vous recherchez.

## <a name="next-steps"></a>Étapes suivantes
Une fois les groupes de machines virtuelles identiques déployés avec succès via Visual Studio, vous pouvez personnaliser davantage votre projet en fonction des besoins de votre application. Vous pouvez, par exemple, configurer la mise à l’échelle automatique via l’ajout d’une ressource **Insights**, l’ajout d’une infrastructure à votre modèle (comme des machines virtuelles autonomes) ou le déploiement d’applications à l’aide de l’extension de script personnalisé. Vous trouverez de bons exemples de modèles dans le référentiel GitHub [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) (recherchez le terme « vmss »).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
