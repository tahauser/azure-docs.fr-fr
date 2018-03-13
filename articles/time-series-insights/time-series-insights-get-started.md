---
title: "Créer un environnement Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment utiliser le portail Azure pour créer un nouvel environnement Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 20156432e17d5eca90779271bd18dc49fa988d7c
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Créer un nouvel environnement de Time Series Insights dans le portail Azure
Cet article décrit comment créer un nouvel environnement Time Series Insights via le portail Azure.

Time Series Insights vous permet de commencer à visualiser et interroger les données qui circulent dans Azure IoT Hubs et Event Hubs en quelques minutes, ce qui vous permet d’interroger de grands volumes de données de série chronologique en quelques secondes.  Cette solution a été conçue pour l’échelle de l’Internet des objets et peut gérer plusieurs téraoctets de données.

## <a name="steps-to-create-the-environment"></a>Étapes pour créer l’environnement
Procédez comme suit pour créer un environnement :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com).

2.  Sélectionner le bouton **+ New**.

3.  Sélectionnez la catégorie **Internet des objets**, puis **Time Series Insights**.

   ![Créer l’environnement Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  Sur la page **Time Series Insights**, sélectionnez **Créer**.

5. Renseignez les paramètres requis. Le tableau suivant décrit chaque paramètre :
   
   ![Créer le groupe de ressources Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Paramètre|Valeur suggérée|Description
   ---|---|---
   Nom de l’environnement | Un nom unique | Ce nom représente l’environnement dans [l’explorateur time series](https://insights.timeseries.azure.com)
   Abonnement | Votre abonnement | Si vous avez plusieurs abonnements, choisissez l’abonnement qui contient votre source d’événements de préférence. Time Series Insights peut détecter automatiquement les ressources Azure IoT Hub et Concentrateur d’événements existant dans le même abonnement.
   Groupe de ressources | Créer un nouveau groupe ou en choisir un existant | Un groupe de ressources correspond à une collection de ressources Azure utilisées ensemble. Vous pouvez choisir un groupe de ressources existant, par exemple, celui qui contient votre concentrateur d’événements ou un IoT Hub. Ou vous pouvez en créer un nouveau si cette ressource n’est pas liée aux autres ressources.
   Lieu | Le plus près possible de votre source d’événements | De préférence, choisissez le même emplacement que pour le centre de données qui contient vos données de source d’événements, dans le but d’éviter les coûts de bande passante supplémentaires entre régions et zones et l’augmentation de la latence lors du déplacement des données en dehors de la région.
   Niveau tarifaire | S1 | Choisissez le débit nécessaire. Pour des coûts inférieurs et une capacité de démarrage, sélectionnez S1.
   Capacity | 1 | La capacité est le multiplicateur qui s’applique à la vitesse d’entrée, à la capacité de stockage et au coût associé à la référence (SKU) sélectionnée.  Vous pouvez modifier la capacité d’un environnement après sa création. Pour obtenir des coûts plus bas, sélectionnez une capacité de 1. 
  
6. Cochez **Épingler au tableau de bord** pour accéder plus facilement à votre environnement Time Series à l’avenir.

   ![Créer l’épingle Time Series Insights sur le tableau de bord](media/time-series-insights-get-started/3-pin-create.png)

7. Sélectionnez **Créer** pour commencer le processus d’approvisionnement. Cela peut prendre quelques minutes.

8. Sélectionnez le symbole de **Notifications** (en forme de cloche) pour surveiller le processus de déploiement.

   ![Regarder les notifications](media/time-series-insights-get-started/4-notifications.png)

Lorsque le déploiement réussit, vous pouvez sélectionner **Aller à la ressource** pour configurer d’autres propriétés, définir la sécurité avec les stratégies d’accès aux données, ajouter les sources d’événements et d’autres actions.

## <a name="next-steps"></a>étapes suivantes
* [Définissez des stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser votre environnement.
* [Ajoutez une source d’événement Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) à votre environnement Azure Time Series Insights. 
* [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.
* Affichez votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
