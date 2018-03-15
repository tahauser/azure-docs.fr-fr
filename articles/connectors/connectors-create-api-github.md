---
title: "Se connecter à GitHub avec Azure Logic Apps | Microsoft Docs"
description: Automatiser les flux de travail pour GitHub avec Azure Logic Apps
services: logic-apps
documentationcenter: 
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
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-github"></a>Se connecter à GitHub

GitHub est un service d’hébergement de référentiel Git basé sur le web. Outre d’autres fonctionnalités, il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git.

Pour bien commencer avec le connecteur GitHub, [commencez par créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub

Pour utiliser le connecteur GitHub dans une application logique, commencez par créer une *connexion*, puis spécifiez les détails des propriétés suivantes : 

| Propriété | Obligatoire | Description | 
| -------- | -------- | ----------- | 
| par jeton | OUI | Indiquez vos informations d’identification GitHub. |

Après avoir créé la connexion, vous pouvez exécuter les actions et rechercher les déclencheurs décrits dans cet article.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Passez en revue les [détails des connecteurs](/connectors/github/) pour connaître les déclencheurs et actions définis dans Swagger ainsi que les éventuelles limites.

## <a name="find-more-connectors"></a>Rechercher d’autres connecteurs

* Passez en revue la [liste des connecteurs](apis-list.md).