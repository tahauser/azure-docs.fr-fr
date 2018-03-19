---
title: "Inscrire votre abonnement Azure auprès du service Gestion des coûts Azure | Microsoft Docs"
description: "Utilisez votre abonnement Azure pour vous inscrire auprès du service Azure Cost Management."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: b7c1fee3276d98b6b7c611a09133804c1253384a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Inscrire un abonnement Azure individuel et afficher les données de coût

Vous utilisez votre abonnement Azure pour vous inscrire auprès du service Azure Cost Management. Votre inscription vous donne accès au portail Cloudyn. Ce guide de démarrage rapide décrit le processus d’inscription nécessaire pour créer un abonnement d’évaluation Cloudyn et vous connecter au portail Cloudyn. Il explique également comment afficher immédiatement des données de coût.

## <a name="log-in-to-azure"></a>Connexion à Azure

- Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Créer une inscription d’évaluation

1. Dans le portail Azure, cliquez sur **Gestion des coûts + Facturation** dans la liste des services.
2. Sous **Vue d’ensemble**, cliquez sur **Gestion des coûts**.  
    ![Page Gestion des coûts](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. Sur la page **Gestion des coûts**, cliquez sur le bouton d’**accès à la Gestion des coûts** pour ouvrir la page d’inscription de Cloudyn dans une nouvelle fenêtre.
4. Sur la page d’inscription à une évaluation du portail Cloudyn, tapez le nom de votre société, sélectionnez **Azure Individual Subscription Owner** (Propriétaire d’abonnement individuel Azure), puis cliquez sur **Next** (Suivant). Vos nom de compte et ID de client sont automatiquement ajoutés au formulaire.  
    ![inscription à une évaluation](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Sélectionnez les **Offer ID - Name** (ID d’offre - Nom) associés à votre abonnement. Si vous ignorez l’ID taux de votre abonnement, vous pouvez consulter votre facture Azure et rechercher **ID d’offre**.
6. Acceptez les conditions d’utilisation, validez vos informations, puis cliquez sur **Next** (Suivant).
7. Dans la page **Gather additional data**, (Collecter des données supplémentaires) cliquez sur **Next** (Suivant) pour autoriser Cloudyn à collecter des données concernant les ressources Azure. Les données collectées incluent les données d’utilisation, de performances, de facturation et de balise de vos abonnements.  
    ![collecter des données supplémentaires](./media/quick-register-azure-sub/gather-additional.png)
8. Votre navigateur vous conduit à la page de connexion à Cloudyn. Connectez-vous avec les informations d’identification de votre abonnement Azure.
9. Cliquez sur **Go to Cloudyn** (Accéder à Cloudyn) pour ouvrir le portail Cloudyn, puis, dans la page **Accounts Management** (gestion des comptes), vous devriez voir les informations de compte de votre abonnement Azure.  
    ![Gestion des comptes](./media/quick-register-azure-sub/accounts-mgt.png)

Pour regarder un didacticiel vidéo sur l’inscription de votre abonnement Azure, consultez l’article [Comment trouver le GUID de répertoire et l’ID taux à utiliser dans Azure Cost Management](https://youtu.be/PaRjnyaNGMI).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé les informations de votre abonnement Azure pour vous inscrire au service Gestion des coûts. Vous vous êtes également connecté au portail Cloudyn et avez affiché des données de coût. Pour en savoir plus sur Azure Cost Management, poursuivez avec le didacticiel sur Cost Management.

> [!div class="nextstepaction"]
> [Réviser l’utilisation et les coûts](./tutorial-review-usage.md)
