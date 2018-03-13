---
title: "Configurer la sécurité pour accéder et gérer Azure Time Series Insights | Microsoft Docs"
description: "Cet article explique comment configurer la sécurité et les autorisations sous forme de stratégies d’accès de gestion et d’accès aux données pour sécuriser Azure Time Series Insights."
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
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Accorder l’accès aux données d’un environnement Time Series Insights à l’aide du portail Azure

Les environnements Time Series Insights comprennent deux types indépendants des stratégies d’accès :

* Stratégies d’accès de gestion
* Stratégies d’accès aux données

Ces deux stratégies accordent aux principaux Azure Active Directory (utilisateurs et applications) diverses autorisations sur un environnement spécifique. Les principaux (utilisateurs et applications) doivent appartenir à l’annuaire Active Directory (ou « locataire Azure ») associé à l’abonnement contenant l’environnement.

Les stratégies d’accès de gestion accordent des autorisations liées à la configuration de l’environnement, telles que la
*   création et la suppression de l’environnement, des sources d’événements, des ensembles de données de référence et la
*   gestion des stratégies d’accès aux données.

Les stratégies d’accès aux données accordent des autorisations pour générer des requêtes de données, manipuler des données de référence dans l’environnement, et des requêtes partagées enregistrées et des perspectives associées à l’environnement.

Les deux types de stratégies permettent de distinguer clairement l’accès à la gestion de l’environnement et l’accès aux données contenues dans l’environnement. Par exemple, vous pouvez configurer un environnement de sorte que le propriétaire/créateur de l’environnement soit supprimé de l’accès aux données. Par ailleurs, les utilisateurs et services qui sont autorisés à lire des données de l’environnement peuvent ne pas avoir accès à la configuration de l’environnement.

## <a name="grant-data-access"></a>Accorder l’accès aux données
Effectuez les étapes suivantes pour accorder l’accès aux données à un utilisateur principal :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Recherchez votre environnement Time Series Insights. Tapez **Time Series** dans la zone de **recherche**. Sélectionnez **Environnement Time Series** dans les résultats de recherche. 

3. Sélectionnez votre environnement Time Series Insights dans la liste.
   
4. Sélectionnez **Stratégies d’accès aux données**, puis **+Ajouter**.
  ![Gérer la source Time Series Insights - Environnement](media/data-access/getstarted-grant-data-access1.png)

5. Sélectionnez **Sélectionner un utilisateur**.  Recherchez le nom d’utilisateur ou l’adresse e-mail pour accéder à l’utilisateur que vous voulez ajouter. Cliquez sur **Sélectionner** pour confirmer la sélection. 

   ![Gérer la source Time Series Insights - Ajouter](media/data-access/getstarted-grant-data-access2.png)

6. Sélectionnez **Sélectionner un rôle**. Choisissez le rôle d’accès approprié pour l’utilisateur :
   - Sélectionnez **Contributeur** si vous voulez autoriser l’utilisateur à changer les données de référence et à partager des requêtes et des perspectives enregistrées avec d’autres utilisateurs de l’environnement. 
   - Sinon, sélectionnez **Lecteur** pour autoriser l’utilisateur à interroger les données dans l’environnement et à enregistrer des requêtes personnelles (non partagées) dans l’environnement.

   Sélectionnez **OK** pour confirmer le choix du rôle.

   ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/data-access/getstarted-grant-data-access3.png)

8. Sélectionnez **OK** dans la page **Sélectionner un rôle utilisateur**.

   ![Gérer la source Time Series Insights - Sélectionner un rôle](media/data-access/getstarted-grant-data-access4.png)

9. La page **Stratégies d’accès aux données** répertorie les utilisateurs, et le ou les rôles pour chaque utilisateur.

   ![Gérer la source Time Series Insights - Résultats](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez le [Guide pratique pour ajouter une source d’événement Event Hub à votre environnement Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement.
* Affichez votre environnement dans [l’explorateur Time Series Insights](https://insights.timeseries.azure.com).
