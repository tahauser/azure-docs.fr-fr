---
title: Utiliser le connecteur SharePoint Server dans vos applications logiques | Microsoft Docs
description: Commencer à utiliser le connecteur SharePoint Server dans vos applications logiques
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: d342b3c4f84c5dab212b9327d6a72759934d0ae5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>Prise en main du connecteur SharePoint
Le connecteur SharePoint permet d’utiliser des listes dans SharePoint.

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Créer une connexion à SharePoint
Pour utiliser le connecteur SharePoint, commencez par créer une **connexion**, puis indiquez les détails de ces propriétés : 

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| par jeton |OUI |Fournir les informations d’identification SharePoint |

Pour vous connecter à **SharePoint**, entrez votre identité (nom d’utilisateur et mot de passe, informations d’identification de la carte à puce, etc.). Une fois l’authentification terminée, vous pourrez utiliser le connecteur SharePoint dans votre application logique. 

Dans le concepteur de votre application logique, suivez les étapes ci-dessous pour vous connecter, et créez la **connexion** à utiliser dans votre application logique :

1. Entrez SharePoint dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant SharePoint dans leur nom :    
   ![Configurer SharePoint][1]  
2. Sélectionner **SharePoint - Quand un fichier est créé**   
3. Sélectionnez **Connexion à SharePoint** :   
   ![Configurer SharePoint][2]    
4. Entrez vos informations d’identification SharePoint pour vous connecter et vous authentifier auprès de SharePoint    
   ![Configurer SharePoint][3]     
5. Une fois l’authentification terminée, vous accéderez automatiquement à votre application logique pour la terminer en configurant la boîte de dialogue **À la création d’un fichier** de SharePoint.          
   ![Configurer SharePoint][4]  
6. Vous pouvez ensuite ajouter d’autres déclencheurs et actions dont vous avez besoin pour terminer votre application logique.   
7. Enregistrez votre travail en sélectionnant **Enregistrer** dans le menu (en haut).

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/sharepoint/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
