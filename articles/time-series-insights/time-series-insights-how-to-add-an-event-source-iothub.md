---
title: "Comment ajouter une source d’événement IoT Hub à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment ajouter une source d’événement connectée à un IoT Hub à votre environnement Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: ed31a0e725d1e0863e9c4695d4eccb324f60678a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Ajout d’une source d’événement de IoT Hub à l’environnement Time Series Insights
Cet article décrit comment utiliser le Portail Azure pour ajouter une source d’événement qui lit des données à partir d’un IoT Hub dans votre environnement Time Series Insights.

## <a name="prerequisites"></a>Composants requis
- Créez un environnement Time Series Insights. Pour plus d’informations, consultez [Créer un environnement Azure Time Series Insights](time-series-insights-get-started.md). 
- Créez un IoT Hub. Pour plus d’informations sur les IoT Hubs, consultez [Création d’un IoT Hub à l’aide du portail Azure](../iot-hub/iot-hub-create-through-portal.md).
- L’IoT Hub doit avoir des événements de message actifs envoyés.
- Créez un groupe de consommateurs dédié dans IoT Hub pour l’environnement Time Series Insight à utiliser. Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié, qui n’est pas partagé avec d’autres consommateurs. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Ajouter une nouvelle source d’événement
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights existant. Cliquez sur **Toutes les ressources** dans le menu de gauche du Portail Azure. Sélectionnez votre environnement Time Series Insights.

3. Sous le titre **Topologie de l’environnement**, cliquez sur **Sources d’événements**.
   ![Sources d’événements + Ajouter](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Cliquez sur **+ Ajouter**.

5. Sous **Nom de la source d’événement**, indiquez un nom unique à cet environnement Time Series Insights, comme **event-stream**.

   ![Renseignez les trois premiers paramètres du formulaire.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Sous **Source**, sélectionnez **IoT Hub**.

7. Sélectionnez l’option appropriée sous **Option d’importation**. 
   - Choisissez **Utiliser IoT Hub à partir des abonnements disponibles** si vous possédez déjà un Iot Hub existant sur l’un de vos abonnements. Il s’agit de l’approche la plus simple.
   - Choisissez **Fournir les paramètres d’IoT Hub manuellement** si l’IoT Hub est externe à vos abonnements ou si vous souhaitez choisir des options avancées. 

8. Si vous avez sélectionné l’option **Utiliser IoT Hub à partir des abonnements disponibles**, reportez-vous au tableau suivant, qui explique chaque propriété requise :

   ![Détails sur l’abonnement et Event Hub](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Propriété | Description |
   | --- | --- |
   | Identifiant d’abonnement | Sélectionnez l’abonnement dans lequel cet IoT Hub a été créé.
   | Nom de l’IoT Hub | Sélectionnez le nom de l’IoT Hub.
   | Nom de la stratégie IoT Hub | Sélectionnez la stratégie d’accès partagé, qui se trouve sous l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
   | Clé de stratégie IoT Hub | La clé est déjà renseignée.
   | Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements de l’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.
   | Format de sérialisation de l’événement | JSON est la seule sérialisation disponible à l’heure actuelle. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans IoT Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** dans la source d’événement est utilisée comme horodateur de l’événement. |

9. Si vous avez sélectionné l’option **Fournir les paramètres d’IoT Hub manuellement**, reportez-vous au tableau suivant, qui décrit chaque propriété requise :

   | Propriété | Description |
   | --- | --- |
   | Identifiant d’abonnement | Abonnement dans lequel cet IoT hub a été créé.
   | Groupe de ressources | Nom du groupe de ressources dans lequel cet IoT Hub a été créé.
   | Nom de l’IoT Hub | Nom de votre IoT Hub Lorsque vous avez créé votre IoT Hub, vous lui avez également donné un nom spécifique.
   | Nom de la stratégie IoT Hub | Stratégie d’accès partagé, qui peut être créée dans l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
   | Clé de stratégie IoT Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus. Tapez la clé primaire ou secondaire ici.
   | Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements de l’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.
   | Format de sérialisation de l’événement | JSON est la seule sérialisation disponible à l’heure actuelle. Les messages d’événement doivent respecter ce format, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format de message des données de message envoyées dans IoT Hub. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message à utiliser comme horodateur de l’événement. Cette valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** dans la source d’événement est utilisée comme horodateur de l’événement. |

10. Sélectionnez **Créer** pour ajouter la nouvelle source d’événement.

   ![Click Create](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion de données dans votre environnement.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Ajouter un groupe de consommateurs à votre instance IoT Hub
Les groupes de consommateurs sont utilisés par les applications pour extraire des données des IoT Hubs. Indiquez un groupe de consommateurs dédiés, qui sera utilisé par cet environnement Time Series Insights uniquement, pour lire les données de manière fiable à partir de votre IoT Hub.

Pour ajouter un nouveau groupe de consommateurs à votre IoT Hub, procédez comme suit :
1. Dans le Portail Azure, recherchez et ouvrez votre IoT Hub.

2. Sous le titre **Messagerie**, sélectionnez **Points de terminaison**. 

   ![Ajouter un groupe de consommateurs](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Sélectionnez le point de terminaison **Événements**. La page **Propriétés** s’affiche.

4. Sous le titre **Groupes de consommateurs**, indiquez un nouveau nom unique pour le groupe de consommateurs. Utilisez le même nom dans l’environnement Time Series Insights lors de la création d’une source d’événement.

5. Sélectionnez **Enregistrer** pour enregistrer le nouveau groupe de consommateurs.

## <a name="next-steps"></a>Étapes suivantes
- [Définissez les stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser les données.
- [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.
- Accédez à votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
