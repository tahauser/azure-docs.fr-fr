---
title: "Ajouter des balises à un laboratoire dans Azure DevTest Labs | Microsoft Docs"
description: "Découvrez comment ajouter une balise à un laboratoire dans Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: 72e82b207a543a9020b38b2c4494717e9f648ab7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Ajouter des balises à un laboratoire dans Azure DevTest Labs

Vous pouvez créer des balises personnalisées et les appliquer à vos ressources DevTest Labs pour classer vos ressources logiquement. Vous pouvez ensuite rapidement et facilement voir toutes les ressources de votre abonnement avec cette balise. Les balises sont utiles si vous devez organiser les ressources à des fins de facturation ou de gestion.

Les ressources prises en charge par les balises comprennent les

* Machines virtuelles de calcul
* Cartes réseau
* Adresses IP
* Équilibreurs de charge
* Comptes de stockage
* Disques gérés

Vous pouvez appliquer des balises quand vous souhaitez [Créer un laboratoire](devtest-lab-create-lab.md), puis les gérer via le panneau Balises dans Configuration et paramètres.

Chaque balise est composée d’une paire **nom**/**valeur**. Par exemple, vous pouvez créer une balise avec le nom *costcenter* et lui attribuer la valeur *34543*. Une telle balise peut à l’avenir vous aider à identifier les ressources de laboratoire facturables dans cette zone spécifique de votre organisation. Vous devez choisir les noms et les valeurs qui ont un sens en fonction de la manière dont vous souhaitez organiser votre abonnement.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Étapes de gestion des balises dans un laboratoire existant

1. Connectez-vous au [Portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Si nécessaire, sélectionnez **Autres services**, puis **DevTest Labs** dans la liste. Votre laboratoire peut déjà être affiché sur le Tableau de bord dans **Toutes les ressources**.
1. Dans la liste des laboratoires, sélectionnez le laboratoire dans lequel vous souhaitez ajouter ou gérer vos balises.  
1. Dans la zone **Vue d’ensemble** du laboratoire, sélectionnez **Configuration et stratégies**.  

    ![Bouton Configuration et stratégies](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Sur la gauche, dans **GÉRER**, sélectionnez **Balises**.
1. Pour créer une nouvelle étiquette pour ce laboratoire, entrez une paire **Nom**/**Valeur** et sélectionnez **Enregistrer**. Vous pouvez également sélectionner une balise existante dans la liste pour afficher ou gérer les ressources associées à cette balise.

    ![Gérer les balises](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Présentation des limitations des balises

Les limites suivantes s’appliquent aux balises :

* Chaque ressource ou groupe de ressources peut inclure un maximum de 15 paires nom/valeur de balise. Cette limitation s’applique uniquement aux balises directement appliquées au groupe de ressources ou à la ressource. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 15 paires nom/valeur de balise. 
* Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères. Pour les comptes de stockage, le nom de balise est limité à 128 caractères, et la valeur de balise à 256 caractères.
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.

[Utiliser des balises pour organiser vos ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fournit des détails supplémentaires sur l’utilisation de balises dans Azure, y compris la gestion des balises à l’aide de PowerShell ou d’Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez appliquer des restrictions et des conventions sur votre abonnement avec des stratégies personnalisées. La stratégie que vous définissez peut exiger que toutes les ressources aient une valeur pour une balise en particulier. Pour plus d’informations, consultez [Définir des stratégies et la planification](devtest-lab-set-lab-policy.md).
* Explorez la [Galerie de modèles de démarrage rapide d’Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
