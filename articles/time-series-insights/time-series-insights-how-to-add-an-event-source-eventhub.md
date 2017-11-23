---
title: "Comment ajouter une source d’événement Event Hubs à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment ajouter une source d’événement qui est connectée à un Event Hub à votre environnement Time Series Insights."
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
ms.openlocfilehash: f3a9a1c7e57383925877f674a2e02f931e5c1e3c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Ajout d’une source d’événement de Event Hub à l’environnement Time Series Insights

Cet article décrit comment utiliser le portail Azure pour ajouter une source d’événements qui lit des données à partir d’un Event Hub dans votre environnement Time Series Insights.

## <a name="prerequisites"></a>Composants requis
- Créez un environnement Time Series Insights. Pour plus d’informations, consultez [Créer un environnement Azure Time Series Insights](time-series-insights-get-started.md) 
- Créez un concentrateur d’événements. Pour plus d’informations sur Event Hubs, consultez [Créer un espace de noms Event Hubs et un hub d’événements à l’aide du portail Azure](../event-hubs/event-hubs-create.md)
- Le concentrateur d’événements doit avoir des événements de message actifs envoyés. Pour plus d’informations, consultez [Envoyer des événements vers Azure Event Hubs à l’aide de .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Créez un groupe de consommateurs dédié dans le concentrateur d’événements pour l’environnement Time Series Insight à utiliser. Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié qui n’est pas partagé avec les autres consommateurs. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d’informations, consultez [Guide de programmation Event Hubs](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Ajouter une nouvelle source d’événement
1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights existant. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

3. Sous le titre **Topologie de l’environnement**, cliquez sur **Sources d’événements**.

   ![Sources d’événement + Ajout](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Cliquez sur **+ Ajouter**.

5. Fournissez un **nom source d’événement** unique à cet environnement Time Series Insights, comme **event-stream**.

   ![Remplissez les trois premiers paramètres du formulaire.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Pour **Source**, sélectionnez **Concentrateur d’événements**.

7. Sélectionnez **l’option d’importation** appropriée. 
   - Choisissez **Utiliser un concentrateur d’événements à partir d’abonnements disponibles** lorsque vous possédez déjà un concentrateur d’événements existant sur l’un de vos abonnements. Il s’agit de l’approche la plus simple.
   - Choisissez **Fournir des paramètres Event Hub manuellement** lorsque le concentrateur d’événements est externe à vos abonnements, ou que vous souhaitez choisir des options avancées. 

8. Si vous avez sélectionné l’option **Fournir des paramètres Event Hub à partir d’abonnements disponibles**, le tableau suivant décrit chaque propriété requise :

   ![Détails de l’abonnement et du concentrateur d’événements](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Propriété | Description |
   | --- | --- |
   | ID d’abonnement | Sélectionnez l’abonnement dans lequel ce Event Hub a été créé.
   | Espace de noms Service Bus | Sélectionnez l’espace de noms Service Bus qui contient le Event Hub.
   | Nom du hub d’événements | Sélectionnez le nom du Event Hub.
   | Nom de la stratégie du hub d’événements | Sélectionnez la stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
   | Clé de la stratégie du Event Hub | La valeur de clé peut être prédéfinie.
   | Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements du Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements. |
   | Format de sérialisation de l’événement | JSON est la seule sérialisation disponible à l’heure actuelle. Les messages d’événement doivent être au format suivant, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format du message des données de message envoyées dans le concentrateur d’événements. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message que vous souhaitez utiliser en tant qu’horodatage d’un événement. La valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** au sein de l’événement source est utilisée en tant qu’horodatage d’événement. |


9. Si vous avez sélectionné l’option **Fournir des paramètres Event Hub manuellement**, le tableau suivant décrit chaque propriété requise :

   | Propriété | Description |
   | --- | --- |
   | Identifiant d’abonnement | Abonnement dans lequel ce Event Hub a été créé.
   | Groupe de ressources | Le groupe de ressources dans lequel ce Event Hub a été créé.
   | Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un hub d’événements, vous avez également créé un espace de noms Service Bus.
   | Nom du hub d’événements | Nom de votre Event Hub Lorsque vous avez créé votre Event Hub, vous lui avez également donné un nom spécifique.
   | Nom de la stratégie du hub d’événements | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
   | Clé de la stratégie du Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus Tapez la clé primaire ou secondaire ici.
   | Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements du Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.
   | Format de sérialisation de l’événement | JSON est la seule sérialisation disponible à l’heure actuelle. Les messages d’événement doivent être au format suivant, sans quoi aucune donnée ne peut être lue. |
   | Nom de la propriété d’horodatage | Pour déterminer cette valeur, vous devez comprendre le format du message des données de message envoyées dans le concentrateur d’événements. Cette valeur est le **nom** de la propriété d’événement spécifique dans les données de message que vous souhaitez utiliser en tant qu’horodatage d’un événement. La valeur respecte la casse. Lorsque ce champ est vide, **l’heure de mise en file d’attente de l’événement** au sein de l’événement source est utilisée en tant qu’horodatage d’événement. |


10. Sélectionnez **Créer** pour ajouter la nouvelle source d’événements.
   
   ![Click Create](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion de données dans votre environnement.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Ajouter un groupe de consommateurs à vitre concentrateur d’événements
Les groupes de consommateurs sont utilisés par les applications pour extraire des données Azure Event Hubs. Fournissez un groupe de consommateurs dédiés, qui sera utilisé par cet environnement Time Series Insights uniquement, pour lire les données de manière fiable à partir de votre concentrateur d’événements.

Pour ajouter un nouveau groupe de consommateurs dans votre concentrateur d’événements, procédez comme suit :
1. Dans le portail Azure, recherchez et ouvrez votre concentrateur d’événements.

2. Sous le titre **Entités**, sélectionnez **Groupes de consommateurs**.

   ![Concentrateur d’événements - Ajouter un groupe de consommateurs](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Sélectionnez **+ Groupe de consommateurs** pour ajouter un nouveau groupe de consommateurs. 

4. Sur la page **Groupes de consommateurs**, fournissez un nouveau **nom** unique.  Utilisez le même nom lors de la création d’une source d’événement dans l’environnement Time Series Insights.

5. Sélectionnez **Créer** pour créer le nouveau groupe de consommateurs.

## <a name="next-steps"></a>Étapes suivantes
- [Définissez les stratégies d’accès aux données](time-series-insights-data-access.md) pour sécuriser les données.
- [Envoyez des événements](time-series-insights-send-events.md) à la source d’événement.
- Accéder à votre environnement sur [l’Explorateur Time Series Insights](https://insights.timeseries.azure.com).
