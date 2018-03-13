---
title: Configurer une messagerie avec Azure Service Bus pour Azure Logic Apps | Microsoft Docs
description: Envoyer et recevoir des messages avec vos applications logiques en utilisant Azure Service Bus
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Envoyer et recevoir des messages avec le connecteur Azure Service Bus

Pour envoyer et recevoir des messages avec votre application logique, connectez-vous à [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Vous pouvez effectuer des actions telles qu’envoyer vers une file d’attente ou une rubrique, et recevoir d’une file d’attente ou d’un abonnement. Pour en savoir plus, voir [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) et [tarification des déclencheurs Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser le connecteur de Service Bus, vous devez disposer de ces éléments qui doivent faire partie du même abonnement Azure, de sorte qu’ils soient visibles l’un pour l’autre :

* Un [espace de noms et une entité de messagerie Service Bus, telle une file d’attente](../service-bus-messaging/service-bus-create-namespace-portal.md)
* Une [application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Se connecter à Azure Service Bus

Pour que votre application logique puisse accéder à tout service, vous devez créer une [*connexion*](./connectors-overview.md) entre votre application logique et le service, si ce n’est déjà fait. Cette connexion autorise votre application logique à accéder aux données. Pour que votre application logique puisse accéder à votre compte Service Bus, vérifiez vos autorisations.

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). 

2. Accédez à votre *espace de noms* Service Bus, pas à une « entité de messagerie » spécifique. Dans la page de l’espace de noms, sous **Paramètres**, choisissez **Stratégies d’accès partagé**. Sous **Revendications**, vérifiez que vous disposez des autorisations **Gérer** pour cet espace de noms.

   ![Gérer les autorisations pour votre espace de noms Service Bus](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Si vous souhaitez pouvoir entrer manuellement vos informations de connexion par la suite, obtenez la chaîne de connexion pour votre espace de noms Service Bus. Choisissez **RootManageSharedAccessKey**. En regard de votre chaîne de connexion de clé primaire, cliquez sur le bouton de copie. Enregistrez la chaîne de connexion en vue d’une utilisation ultérieure.

   ![Copier la chaîne de connexion de l’espace de noms Service Bus](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Pour vous assurer que votre chaîne de connexion est bien associée à votre espace de noms Service Bus ou à une entité spécifique, vérifiez la chaîne de connexion pour le paramètre `EntityPath`. Si vous trouvez ce paramètre, la chaîne de connexion est pour une entité spécifique, et n’est pas la chaîne appropriée à utiliser avec votre application logique.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Déclencher un flux de travail lorsque votre Service Bus reçoit de nouveaux messages

Un [*déclencheur*](../logic-apps/logic-apps-overview.md#logic-app-concepts) désigne un événement qui démarre un workflow dans votre application logique. Pour démarrer un flux de travail lorsque de nouveaux messages sont envoyés à votre Service Bus, procédez comme suit afin d’ajouter le déclencheur qui détecte ces messages.

1. Dans le [portail Azure](https://portal.azure.com "portail Azure"), accédez à votre application logique existante ou créez une application logique vide.

2. Dans le concepteur d’applications logiques, entrez « service bus » en tant que filtre dans la zone de recherche. Sélectionnez le connecteur **Service Bus**. 

   ![Sélectionner un connecteur Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Sélectionnez le déclencheur à utiliser. Par exemple, pour exécuter une application logique quand un nouvel élément est envoyé à une file d’attente Service Bus, sélectionnez le déclencheur suivant : **Service Bus - Quand une file d’attente reçoit un message (saisie semi-automatique)**

   ![Sélectionner un déclencheur Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. Si vous ne disposez pas encore d’une connexion à votre espace de noms Service Bus, vous êtes invité à créer cette connexion. Donnez un nom à votre connexion, puis sélectionnez l’espace de noms Service Bus à utiliser.

      ![Créer une connexion Service Bus](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Ou bien, pour entrer manuellement la chaîne de connexion, choisissez **Entrer manuellement les informations de connexion**. 
      Découvrez [comment trouver votre chaîne de connexion](#permissions-connection-string).

   2. Sélectionnez à présent la stratégie Service Bus à utiliser, puis choisissez **Créer**.

      ![Créer une connexion Service Bus, partie 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Sélectionnez la file d’attente Service Bus à utiliser, puis définissez l’intervalle et la fréquence de contrôle de la file d’attente.

   ![Sélectionner une file d’attente Service Bus, configurer l’intervalle d’interrogation](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Désormais, quand votre application logique vérifie la file d’attente sélectionnée et trouve un nouveau message, le déclencheur exécute les actions de votre logique d’application pour le message trouvé.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Envoyer des messages à partir de votre application logique à votre Service Bus

Une [*action*](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une tâche effectuée par le flux de travail de votre application logique. Après avoir ajouté un déclencheur à votre application logique, vous pouvez ajouter une action permettant d’effectuer des opérations avec les données générées par ce déclencheur. Pour envoyer un message à votre entité de messagerie Service Bus à partir de votre application logique, procédez comme suit.

1. Dans le concepteur d’applications logiques, sous votre déclencheur, choisissez **+ Nouvelle étape** > **Ajouter une action**.

2. Dans la zone de recherche, entrez «  service bus » en tant que filtre. Sélectionner le connecteur **Service Bus**

   ![Sélectionner un connecteur Service Bus](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Sélectionner l’action **Service Bus - Envoyer un message**

   ![Sélectionner « Service Bus - Envoyer un message »](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Sélectionnez l’entité de messagerie, qui est le nom de la file d’attente ou de la rubrique auxquelles envoyer le message. Ensuite, entrez le contenu du message et tous les autres détails.

   ![Sélectionner l’entité de messagerie et fournir les détails du message](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Enregistrez votre application logique. 

Vous avez configuré une action qui envoie des messages à partir de votre application logique. 

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour en savoir plus sur les déclencheurs et les actions définies par le fichier Swagger, ainsi que les limites, voir les [détails du connecteur](/connectors/servicebus/).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [tous les autres connecteurs pour Azure Logic apps](../connectors/apis-list.md)