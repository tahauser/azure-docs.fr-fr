---
title: Solutions de gestion Azure | Microsoft Docs
description: "Les solutions de gestion incluent des scénarios de gestion prêts à l’emploi dans Azure que les clients peuvent ajouter à leur espace de travail Log Analytics.  Cet article explique comment les solutions personnalisées sont créées par les clients et partenaires."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Utilisation des solutions de gestion Azure (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire des solutions de gestion dans Azure qui sont actuellement en préversion.    
> 
> 

Les solutions de gestion incluent des scénarios de gestion prêts à l’emploi que les clients peuvent ajouter à leur environnement Azure.  En plus des [solutions proposées par Microsoft](../log-analytics/log-analytics-add-solutions.md), les partenaires et les clients peuvent créer des solutions de gestion utilisables dans leur propre environnement ou mises à la disposition des clients par le biais de la communauté.

## <a name="finding-and-installing-management-solutions"></a>Recherche et installation de solutions de gestion
Il existe plusieurs méthodes de localisation et d’installation de solutions de gestion, qui sont décrites dans les sections suivantes.

### <a name="azure-marketplace"></a>Place de marché Azure
Les solutions de gestion fournies par Microsoft et les partenaires approuvés peuvent être installées à partir de la Place de marché Azure, dans le Portail Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **Tous les services**.
3. Faites défiler la liste jusqu’à **Solutions**, ou bien tapez *solutions* dans la boîte de dialogue **Filtre**.
4. Cliquez sur le bouton **+ Ajouter** .
5. Recherchez les solutions qui vous intéressent en parcourant la liste, en cliquant sur le bouton **Filtre**, ou encore en tapant dans la boîte **Rechercher partout**.
6. Cliquez sur un élément de la Place de marché pour afficher ses informations détaillées.
7. Cliquez sur **Créer** pour ouvrir le volet **Ajouter une solution**.
8. Vous êtes invité à entrer les informations requises, notamment [l’espace de travail Log Analytics et le compte Automation](#log-analytics-workspace-and-automation-account), en plus des valeurs des paramètres de la solution.
9. Cliquez sur **Créer** pour installer la solution.

### <a name="oms-portal"></a>Portail OMS
Les solutions de gestion fournies par Microsoft peuvent être installées à partir de la galerie de solutions dans le portail OMS.

1. Connectez-vous au portail OMS.
2. Cliquez sur la vignette **Galerie de solutions**.
3. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'OMS. Cliquez sur le nom de la solution que vous souhaitez ajouter.
4. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Add**.
5. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d’aperçu dans le portail. Vous pouvez commencer à l’utiliser une fois que Log Analytics a traité vos données.

### <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Microsoft Azure
Les membres de la communauté peuvent soumettre des solutions de gestion aux modèles de démarrage rapide Azure.  Vous pouvez télécharger ces modèles pour une installation ultérieure ou les inspecter pour apprendre à [créer vos propres solutions](#creating-a-solution).

1. Suivez la procédure décrite dans [Espace de travail Log Analytics et compte Automation](#log-analytics-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Accédez à [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).  
3. Recherchez une solution qui vous intéresse.
4. Sélectionnez la solution dans les résultats pour en afficher les détails.
5. Cliquez sur le bouton **Déployer dans Azure**.
6. Vous êtes invité à fournir des informations comme le groupe de ressources et l’emplacement, en plus des valeurs des paramètres de la solution.
7. Cliquez sur **Achat** pour installer la solution.

### <a name="deploy-azure-resource-manager-template"></a>Déployer un modèle Azure Resource Manager
Les solutions que vous obtenez dans la communauté ou que vous [créez vous-même](#creating-a-solution) sont implémentées comme modèle Resource Manager ; vous pouvez utiliser toutes les méthodes standards de [déploiement d’un modèle](../azure-resource-manager/resource-group-template-deploy-portal.md).  Notez que vous devez créer et lier [l’espace de travail Log Analytics et le compte Automation](#log-analytics-workspace-and-automation-account) avant d’installer la solution.

## <a name="log-analytics-workspace-and-automation-account"></a>Espace de travail Log Analytics et compte Automation
La plupart des solutions de gestion nécessitent un [espace de travail Log Analytics](../log-analytics/log-analytics-manage-access.md) avec des vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) contenant les runbooks et les ressources associées. L’espace de travail et le compte doivent répondre aux exigences suivantes.

* Une solution ne peut utiliser qu’un seul espace de travail Log Analytics et un seul compte Automation.  
* L’espace de travail Log Analytics et le compte Automation utilisés par une solution doivent être liés l’un à l’autre. Un espace de travail Log Analytics ne peut être lié qu’à un seul compte Automation, et un compte Automation ne peut être lié qu’à un seul espace de travail Log Analytics.
* Pour être liés, l’espace de travail Log Analytics et le compte Automation doivent se trouver dans le même groupe de ressources et la même région.  La configuration suivante fait figure d’exception : un espace de travail Log Analytics dans la région États-Unis de l’Est et un compte Automation dans États-Unis de l’Est 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Création d’un lien entre un espace de travail Log Analytics et un compte Automation
Le mode de spécification de l’espace de travail Log Analytics et du compte Automation dépend de la méthode d’installation de votre solution.

* Quand vous installez une solution Microsoft avec le portail OMS, elle est installée dans l’espace de travail Log Analytics actif ; aucun compte Automation n’est nécessaire.
* Quand vous installez une solution sur la Place de marché Azure, vous êtes invité à choisir un espace de travail Log Analytics et un compte Automation ; un lien est créé pour vous entre les deux.  
* Pour les solutions extérieures à la Place de marché Azure, vous devez lier l’espace de travail Log Analytics et le compte Automation avant d’installer la solution.  Pour ce faire, vous pouvez sélectionner n’importe quelle solution de la Place de marché Azure et choisir l’espace de travail Log Analytics et le compte Automation.  Vous n’êtes pas obligé d’installer effectivement la solution, car le lien est créé dès que l’espace de travail Log Analytics et le compte Automation sont sélectionnés.  Une fois le lien créé, vous pouvez utiliser cet espace de travail Log Analytics et ce compte Automation pour toutes les solutions. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Vérification du lien entre un espace de travail Log Analytics et un compte Automation
Vous pouvez vérifier le lien entre un espace de travail Log Analytics et un compte Automation à l’aide de la procédure suivante.

1. Sélectionnez le compte Automation dans le Portail Azure.
2. Si le paramètre **Espace de travail** dans la section **Ressources associées** du menu est activé, cela signifie que ce compte est attaché à un espace de travail Log Analytics.  Vous pouvez cliquer sur **Espace de travail** pour afficher les détails de l’espace de travail.

## <a name="listing-management-solutions"></a>Liste des solutions de gestion
Utilisez la procédure suivante pour afficher les solutions de gestion des espaces de travail liés à votre abonnement Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **Tous les services**.
3. Faites défiler la liste jusqu’à **Solutions**, ou bien tapez *solutions* dans la boîte de dialogue **Filtre**.
4. Les solutions installées dans tous vos espaces de travail sont listées.

Notez que vous ne pouvez consulter que les solutions Microsoft installées dans l’espace de travail actif à l’aide du portail OMS.

## <a name="removing-a-management-solution"></a>Suppression d’une solution de gestion
Lorsqu’une solution de gestion est supprimée, toutes les ressources de la solution sont également supprimées.  

1. Recherchez la solution dans le Portail Azure à l’aide de la procédure décrite dans [Liste des solutions](#listing-solutions).
2. Sélectionnez la solution que vous souhaitez supprimer.
3. Cliquez sur le bouton **Supprimer** .

## <a name="creating-a-management-solution"></a>Création d’une solution de gestion
Une aide complète à la création de solutions de gestion est disponible dans [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Étapes suivantes
* Dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates), recherchez des exemples de modèles Resource Manager.
* Créez vos propres [solutions de gestion](operations-management-suite-solutions-creating.md).

