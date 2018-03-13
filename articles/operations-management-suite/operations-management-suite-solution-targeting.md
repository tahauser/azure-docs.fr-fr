---
title: Ciblage des solutions de gestion dans Azure | Microsoft Docs
description: "Le ciblage des solutions de gestion vous permet de limiter les solutions de gestion à un ensemble spécifique d’agents.  Cet article décrit comment créer une configuration d’étendue et l’appliquer à une solution."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: e24a7540a67f842b8229223bf6602cd9e0ee5dbd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Ciblage des solutions de gestion dans Azure (préversion)
Quand vous ajoutez une solution de gestion à votre abonnement, elle est déployée automatiquement par défaut sur tous les agents Windows et Linux connectés à votre espace de travail Log Analytics.  Vous pouvez gérer vos coûts et limiter la quantité de données collectées pour une solution en limitant celle-ci à un ensemble spécifique d’agents.  Cet article explique comment utiliser le **ciblage de solution**, une fonctionnalité qui permet d’appliquer une étendue à vos solutions.

## <a name="how-to-target-a-solution"></a>Ciblage d’une solution
Il existe trois étapes au ciblage d’une solution, comme décrit dans les sections suivantes. 


### <a name="1-create-a-computer-group"></a>1. Créer un groupe d’ordinateurs
Vous spécifiez les ordinateurs que vous souhaitez inclure dans une étendue en créant un [groupe d’ordinateurs](../log-analytics/log-analytics-computer-groups.md) dans Log Analytics.  Le groupe d’ordinateurs peut être basé sur une recherche dans les journaux ou importé à partir d’autres sources comme les groupes WSUS ou Active Directory. Comme [décrit ci-dessous](#solutions-and-agents-that-cant-be-targeted), seuls les ordinateurs qui sont directement connectés à Log Analytics figurent dans l’étendue.

Une fois que le groupe d’ordinateurs est créé dans votre espace de travail, vous devez l’inclure dans une configuration d’étendue qui peut être appliquée à une ou plusieurs solutions.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Création d’une configuration d’étendue
 Une **configuration d’étendue** inclut un ou plusieurs groupes d’ordinateurs et peut être appliquée à une ou plusieurs solutions. 
 
 Créez une configuration d’étendue à l’aide de la procédure suivante.  

 1. Dans le portail Azure, accédez à **Log Analytics** et sélectionnez votre espace de travail.
 2. Dans les propriétés de l’espace de travail sous **Sources de données de l’espace de travail**, sélectionnez **Configurations d’étendue**.
 3. Cliquez sur **Ajouter** pour créer une configuration d’étendue.
 4. Entrez un **nom** pour la configuration d’étendue.
 5. Cliquez sur **Sélectionner des groupes d’ordinateurs**.
 6. Sélectionnez le groupe d’ordinateurs que vous avez créé et éventuellement d’autres groupes à ajouter à la configuration.  Cliquez sur **Sélectionner**.  
 6. Cliquez sur **OK** pour créer la configuration d’étendue. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Appliquez la configuration d’étendue à une solution.
Une fois que vous avez une configuration d’étendue, vous pouvez l’appliquer à une ou plusieurs solutions.  Notez qu’une seule configuration d’étendue peut être utilisée avec plusieurs solutions, mais chaque solution ne peut utiliser qu’une seule configuration d’étendue.

Appliquez une configuration d’étendue à l’aide de la procédure suivante.  

 1. Dans le portail Azure, accédez à **Log Analytics** et sélectionnez votre espace de travail.
 2. Dans les propriétés de l’espace de travail, sélectionnez **Solutions**.
 3. Cliquez sur la solution à laquelle appliquer une étendue.
 4. Dans les propriétés de la solution sous **Sources de données de l’espace de travail**, sélectionnez **Ciblage de solution**.  Si l’option n’est pas disponible, alors [cette solution ne peut pas être ciblée](#solutions-and-agents-that-cant-be-targeted).
 5. Cliquez sur **Ajouter une configuration d’étendue**.  Si une configuration est déjà appliquée à cette solution, alors cette option n’est pas disponible.  Vous devez supprimer la configuration existante avant d’en ajouter une autre.
 6. Cliquez sur la configuration d’étendue que vous avez créée.
 7. Consultez **l’état** de la configuration pour vous assurer qu’il s’affiche en tant que **Réussi**.  Si l’état indique une erreur, cliquez sur les points de suspension à droite de la configuration et sélectionnez **Modifier la configuration d’étendue** pour apporter des modifications.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Solutions et agents ne pouvant pas être ciblés
Voici les critères pour les agents et les solutions qui ne peuvent pas être utilisés avec le ciblage de solution.

- Le ciblage de solution s’applique uniquement aux solutions avec déploiement sur les agents.
- Le ciblage de solution s’applique uniquement aux solutions fournies par Microsoft.  Il ne s’applique pas aux solutions [créées par vous-même ou vos partenaires](operations-management-suite-solutions-creating.md).
- Vous pouvez filtrer uniquement les agents qui se connectent directement à Log Analytics.  Les solutions se déploient automatiquement sur les agents (qu’ils soient inclus dans une configuration d’étendue ou non) qui font partie d’un groupe d’administration Operations Manager connecté.

### <a name="exceptions"></a>Exceptions
Le ciblage de solution ne peut pas être utilisé avec les solutions suivantes, même si elles sont conformes aux critères indiqués.

- Évaluation de l’intégrité de l’agent

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les solutions de gestion, notamment les solutions qui peuvent être installées dans votre environnement en consultant [Ajout de solutions de gestion Azure Log Analytics dans votre espace de travail](../log-analytics/log-analytics-add-solutions.md).
- En savoir plus sur la création de groupes d’ordinateurs en consultant [Groupes d’ordinateurs dans la recherche dans les journaux de Log Analytics](../log-analytics/log-analytics-computer-groups.md).