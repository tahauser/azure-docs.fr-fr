---
title: "Développer des applications pour Azure Stack | Microsoft Docs"
description: "Découvrez les considérations relatives au développement pour le prototypage d’applications sur Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 4b9e0f5a264296e59579539dd929f5d75a54b62a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="develop-for-azure-stack"></a>Développer pour Azure Stack
Vous pouvez commencer à développer des applications dès aujourd’hui, même si vous n’avez pas accès à un environnement Azure Stack. Comme Azure Stack fournit des services Microsoft Azure qui s’exécutent dans votre centre de données, vous pouvez utiliser des outils et des processus similaires pour développer sur Azure Stack comme vous le feriez avec Azure.  Avec un peu de préparation et grâce aux instructions de la rubrique suivante, vous pouvez utiliser Azure pour émuler un environnement Azure Stack :

* Dans Azure, vous pouvez créer des modèles Azure Resource Manager pouvant également être déployés sur Azure Stack.  Pour obtenir des conseils sur le développement de vos modèles pour assurer la portabilité, consultez [Considérations relatives aux modèles](azure-stack-develop-templates.md).
* Il existe une différence de disponibilité de service et de gestion des versions de services entre Azure et Azure Stack. Vous pouvez utiliser le [module de stratégie Azure Stack](azure-stack-policy-module.md) pour restreindre les types de ressources et la disponibilité de service Azure à ce qui est disponible dans Azure Stack. La restriction des services disponibles aidera votre application à s’appuyer sur les services accessibles à Azure Stack.
* Les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sont des exemples de scénarios courants qui illustrent comment développer vos modèles pour qu’ils puissent être déployés sur Azure et Azure Stack.


