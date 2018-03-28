---
title: Ajouter le connecteur Office 365 Outlook dans vos applications logiques | Microsoft Docs
description: 'Créez des applications logiques avec un connecteur Office 365 pour permettre l’interaction avec Office 365. Par exemple : création, modification et mise à jour de contacts et d’éléments de calendrier.'
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b2f6cc2c-bba2-493a-b0ba-841785462a80
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: ea0b2f657f808bb6ba4bf427497955b9e520c669
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Prise en main du connecteur Office 365 Outlook
Le connecteur Office 365 Outlook permet d’interagir avec Outlook dans Office 365. Utilisez ce connecteur pour créer, modifier et mettre à jour des contacts et des éléments de calendrier, ainsi que pour recevoir et envoyer des e-mails et pour y répondre.

Avec Office 365 Outlook, vous pouvez effectuer les opérations suivantes :

* Créer votre workflow à l’aide des fonctionnalités de messagerie et de calendrier dans Office 365. 
* Utiliser des déclencheurs pour démarrer votre workflow lorsqu’un nouvel e-mail apparaît, lorsqu’un élément de calendrier est mis à jour, et bien davantage.
* Utiliser des actions pour envoyer un e-mail, créer un événement de calendrier, etc. Par exemple, lorsqu’un nouvel objet existe dans Salesforce (déclencheur), un e-mail doit être envoyé à votre application Office 365 Outlook (action). 

Cet article décrit comment utiliser le connecteur Office 365 Outlook dans une application logique et répertorie les déclencheurs et les actions.

> [!NOTE]
> Cette version de l’article s’applique à la disponibilité générale des applications logiques.
> 
> 

Pour plus d’informations sur Logic Apps, voir [Qu’est-ce qu’une application logique ?](../logic-apps/logic-apps-overview.md) et [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Connexion à Office 365
Pour que votre application logique puisse accéder à un service, vous devez d’abord créer une *connexion* à celui-ci. Une connexion permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à Office 365 Outlook, vous devez préalablement disposer d’une *connexion* Office 365. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous souhaitez vous connecter. Ensuite, dans Office 365 Outlook, entrez les informations d’identification de votre compte Office 365 pour créer la connexion.

## <a name="create-the-connection"></a>Créer la connexion
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilisation d’un déclencheur
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. Les déclencheurs « interrogent » le service à l’intervalle et à la fréquence de votre choix. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Dans l’application logique, tapez « office 365 » pour obtenir la liste des déclencheurs :  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Sélectionnez **Office 365 Outlook - When an upcoming event is starting soon** (Office 365 Outlook - Quand un événement à venir est imminent). Si une connexion existe déjà, sélectionnez un calendrier dans la liste déroulante.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Si vous êtes invité à vous connecter, entrez les informations de connexion pour créer la connexion. La section [Créer la connexion](connectors-create-api-office365-outlook.md#create-the-connection) dans cette rubrique répertorie les étapes. 
   
   > [!NOTE]
   > Dans cet exemple, l’application logique s’exécute lorsqu’un événement de calendrier est mis à jour. Pour visualiser les résultats de ce déclencheur, ajoutez une autre action qui vous envoie un SMS. Par exemple, ajoutez l’action Twilio *Send message* (Envoyer un message) qui vous envoie un SMS lorsque l’événement de calendrier doit démarrer dans 15 minutes. 
   > 
   > 
3. Sélectionnez le bouton **Modifier**, puis renseignez les valeurs **Fréquence** et **Intervalle**. Par exemple, si vous souhaitez que le déclencheur interroge le service toutes les 15 minutes, définissez le champ **Fréquence** sur **Minute**, et le champ **Intervalle** sur **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="use-an-action"></a>Utilisation d’une action
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Sélectionnez le signe plus. Vous disposez de plusieurs options : **Ajouter une action**, **Ajouter une condition** ou l’une des options **Plus**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Choisissez **Ajouter une action**.
3. Dans la zone de texte, tapez « office 365 » pour obtenir la liste de toutes les actions disponibles.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. Dans notre exemple, choisissez **Office 365 Outlook - Créer un contact**. Si une connexion existe déjà, choisissez **ID du dossier**, **Prénom** et d’autres propriétés :  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Si vous êtes invité à saisir les informations de connexion, entrez les informations requises pour créer la connexion. La section [Créer la connexion](connectors-create-api-office365-outlook.md#create-the-connection) dans cette rubrique décrit ces propriétés. 
   
   > [!NOTE]
   > Dans cet exemple, nous créons un contact dans Office 365 Outlook. Vous pouvez utiliser la sortie d’un autre déclencheur pour créer le contact. Par exemple, ajoutez le déclencheur SalesForce *Quand un objet est créé*. Ensuite, ajoutez l’action Office 365 Outlook *Créer un contact* qui utilise les champs SalesForce pour créer le contact dans Office 365. 
   > 
   > 
5. **Enregistrez** vos modifications (dans le coin supérieur gauche de la barre d’outils). Votre application logique est enregistrée et peut être activée automatiquement.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/office365connector/). 

## <a name="next-steps"></a>Étapes suivantes
[Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).

