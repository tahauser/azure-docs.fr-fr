---
title: Utiliser le connecteur Slack dans vos applications logiques Azure | Microsoft Docs
description: "Se connecter à Slack dans vos applications logiques"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 04ea4508495b227d6ace4a3105f283c474c51d14
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-slack-connector"></a>Prise en main du connecteur Slack
Slack est un outil de communication collaboratif qui centralise toutes les communications de votre équipe dans un seul emplacement que vous pouvez consulter instantanément, où que vous vous trouviez. 

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Créer une connexion à Slack
Pour utiliser le connecteur Slack, vous devez créer une **connexion** , puis fournir les détails de ces propriétés : 

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| par jeton |OUI |Fournir les informations d’identification de Slack |

Suivez ces étapes pour vous connecter à Slack et terminer la configuration de la **connexion** à Slack dans votre application logique :

1. Sélectionnez **Périodicité**
2. Sélectionnez une **Fréquence** et entrez un **Intervalle**
3. Sélectionnez **Ajouter une action**  
   ![Configurer Slack][1]  
4. Entrez Slack dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant Slack dans leur nom
5. Sélectionnez **Slack - Publier un message**
6. Sélectionnez **Se connecter à Slack** :  
   ![Configurer Slack][2]
7. Entrer vos informations d’identification Slack pour vous connecter et autoriser l’application    
   ![Configurer Slack][3]  
8. Vous êtes redirigé vers la page de connexion de votre organisation. **Autorisez** Slack à interagir avec votre application logique :      
   ![Configurer Slack][5] 
9. Une fois l’autorisation terminée, vous serez redirigé vers votre application logique pour la terminer en configurant la section **Slack - Obtenir tous les messages** . Ajouter les autres déclencheurs et actions dont vous avez besoin.  
   ![Configurer Slack][6]
10. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menu supérieure.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/slack/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
