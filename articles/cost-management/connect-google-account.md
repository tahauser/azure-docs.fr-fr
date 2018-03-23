---
title: Connecter un compte Google Cloud Platform à Azure Cost Management | Microsoft Docs
description: Connectez un compte Google Cloud Platform pour afficher les données de coût et d’utilisation dans les référentiels Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 8f8c157be0a369817099afa211015ba7587017e3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Connecter un compte Google Cloud Platform

Vous pouvez connecter votre compte Google Cloud Platform existant à Azure Cost Management. Une fois votre compte connecté à Cost Management, les données de coût et d’utilisation sont disponibles dans les rapports de Cost Management. Cet article vous aide à configurer et à connecter votre compte Google à Cost Management.

## <a name="collect-project-information"></a>Collecter des informations sur le projet

Vous commencez par collecter les informations sur votre projet.

1. Connectez-vous à la console Google Cloud Platform via la page [https://console.cloud.google.com](https://console.cloud.google.com).
2. Passez en revue les informations du projet que vous souhaitez intégrer à Cost Management, puis notez le **Nom du projet** et l’**ID du projet**. Conservez les informations à portée de main pour les étapes ultérieures.  
    ![Console Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Si la facturation n’est pas activée et liée à votre projet, créez un compte de facturation. Pour plus d’informations, voir [Créer un compte de facturation](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Activer l’exportation de la facturation du compartiment de stockage

Cost Management récupère vos données de facturation Google à partir d’un compartiment de stockage. Conservez les informations **Nom du compartiment** et **Préfixe du rapport** à portée de main en vue d’un usage ultérieur lors de l’inscription de Cost Management.

L’utilisation de Google Cloud Storage pour stocker les rapports d’utilisation occasionne un minimum de frais. Pour plus d’informations, voir [Tarifs de Cloud Storage](https://cloud.google.com/storage/pricing).

1. Si vous n’avez pas activé l’exportation de la facturation vers un fichier, suivez les instructions de la page [Activer l’exportation de la facturation vers un fichier](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Vous pouvez utiliser le format d’exportation de facturation JSON ou CSV.
2. Autrement, dans la console Google Cloud Platform, accédez à **Facturation** > **Exportation de la facturation**. Notez le **Nom du compartiment** et le **Préfixe du rapport** de votre facturation.  
    ![Exportation de la facturation](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Activez les API Google Cloud Platform

Pour collecter les informations sur les actifs et l’utilisation, Cost Management a besoin que les API Google Cloud Platform suivantes soient activée :

- API BigQuery
- Google Cloud SQL
- API Google Cloud Datastore
- Google Cloud Storage
- API JSON Google Cloud Storage
- API Google Compute Engine

### <a name="enable-or-verify-apis"></a>API Activer ou Vérifier

1. Dans la console Google Cloud Platform, sélectionnez le projet que vous souhaitez inscrire auprès de Cost Management.
2. Accédez à **API et Services** > **Bibliothèque**.
3. Utilisez une recherche pour trouver chaque API.
4. Pour chaque API, vérifiez que l’option **API activée** est sélectionnée. Autrement, cliquez sur **ACTIVER**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Ajouter un compte Google Cloud à Cost Management

1. Ouvrez le portail Cloudyn à partir du Portail Azure, ou accédez à la page [https://azure.cloudyn.com](https://azure.cloudyn.com/) et connectez-vous.
2. Cliquez sur **Paramètres** (symbole de roue dentée), puis sélectionnez **Comptes cloud**.
3. Dans **Gestion de comptes**, sélectionnez l’onglet **Comptes Google**, puis cliquez sur **Ajouter un nouveau +**.
4. Dans **Nom du compte Google**, entrez l’adresse de messagerie du compte de facturation, puis cliquez sur **Suivant**.
5. Dans la boîte de dialogue d’authentification de Google, sélectionnez ou entrez un compte Google, puis choisissez d’**AUTORISER** cloudyn.com à accéder à votre compte.
6. Ajoutez les informations de projet que vous avez notées précédemment. Il s’agit de l’**ID du projet**, du **Nom du projet**, du **Nom du compartiment de facturation**, et du préfixe de rapport du **Fichier de facturation**. Cliquez ensuite sur **Enregistrer**.  
    ![Ajouter un projet Google](./media/connect-google-account/add-project.png)

Votre compte Google apparaît dans la liste des comptes, et devrait indiquer **Authentifié**. Sous celui-ci devraient apparaître le nom et l’ID de votre projet Google, ainsi qu’un symbole de coche verte. L’état du compte devrait être **Terminé**.

Après quelques heures, les rapports Cost Management affichent les informations de coût et d’utilisation de Google.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Azure Cost Management, poursuivez avec le didacticiel [Examen de l’utilisation et des coûts](./tutorial-review-usage.md) pour Cost Management.
