---
title: Connecteur Wunderlist dans Azure Logic Apps | Microsoft Docs
description: "Créez une connexion à Wunderlist et utilisez cette connexion pour générer votre flux de travail dans les applications logiques."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 3657955ca4280fecd3a0fb1ea64b90e0a5c5c765
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Prise en main du connecteur Wunderlist
Wunderlist fournit un gestionnaire de tâches et de listes de tâches pour aider les utilisateurs à travailler efficacement.  Si vous partagez une liste de courses avec un proche, si vous travaillez sur un projet ou planifiez des vacances, Wunderlist facilite la capture, le partage et le suivi de vos listes de tâches. Wunderlist est instantanément synchronisé entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches à partir de n’importe quel endroit.

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-wunderlist"></a>Créer une connexion à Wunderlist
Pour créer des applications logiques avec Wunderlist, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| par jeton |OUI |Fournir des informations d’identification Wunderlist |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/wunderlist/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).