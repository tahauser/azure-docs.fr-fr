---
title: "Configurer l’observateur d’événements avec Azure Event Hubs pour Azure Logic Apps | Microsoft Docs"
description: "Surveiller les flux de données afin de recevoir et envoyer des événements pour Azure Logic Apps avec Azure Event Hubs"
services: logic-apps
keywords: "flux de données, observateur d’événements, event hubs"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: a7f31c2c17d326d58ede0bb00cdc0f701069ea14
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Surveillez, recevez et envoyez des événements avec le connecteur Event Hubs

Pour configurer un observateur d’événements afin que votre application logique puisse détecter, recevoir et envoyer des événements, connectez-vous à un hub [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) à partir de votre application logique. En savoir plus sur [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Configuration requise

* Vous devez avoir un [espace de noms Event Hubs et un hub Event Hubs](../event-hubs/event-hubs-create.md) dans Azure. Découvrez [comment créer un espace de noms Event Hubs et un hub Event Hubs](../event-hubs/event-hubs-create.md). 

* Pour utiliser [n’importe quel connecteur](https://docs.microsoft.com/azure/connectors/apis-list) dans votre application logique, vous devez d’abord créer une application logique. Découvrez [comment créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Vérifier les autorisations d’espace de noms Event Hubs et rechercher la chaîne de connexion

Pour que votre application logique accède à n’importe quel service, vous devez créer une [*connexion*](./connectors-overview.md) entre votre application logique et le service, si ce n’est pas déjà fait. Cette connexion autorise votre application logique à accéder aux données.
Pour que votre application logique accède à votre hub Event Hubs, vous devez disposer des autorisations **Gérer** et de la chaîne de connexion pour votre espace de noms Event Hubs.

Pour vérifier vos autorisations et obtenir la chaîne de connexion, procédez comme suit.

1.  Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). 

2.  Accédez à votre *espace de noms* Event Hubs, et non au hub Event Hubs spécifique. Dans le panneau de l’espace de noms, sous **Paramètres**, choisissez **Stratégies d’accès partagé**. Sous **Revendications**, vérifiez que vous disposez des autorisations **Gérer** pour cet espace de noms.

    ![Gérer les autorisations pour votre espace de noms Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Pour copier la chaîne de connexion associée à l’espace de noms Event Hubs, choisissez **RootManageSharedAccessKey**. En regard de votre chaîne de connexion de clé primaire, cliquez sur le bouton de copie.

    ![Copier la chaîne de connexion d’espace de noms Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Pour vérifier que votre chaîne de connexion est bien associée à votre espace de noms Event Hubs ou à un hub Event Hubs spécifique, vérifiez le paramètre `EntityPath` pour la chaîne de connexion. Si vous trouvez ce paramètre, la chaîne de connexion concerne une « entité » Event Hubs spécifique et elle ne peut pas être utilisée avec votre application logique.

4.  À présent, lorsque vous êtes invité à indiquer des informations d’identification après avoir ajouté un déclencheur ou une action Event Hubs à votre application logique, vous pouvez vous connecter à votre espace de noms Event Hubs. Nommez votre connexion, entrez la chaîne de connexion que vous avez copiée, puis choisissez **Créer**.

    ![Entrer la chaîne de connexion pour un espace de noms Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Une fois votre connexion créée, le nom de connexion doit apparaître dans le déclencheur ou l’action Event Hubs. 
    Vous pouvez désormais passer aux autres étapes dans votre application logique.

    ![Connexion d’espace de noms Event Hubs créée](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Démarrer le workflow lorsque votre hub Event Hubs reçoit de nouveaux événements

Un [*déclencheur*](../logic-apps/logic-apps-overview.md#logic-app-concepts) désigne un événement qui démarre un workflow dans votre application logique. Pour démarrer un workflow lorsque de nouveaux événements sont envoyés à votre hub Event Hubs, procédez comme suit afin d’ajouter le déclencheur qui détecte cet événement.

1.  Dans le [portail Azure](https://portal.azure.com "portail Azure"), accédez à votre application logique existante ou créez une application logique vide.

2.  Dans la zone de recherche du Concepteur d’application logique, entrez `event hubs` pour votre filtre. Sélectionnez ce déclencheur : **When events are available in Event Hub (Lorsque les événements sont disponibles dans un hub Event Hubs)**

    ![Sélectionner le déclencheur lorsque votre hub Event Hubs reçoit de nouveaux événements](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Si vous ne disposez pas encore d’une connexion à votre espace de noms Event Hubs, vous êtes invité à créer cette connexion dès maintenant. Nommez votre connexion, puis entrez la chaîne de connexion pour votre espace de noms Event Hubs. 
    Si nécessaire, découvrez [comment rechercher votre chaîne de connexion](#permissions-connection-string).

    ![Entrer la chaîne de connexion pour un espace de noms Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Une fois la connexion créée, les paramètres du déclencheur **When an event in available in Event Hub (Lorsque les événements sont disponibles dans un hub Event Hubs)** s’affichent.

    ![Paramètres du déclencheur lorsque votre hub Event Hubs reçoit de nouveaux événements](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Entrez ou sélectionnez le nom du hub Event Hubs que vous souhaitez surveiller. Sélectionnez la fréquence et l’intervalle de fréquence auxquels vous souhaitez vérifier le hub Event Hubs.

    > [!TIP]
    > Si vous souhaitez sélectionner un groupe de consommateurs pour la lecture des événements, choisissez **Afficher les options avancées**. 

    ![Spécifier un hub Event Hubs ou un groupe de consommateurs](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Vous avez à présent défini un déclencheur qui démarre un workflow pour votre application logique. 
    Votre application logique vérifie le hub Event Hubs spécifié conformément à la planification définie. 
    Si votre application trouve de nouveaux événements dans le hub Event Hubs, le déclencheur exécute d’autres actions ou d’autres déclencheurs dans votre application logique.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Envoyer des événements à votre hub Event Hubs à partir de votre application logique

Une [*action*](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une tâche effectuée par le flux de travail de votre application logique. Après avoir ajouté un déclencheur à votre application logique, vous pouvez ajouter une action permettant d’effectuer des opérations avec les données générées par ce déclencheur. Pour envoyer un événement à votre hub Event Hubs à partir de votre application logique, procédez comme suit.

1.  Dans le Concepteur d’application logique, sous le déclencheur de votre application logique, sélectionnez **Nouvelle étape** > **Ajouter une action**.

    ![Cliquez sur Nouvelle étape, puis sur Ajouter une action](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Vous pouvez maintenant rechercher et sélectionner une action à effectuer. 
    Bien que vous puissiez sélectionner n’importe quelle action, dans cet exemple, nous souhaitons que l’action Event Hubs envoie les événements.

2.  Dans la zone de recherche, entrez `event hubs` pour votre filtre.
Sélectionnez cette action : **Envoyer un événement**

    ![Sélectionnez l’action « Event Hubs - Envoyer un événement »](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Entrez les informations requises pour l’événement, comme le nom du hub Event Hubs dans lequel vous souhaitez envoyer l’événement. Entrez les autres détails facultatifs relatifs à l’événement, comme le contenu de cet événement.

    > [!TIP]
    > Si vous souhaitez spécifier la partition du hub Event Hubs où envoyer l’événement, sélectionnez **Afficher les options avancées**. 

    ![Entrez le nom du hub Event Hubs et les détails facultatifs de l’événement](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Enregistrez vos modifications.

    ![Enregistrer votre application logique](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    Vous avez à présent défini une action permettant d’envoyer des événements à partir de votre application logique. 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez tous les déclencheurs et les actions définies dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/eventhubs/). 

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions, répondre aux questions et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

*  [Rechercher d’autres connecteurs pour Azure Logic Apps](./apis-list.md)