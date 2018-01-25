---
title: Connecteur GitHub dans Azure Logic Apps | Documents Microsoft
description: "Créez des applications logiques avec Azure App Service. GitHub est un servie d’hébergement de dépôt Git sur le web. Il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git, ainsi que ses propres fonctionnalités."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c9120babaa5f6da4f33bd60ba27434e24cb2f45e
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-github-connector"></a>Prise en main du connecteur GitHub
GitHub est un servie d’hébergement de dépôt Git sur le web. Il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git, ainsi que ses propres fonctionnalités.

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub
Pour créer des applications logiques avec GitHub, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes : 

| Propriété | Obligatoire | DESCRIPTION |
| --- | --- | --- |
| par jeton |OUI |Fournir des informations d’identification GitHub |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/github/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).