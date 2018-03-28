---
title: Connecteur Wunderlist dans Azure Logic Apps | Microsoft Docs
description: Créez une connexion à Wunderlist et utilisez cette connexion pour générer votre flux de travail dans les applications logiques.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4d1ae30724faa59dcdeffd21be9c67d280d574f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Prise en main du connecteur Wunderlist
Wunderlist est un gestionnaire de listes et de tâches qui aide les utilisateurs à mener à bien leurs tâches.  Que ce soit pour partager une liste de courses avec un proche, pour travailler sur un projet ou pour planifier des vacances, Wunderlist facilite la capture, le partage et la réalisation des éléments de la liste. Wunderlist se synchronise instantanément entre votre téléphone, votre tablette et votre ordinateur, pour vous permettre d’accéder à toutes vos tâches où que vous soyez.

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