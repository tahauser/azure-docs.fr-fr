---
title: "Guide d’ajout d’un jeu de données de référence à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment ajouter un jeu de données de référence à votre environnement Azure Time Series Insights. Les données de référence sont utiles, joignez-les à votre source de données pour augmenter les valeurs."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7cdcefbd0daec3b7bab59680afa1470624583c74
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Créer un jeu de données de référence pour votre environnement Time Series Insights à l’aide du portail Azure

Cet article décrit comment ajouter un jeu de données de référence à votre environnement Azure Time Series Insights. Les données de référence sont utiles, joignez-les à votre source de données pour augmenter les valeurs.

Un jeu de données de référence est une collection d’éléments à laquelle s’ajoutent les événements issus de votre source d’événements. Le moteur d’entrée Time Series Insights joint un événement à partir de votre source d’événements à un élément dans votre jeu de données de référence. Cet événement ajouté est ensuite disponible pour la requête. Ce type de jointure repose sur les clés définies dans votre jeu de données de référence.

## <a name="add-a-reference-data-set"></a>Ajouter un jeu de données de référence

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights existant. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Sélectionnez votre environnement Time Series Insights.

3. Sous le titre **Topologie de l’environnement**, sélectionnez **Jeux de données de référence**.

    ![Créer le jeu de données de référence Time Series Insights](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Sélectionnez **+ Ajouter** pour ajouter un nouveau jeu de données de référence.

5. Spécifiez une valeur unique de **Nom de jeu de données de référence**.

    ![Créer le jeu de données de référence Time Series Insights - détails](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

6. Spécifiez le **Nom de la clé** dans le champ vide et sélectionnez son **Type**. Ce nom et ce type sont utilisés pour récupérer la propriété appropriée à partir de l’événement dans votre source d’événement pour joindre les données de référence. 

   Par exemple, si vous indiquez le nom de clé **DeviceId** et le type **String**, le moteur d’entrée Time Series Insights recherche une propriété portant le nom **DeviceId** et de type **String** dans l’événement entrant à trouver et joindre. Vous pouvez indiquer plusieurs clés à joindre à l’événement. Le nom de la clé est sensible à la casse.

7. Sélectionnez **Créer**.

## <a name="next-steps"></a>Étapes suivantes
* [Gérez les données de référence](time-series-insights-manage-reference-data-csharp.md) par programme.
* Pour obtenir des informations de référence d’API complètes, consultez le document [API de données de référence](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
