---
title: "Surveillance à distance IoT et notifications avec Azure Logic Apps | Microsoft Docs"
description: "Utilisez Azure Logic Apps pour surveiller la température IoT sur votre hub IoT et envoyer automatiquement des notifications par courrier électronique dans votre boîte aux lettres en cas de détection d’anomalies."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "surveillance iot, notifications iot, surveillance de température iot"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 0caa3397723f9cd0476e85d52a8d30ae283b6e47
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Surveillance à distance IoT et notifications avec Azure Logic Apps connectant votre IoT Hub et votre boîte aux lettres

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps permet d’automatiser des processus comme une série d’étapes. Une application logique peut établir une connexion via divers services et protocoles. Elle commence par un déclencheur tel que « Lorsqu’un compte est ajouté », suivie d’une combinaison d’actions, similaire à « envoi d’une notification Push ». Cette fonctionnalité rend Logic Apps idéale pour la surveillance IoT, telle que la vigilance face aux anomalies, parmi d’autres scénarios d’utilisation.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à créer une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications. Lorsque la température est supérieure à 30 °C, l’application cliente indique `temperatureAlert = "true"` dans le message qu’elle envoie à votre IoT Hub. Suite à ce message, l’application logique vous envoie un e-mail de notification.

## <a name="what-you-do"></a>Procédure

* Créez un espace de noms Service Bus et ajoutez-lui une file d’attente.
* Ajoutez un point de terminaison et une règle de routage à votre IoT Hub.
* Créez, configurez et testez une application logique.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  * Un abonnement Azure actif.
  * Une instance Azure IoT Hub associée à votre abonnement.
  * Une application cliente qui envoie des messages à votre instance Azure IoT Hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Créer un espace de noms Service Bus et lui ajouter une file d’attente

### <a name="create-a-service-bus-namespace"></a>Création d'un espace de noms Service Bus

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Enterprise Integration** > **Service Bus**.
1. Fournissez les informations suivantes :

   **Nom** : le nom du Service Bus.

   **Niveau tarifaire**: cliquez sur **De base** > **Sélectionnez**. Le niveau de base est suffisant pour ce didacticiel.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   **Emplacement** : utilisez le même emplacement que celui utilisé par votre IoT Hub.
1. Cliquez sur **Créer**.

   ![Créer un espace de noms Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Ajouter une file d’attente Service Bus

1. Ouvrez l’espace de noms Service Bus, puis cliquez sur **+ file d’attente**.
1. Entrez un nom pour la file d’attente, puis cliquez sur **Créer**.
1. Ouvrez la file d’attente Service Bus, puis cliquez sur **Stratégies d’accès partagé** > **+ Ajouter**.
1. Entrez un nom pour la stratégie, cochez **Gérer**, puis cliquez sur **Créer**.

   ![Ajouter une file d’attente Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Ajouter un point de terminaison et une règle de routage à votre IoT Hub

### <a name="add-an-endpoint"></a>Ajout d’un point de terminaison

1. Ouvrez votre IoT Hub et cliquez sur Points de terminaison > + Ajouter.
1. Entrez les informations suivantes :

   **Nom** : le nom du point de terminaison.

   **Type de point de terminaison** : sélectionnez **File d’attente Service Bus**.

   **Espace de noms Service Bus** : sélectionnez l’espace de noms que vous avez créé.

   **File d’attente Service Bus** : sélectionnez la file d’attente que vous avez créée.
1. Cliquez sur **OK**.

   ![Ajouter un point de terminaison à votre IoT Hub dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Ajouter une règle de routage

1. Dans votre Iot Hub, cliquez sur **Itinéraires** > **+ Ajouter**.
1. Entrez les informations suivantes :

   **Nom** : le nom de la règle de routage.

   **Source de données** : sélectionnez **DeviceMessages**.

   **Point de terminaison** : sélectionnez le point de terminaison que vous avez créé.

   **Chaîne de requête** : entrez `temperatureAlert = "true"`.
1. Cliquez sur **Enregistrer**.

   ![Ajouter une règle de routage dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Créer et configurer une application logique

### <a name="create-a-logic-app"></a>Créer une application logique

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Enterprise Integration** > **Application logique**.
1. Entrez les informations suivantes :

   **Nome** : le nom de l’application logique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   **Emplacement** : utilisez le même emplacement que celui utilisé par votre IoT Hub.
1. Cliquez sur **Créer**.

### <a name="configure-the-logic-app"></a>Configurer l’application logique

1. Ouvrez l’application logique qui s’ouvre dans le Concepteur d’applications logiques.
1. Dans le Concepteur d’applications logiques, cliquez sur **Application logique vide**.

   ![Commencer avec une application logique vide dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Cliquez sur **Service Bus**.

   ![Sélectionner Service Bus pour commencer à créer votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Cliquez sur **Service Bus – lorsqu’un ou plusieurs messages arrivent dans une file d’attente (saisie semi-automatique)**.
1. Créez une connexion Service Bus.
   1. Entrez un nom de connexion.
   1. Cliquez sur l’espace de noms Service Bus > la stratégie Service Bus > **Créer**.

      ![Créer une connexion Service Bus pour votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Cliquez sur **Continuer** après avoir créé la connexion Service Bus.
   1. Sélectionnez la file d’attente que vous avez créée, puis entrez `175` pour **Nombre maximal de messages**

      ![Spécifier le nombre maximal de messages pour les connexions Service Bus dans votre application logique](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Cliquez sur le bouton « Enregistrer » pour enregistrer les modifications.

1. Créez une connexion de service SMTP.
   1. Choisissez **Nouvelle étape** > **Ajouter une action**.
   1. Entrez `SMTP`, cliquez sur le service **SMTP** dans les résultats de la recherche, puis cliquez sur **SMTP - Envoyer un e-mail**.

      ![Créer une connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Entrez les informations SMTP de votre boîte aux lettres, puis cliquez sur **Créer**.

      ![Entrer les informations de connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenez les informations SMTP pour [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) et [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Entrez votre adresse de messagerie pour **De** et **À**, et `High temperature detected` pour **Objet** et **Corps**.
   1. Cliquez sur **Enregistrer**.

L’application logique est en état de marche lorsque vous l’enregistrez.

## <a name="test-the-logic-app"></a>Tester l’application logique

1. Démarrez l’application cliente que vous déployez sur votre appareil dans [Connecter la carte ESP8266 à Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Faites en sorte que la température ambiante autour du SensorTag soit supérieure à 30 °C. Par exemple, allumez une bougie à côté de votre SensorTag.
1. Vous devriez recevoir un e-mail de notification envoyé par l’application logique.

   > [!NOTE]
   > Il se peut que votre fournisseur de services de messagerie doive vérifier l’identité de l’expéditeur pour s’assurer que vous êtes bien celui qui envoie l’e-mail.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé avec succès une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
