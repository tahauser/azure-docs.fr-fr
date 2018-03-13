---
title: Interroger des messages B2B dans Operations Management Suite - Azure Logic Apps | Microsoft Docs
description: "Créer des requêtes pour suivre des messages AS2, X 12 et EDIFACT dans Operations Management Suite"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bc1ea42c9fb81fe1e2a2594fda48500132cbb539
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Interroger des messages AS2, X 12 et EDIFACT dans Operations Management Suite (OMS)

Pour rechercher les messages AS2, X12 ou EDIFACT que vous suivez avec [Azure Log Analytics](../log-analytics/log-analytics-overview.md) dans [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), vous pouvez créer des requêtes qui filtrent les actions en fonction de critères spécifiques. Par exemple, vous pouvez rechercher des messages sur la base d’un numéro de contrôle d’échange spécifique.

## <a name="requirements"></a>Configuration requise

* Une application logique configurée avec une journalisation des diagnostics. Découvrez comment [créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) et comment [configurer la journalisation pour cette application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un compte d’intégration configuré avec une surveillance et une journalisation. Découvrez comment [créer un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) et comment [configurer une surveillance et une journalisation pour ce compte](../logic-apps/logic-apps-monitor-b2b-message.md).

* Si ce n’est déjà fait, [publiez des données de diagnostic sur Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) puis [configurez le suivi des messages dans OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Une fois les conditions précédentes réunies, vous devez disposer d’un espace de travail dans [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Vous devez utiliser l’espace de travail OMS que vous utilisez pour le suivi de votre communication B2B dans OMS. 
>  
> Si vous n’avez pas d’espace de travail OMS, découvrez comment [créer un espace de travail OMS](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Créer des requêtes de messages avec des filtres dans le portail Operations Management Suite

Cet exemple montre comment rechercher des messages en fonction de leur numéro de contrôle d’échange.

> [!TIP] 
> Si vous connaissez le nom de votre espace de travail OMS, accédez à la page d’accueil de votre espace de travail (`https://{your-workspace-name}.portal.mms.microsoft.com`), puis passez à l’étape 4. Autrement, commencez à l’étape 1.

1. Dans le [portail Azure](https://portal.azure.com), choisissez **Tous les services**. Recherchez « log analytics », puis choisissez **Log Analytics** comme illustré ici :

   ![Rechercher Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail OMS.

   ![Sélectionner votre espace de travail OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. Sous **Gestion**, choisissez **Portail OMS**.

   ![Choisir le portail OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. Dans votre page d’accueil d’OMS, choisissez **Recherche dans les journaux**.

   ![Dans votre page d’accueil d’OMS, choisissez « Recherche dans les journaux »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![Dans le menu OMS, choisir « Recherche dans les journaux »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. Dans la zone de recherche, complétez un champ que vous souhaitez trouver, puis appuyez sur **Entrée**. Lorsque vous commencez à taper, OMS affiche les correspondances possibles et les opérations que vous pouvez utiliser. Pour en savoir plus, voir [Recherche de données à l’aide de recherches de journal](../log-analytics/log-analytics-log-searches.md).

   Cet exemple recherche des événements avec **Type=AzureDiagnostics**.

   ![Commencer à taper la chaîne de requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. Dans la barre de gauche, choisissez la plage de temps que vous souhaitez afficher. Pour ajouter un filtre à votre requête, choisissez **+Ajouter**.

   ![Ajouter un filtre à une requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. Sous **Ajouter des filtres**, entrez le nom du filtre afin de trouver le filtre souhaité. Sélectionnez le filtre, puis choisissez **+Ajouter**.

   Pour trouver le numéro de contrôle d’échange, cet exemple recherche le mot « interchange » (échange), puis sélectionne **event_record_messageProperties_interchangeControlNumber_s** en tant que filtre.

   ![Sélectionner le filtre](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. Dans la barre de gauche, sélectionnez la valeur de filtre que vous souhaitez utiliser, puis choisissez **Appliquer**.

   Cet exemple sélectionne le numéro de contrôle d’échange pour les messages que nous voulons.

   ![Sélectionner une valeur de filtre](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Revenez à présent à la requête que vous créez. Votre requête a été mise à jour avec l’événement et la valeur de filtre sélectionnés. Vos résultats précédents sont à présent également filtrés.

    ![Revenir à votre requête avec les résultats filtrés](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Enregistrer votre requête pour un usage ultérieur

1. À partir de votre requête dans la page **Recherche dans les journaux**, choisissez **Enregistrer**. Donnez un nom à votre requête, sélectionnez une catégorie, puis choisissez **Enregistrer**.

   ![Donner un nom et une catégorie à votre requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Pour afficher votre requête, choisissez **Favoris**.

   ![Choisir « Favoris »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. Sous **Recherches enregistrées**, sélectionnez votre requête afin de pouvoir afficher les résultats. Pour mettre à jour la requête afin d’obtenir des résultats différents, modifiez la requête.

   ![Sélectionner votre requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Rechercher et exécuter des requêtes enregistrées dans le portail Operations Management Suite

1. Ouvrez la page d’accueil de votre espace de travail OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`), puis choisissez **Recherche dans les journaux**.

   ![Dans votre page d’accueil d’OMS, choisissez « Recherche dans les journaux »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   -ou-

   ![Dans le menu OMS, choisir « Recherche dans les journaux »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Dans la page d'accueil **Recherche dans les journaux**, choisissez **Favoris**.

   ![Choisir « Favoris »](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. Sous **Recherches enregistrées**, sélectionnez votre requête afin de pouvoir afficher les résultats. Pour mettre à jour la requête afin d’obtenir des résultats différents, modifiez la requête.

   ![Sélectionner votre requête](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Étapes suivantes

* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémas de suivi X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schémas de suivi personnalisé](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)