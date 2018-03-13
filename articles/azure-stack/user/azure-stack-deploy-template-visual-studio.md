---
title: "Déployer des modèles avec Visual Studio dans Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer des modèles avec Visual Studio dans Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Déploiement de modèles dans Azure Stack à l’aide de Visual Studio

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez Visual Studio pour déployer des modèles Azure Resource Manager dans le Kit de développement Azure Stack.

1. [Procédez à l’installation et à la connexion](azure-stack-install-visual-studio.md) d’Azure Stack avec Visual Studio.
2. Ouvrez Visual Studio.
3. Cliquez sur **Fichier**, sur **Nouveau** puis, dans la boîte de dialogue **Nouveau projet**, cliquez sur **Groupe de ressources Azure**.
4. Saisissez le **Nom** du nouveau projet, puis cliquez sur **OK**.
5. Dans la boîte de dialogue **Sélectionner un modèle Azure**, dans la liste déroulante *Afficher les modèles à partir de cet emplacement*, sélectionnez **Démarrage rapide Azure Stack**.
6. Cliquez sur **101-create-storage-account**, puis cliquez sur **OK**.  
7. Dans le nouveau projet, vous pouvez afficher la liste des modèles disponibles en développant le nœud **Modèles** dans le volet **Explorateur de solutions**.
8. Dans le volet **Explorateur de solutions**, cliquez avec le bouton droit de la souris sur le nom de votre projet, cliquez sur **Déployer**, puis sur **Nouveau déploiement**.
9. Dans la boîte de dialogue **Déployer vers le groupe de ressources**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement Microsoft Azure Stack.
10. Dans la liste **Groupe de ressources** , sélectionnez un groupe de ressources existant ou créez-en un.
11. Dans la liste **Emplacement du groupe de ressources**, choisissez un emplacement, puis cliquez sur **Déployer**.
12. Dans la boîte de dialogue **Modifier les paramètres**, saisissez les valeurs correspondant aux paramètres (qui varient selon le modèle), puis cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
[Déployer des modèles avec la ligne de commande](azure-stack-deploy-template-command-line.md)

[Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)

