---
title: Connecteur Dropbox dans Azure Logic Apps | Documents Microsoft
description: Créez des applications logiques avec Azure App Service. Connectez-vous à Dropbox pour gérer vos fichiers. Vous pouvez exécuter différentes actions, comme charger, mettre à jour, obtenir et supprimer des fichiers dans Dropbox.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7ac72cf5b18fa19bc0294abc67bf0a7089774a89
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-dropbox-connector"></a>Prise en main du connecteur Dropbox
Connectez-vous à Dropbox pour gérer vos fichiers. Vous pouvez exécuter différentes actions, comme charger, mettre à jour, obtenir et supprimer des fichiers dans Dropbox.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Se connecter à Dropbox
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Dropbox, vous devez préalablement disposer d’une *connexion* Dropbox. Pour créer une connexion, vous devez fournir les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ainsi, dans l’exemple Dropbox, entrez les informations d’identification de votre compte Dropbox pour créer la connexion correspondante. [Apprenez-en davantage sur les connexions]().

### <a name="create-a-connection-to-dropbox"></a>Créer une connexion à Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Utiliser un déclencheur Dropbox
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Dans cet exemple, nous allons utiliser le déclencheur **Lorsqu’un fichier est créé**. Lorsque ce déclencheur apparaîtra, nous appellerons l’action Dropbox **Obtenir le contenu d’un fichier à l’aide du chemin**. 

1. Entrez *dropbox* dans la zone de recherche du concepteur Logic Apps, puis sélectionnez le déclencheur **Dropbox - Quand un fichier est créé**.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Sélectionnez le dossier dans lequel vous souhaitez effectuer le suivi de la création de fichiers. Sélectionnez le bouton ... (encadré en rouge ci-après) et accédez au dossier que vous souhaitez sélectionner pour l’entrée du déclencheur.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Utiliser une action Dropbox
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action qui récupérera le contenu du nouveau fichier.

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter lorsqu’un fichier est créé.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Sélectionnez **Ajouter une action**. Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Entrez *dropbox* pour rechercher les actions associées à Dropbox.  
4. Sélectionnez **Dropbox - Obtenir le contenu d’un fichier à l’aide du chemin** comme action à exécuter lorsqu’un fichier est créé dans le dossier Dropbox sélectionné. Le bloc de contrôles de l’action s’affiche. Vous serez invité à autoriser votre application logique à accéder à votre compte Dropbox, si vous ne l’avez pas fait précédemment.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Sélectionnez le bouton ... (situé à droite du contrôle **Chemin de fichier**) et recherchez le chemin d’accès au fichier que vous souhaitez utiliser. Une autre possibilité consiste à utiliser le jeton **chemin de fichier** pour accélérer la création de votre application logique.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Enregistrez votre travail et créez un fichier dans Dropbox pour activer votre workflow.  

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/dropbox/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).