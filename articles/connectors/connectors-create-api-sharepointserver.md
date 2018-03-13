---
title: Utiliser le connecteur SharePoint Server dans vos applications logiques | Microsoft Docs
description: "Commencer à utiliser le connecteur SharePoint Server dans vos applications logiques"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: da863e0249cb46e4e569812a851f3199d57b2107
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>Prise en main du connecteur SharePoint
Le connecteur SharePoint permet d’utiliser des listes dans SharePoint.

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Créer une connexion à SharePoint
Pour utiliser le connecteur SharePoint, vous devez créer une **connexion** , puis fournir les détails de ces propriétés : 

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| par jeton |OUI |Fournir les informations d’identification SharePoint |

Pour vous connecter à **SharePoint**, entrez votre identité (nom d’utilisateur et mot de passe, informations d’identification de la carte à puce, etc.) dans SharePoint. Une fois que vous avez été authentifié, vous pouvez utiliser le connecteur SharePoint dans votre application logique. 

Dans le Concepteur de votre application logique, procédez comme suit pour vous connecter à SharePoint afin de créer la **connexion** à utiliser dans votre application logique :

1. Entrez SharePoint dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant SharePoint dans leur nom :    
   ![Configurer SharePoint][1]  
2. Sélectionner **SharePoint - Quand un fichier est créé**   
3. Sélectionnez **Connexion à SharePoint** :   
   ![Configurer SharePoint][2]    
4. Entrez vos informations d’identification SharePoint pour vous connecter et vous authentifier auprès de SharePoint    
   ![Configurer SharePoint][3]     
5. Une fois l’authentification terminée, vous serez redirigé vers votre application logique pour la terminer en configurant la boîte de dialogue **Quand un fichier est créé** de SharePoint.          
   ![Configurer SharePoint][4]  
6. Vous pouvez ensuite ajouter d’autres déclencheurs et actions dont vous avez besoin pour terminer votre application logique.   
7. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menu supérieure.  

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/sharepoint/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
