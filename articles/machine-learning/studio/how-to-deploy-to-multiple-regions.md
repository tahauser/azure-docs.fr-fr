---
title: Déployer un service web dans plusieurs régions | Microsoft Docs
description: Étapes pour déployer (copier) un nouveau service web dans d’autres régions.
services: machine-learning
documentationcenter: ''
author: aashishb
manager: hjerez
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: aashishb
ms.openlocfilehash: 68ebd388b46199710cbe8f314b705e4733e2440e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Comment déployer un service web dans plusieurs régions
Les nouveaux services web Azure vous permettent de déployer facilement un service web dans plusieurs régions, sans avoir besoin de plusieurs abonnements ou espaces de travail. 

Le tarif est spécifique à chaque région et vous devez donc définir un profil de facturation pour chaque région dans laquelle vous allez déployer le service web.

## <a name="to-create-a-plan-in-another-region"></a>Pour créer un profil dans une autre région
1. Connectez-vous aux [services web Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Cliquez sur l’option de menu **Abonnements** .
3. Sur la page de présentation des abonnements, cliquez sur **nouveau**.
4. Dans la liste déroulante **Abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau plan.
5. Dans la liste déroulante **Région** , sélectionnez une région pour le nouveau plan. Les options du plan de la région sélectionnée apparaissent dans la section **Plan Options** (Option du plan) de la page.
6. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le plan. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. Dans **Plan Name** (Nom du plan), tapez le nom du plan.
8. Sous **Plan Options**(Options du plan), cliquez sur le niveau de facturation du nouveau plan.
9. Cliquez sur **Créer**.

## <a name="deploying-the-web-service-to-another-region"></a>Déploiement du service web dans une autre région
1. Cliquez sur l’option de menu **Services web** .
2. Sélectionnez le service web que vous déployez dans une nouvelle région.
3. Cliquez sur **Copy**.
4. Dans **Nom du service web**, tapez le nouveau nom du service web.
5. Dans **Description du service web**, tapez une description du service web.
6. Dans la liste déroulante **Abonnement** , sélectionnez l’abonnement dans lequel résidera le nouveau service web.
7. Dans la liste déroulante **Groupe de ressources** , sélectionnez un groupe de ressources pour le service web. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. Dans la liste déroulante **Région** , sélectionnez la région dans laquelle vous voulez déployer le service web.
9. Dans la liste déroulante **Compte de stockage** , sélectionnez un compte de stockage où stocker le service web.
10. Dans la liste déroulante **Plan de tarification** , sélectionnez un plan dans la région que vous avez sélectionnée à l’étape 8.
11. Cliquez sur **Copy**.

