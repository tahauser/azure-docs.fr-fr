---
title: "Enregistrer des messages IoT Hub dans le stockage de données Azure | Microsoft Docs"
description: "Utilisez le routage de messages IoT Hub pour enregistrer les messages IoT Hub dans votre stockage Blob Azure. Les messages IoT Hub contiennent des informations, notamment des données de capteurs, envoyées par l’appareil IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "stockage de données iot, stockage de données de capteur iot"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: f6b334dbc9903d0080b74052062de7564aa4a993
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Enregistrer les messages IoT Hub qui contiennent des données de capteurs dans un stockage Blob Azure

![Diagramme de bout en bout](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Vous allez découvrir comment créer un compte de stockage Azure et une application de fonction Azure pour stocker des messages IoT Hub dans votre stockage d’objets blob.

## <a name="what-you-do"></a>Procédure

- Créez un compte de stockage Azure.
- Préparez votre IoT Hub pour router les messages vers le stockage.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- [Configurez votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) de façon à couvrir les exigences suivantes :
  - Un abonnement Azure actif
  - Un hub IoT associé à votre abonnement 
  - Une application fonctionnelle qui envoie des messages à votre hub IoT

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Créer une ressource** > **Stockage** > **Compte de stockage** > **Créer**.

2. Entrez les informations nécessaires sur le compte de stockage :

   ![Créer un compte de stockage sur le Portail Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Name** : nom du compte de stockage. Le nom doit être globalement unique.

   * **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   * **Épingler au tableau de bord** : Sélectionnez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

3. Cliquez sur **Créer**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Préparer l’IoT Hub pour router les messages vers le stockage

IoT Hub en mode natif prend en charge le routage des messages vers le stockage Azure en tant qu’objets blob.

### <a name="add-storage-as-a-custom-endpoint"></a>Ajouter un stockage en tant qu’un point de terminaison personnalisé

Accédez à votre IoT Hub dans le portail Azure. Cliquez sur **Points de terminaison** > **Ajouter**. Nommez le point de terminaison et sélectionnez **Conteneur de stockage Azure** comme type de point de terminaison. Utilisez le sélecteur pour sélectionner le compte de stockage que vous avez créé dans la section précédente. Créez un conteneur de stockage et sélectionnez-le, puis cliquez sur **OK**.

  ![Créer un point de terminaison personnalisé dans IoT Hub](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Ajouter un itinéraire pour router les données vers le stockage

Cliquez sur **Itinéraires** > **Ajouter** et attribuez un nom à l’itinéraire. Sélectionnez **Messages des appareils** comme source de données, puis sélectionnez le point de terminaison de stockage que vous venez de créer en tant que point de terminaison de l’itinéraire. Entrez `true` en tant que chaîne de requête, puis cliquez sur **Enregistrer**.

  ![Créer un itinéraire dans IoT Hub](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Ajouter un itinéraire pour la télémétrie de chemin réactif (facultatif)

Par défaut, IoT Hub achemine tous les messages qui ne correspondent à aucun autre itinéraire vers le point de terminaison intégré. Étant donné que tous les messages de télémétrie correspondent désormais à la règle qui achemine les messages vers le stockage, vous devez ajouter un autre itinéraire pour les messages à écrire au point de terminaison intégré. Aucun frais supplémentaire n’est appliqué pour acheminer les messages vers plusieurs points de terminaison.

> [!NOTE]
> Vous pouvez ignorer cette étape si vous n’effectuez aucun traitement supplémentaire de vos messages de télémétrie.

Cliquez sur **Ajouter** dans le volet Itinéraires et attribuez un nom à l’itinéraire. Sélectionnez **Messages des appareils** comme source de données et **events** comme point de terminaison. Entrez `true` en tant que chaîne de requête, puis cliquez sur **Enregistrer**.

  ![Créer un itinéraire de chemin réactif dans IoT Hub](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Vérifier votre message dans votre conteneur de stockage

1. Exécutez l’exemple d’application sur votre appareil pour envoyer des messages à votre IoT Hub.

2. [Téléchargez et installez l’Explorateur de Stockage Azure](http://storageexplorer.com/).

3. Ouvrez l’Explorateur de Stockage, cliquez sur **Ajouter un compte Azure** > **Se connecter**, puis connectez-vous à votre compte Azure.

4. Cliquez sur votre abonnement Azure > **Comptes de stockage** > votre compte de stockage > **Conteneurs d’objets blob** > votre conteneur.

   Vous devriez voir les messages envoyés par votre appareil à votre IoT Hub consignés dans le conteneur d’objets blob.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de créer votre compte de stockage Azure et d’acheminer les messages à partir de IoT Hub vers un conteneur d’objets blob de ce compte de stockage.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
