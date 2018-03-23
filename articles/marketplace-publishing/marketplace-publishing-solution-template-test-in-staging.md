---
title: Test de votre offre de modèle de solution pour Marketplace | Microsoft Docs
description: Découvrez comment tester votre offre de modèle de solution pour Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: mbaldwin
ms.openlocfilehash: e789d0996e72c935ed9d5f456f9868b73d5ef4ee
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="test-your-solution-template-offer-in-staging"></a>Test de votre offre de modèle de solution en mode intermédiaire
Dans le cadre du déploiement dans un environnement intermédiaire, vous déployez votre offre dans un « bac à sable » (sandbox) privé dans lequel vous pouvez tester et vérifier ses fonctionnalités avant le lancement de sa production. L'offre apparaît avec le statut Intermédiaire, comme pour un client l'ayant déployée. Votre offre doit être certifiée pour passer en mode intermédiaire.

Une fois que votre offre a le statut Intermédiaire, vous pouvez l’afficher et la tester dans le [portail Azure](https://portal.azure.com/).

Suivez les étapes ci-dessous pour faire passer votre offre en mode Intermédiaire et la tester dans le [portail Azure](https://portal.azure.com/):

1. Accédez au [portail de publication](https://publish.windowsazure.com) > **Modèles de solution** > votre offre > **Publier** > **Push to Staging (Déployer dans un environnement intermédiaire)**.
2. Fournissez la liste des abonnements Azure que vous utiliserez pour l'aperçu et le test de votre offre.
3. Connectez-vous au portail Azure en version préliminaire à l'aide de l'ID d'abonnement utilisé dans l'étape précédente.
4. Effectuez au moins une série de tests des points mentionnés ci-dessous dans le portail Azure en version préliminaire.
   * Assurez-vous que le contenu marketing s'affiche correctement sur Azure Marketplace.
   * Réalisez un déploiement de bout en bout de la topologie.
   * Réalisez des tests de performances et de contrainte.
   * Assurez-vous que votre topologie respecte les meilleures pratiques.

## <a name="next-steps"></a>Étapes suivantes
Si vous êtes satisfait des résultats, vous pouvez passer à la phase finale de publication de l’offre, **Étape 4** : [Déploiement de votre offre dans Marketplace](marketplace-publishing-push-to-production.md). Sinon, apportez les modifications nécessaires à votre offre et effectuez de nouveau une demande de certification.

> [!NOTE]
> En cas de modifications de contenus marketing, la certification n'est pas nécessaire.
> 
> 

Consultez [Prise en main : Publier une offre dans Azure Marketplace](marketplace-publishing-getting-started.md) pour obtenir un guide sur toutes les tâches du serveur de publication.

