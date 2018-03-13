---
title: "Suspendre et démarrer une capacité Power BI Embedded sur le Portail Azure | Microsoft Docs"
description: "Cet article explique comment suspendre et démarrer une capacité Power BI Embedded dans Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: a7f86ebf3e79812eb50e58cbb320336cbd1149e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>Suspendre et démarrer une capacité Power BI Embedded sur le Portail Azure

Cet article explique comment suspendre et démarrer une capacité Power BI Embedded dans Microsoft Azure. Cela suppose d’avoir créé une capacité Power BI Embedded. Dans le cas contraire, consultez la page [Créer une capacité Power BI Embedded sur le Portail Azure](create-capacity.md) pour démarrer.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="pause-your-capacity"></a>Suspendre une capacité

La suspension de la capacité permet d’éviter d’être facturé. C’est idéal si vous n’avez pas besoin d’utiliser cette capacité pendant un certain temps. Suivez les étapes ci-dessous pour suspendre votre capacité.

> [!NOTE]
> Suspendre une capacité risque de gêner l’accès au contenu dans Power BI. Veillez à supprimer l’affectation des espaces de travail à votre capacité avant la suspension pour éviter toute interruption.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Tous les services** > **Power BI Embedded** pour voir vos capacités.

    ![Tous les services dans le portail Azure](media/pause-start/azure-portal-more-services.png)

3. Sélectionnez la capacité que vous souhaitez suspendre.

    ![Liste des capacités Power BI Embedded sur le Portail Azure](media/pause-start/azure-portal-capacity-list.png)

4. Sélectionnez **Suspendre** dans les détails de la capacité.

    ![Suspendre une capacité](media/pause-start/azure-portal-pause-capacity.png)

5. Sélectionnez **Oui**, pour confirmer que vous voulez suspendre la capacité.

    ![Confirmer la pause](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>Démarrer une capacité

Retrouvez l’usage de votre capacité en la démarrant. Le démarrage de la capacité reprend également la facturation.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Tous les services** > **Power BI Embedded** pour voir vos capacités.

    ![Tous les services dans le portail Azure](media/pause-start/azure-portal-more-services.png)

3. Sélectionnez la capacité que vous souhaitez démarrer.

    ![Liste des capacités Power BI Embedded sur le Portail Azure](media/pause-start/azure-portal-capacity-list.png)

4. Sélectionnez **Démarrer** dans les détails de la capacité.

    ![Démarrer une capacité](media/pause-start/azure-portal-start-capacity.png)

5. Sélectionnez **Oui**, pour confirmer que vous voulez démarrer la capacité.

    ![Confirmer le démarrage](media/pause-start/azure-portal-confirm-start.png)

Si du contenu est affecté à cette capacité, il sera disponible après le démarrage.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez monter ou descendre en puissance votre capacité, consultez la page [Mettre à l’échelle une capacité Power BI Embedded](scale-capacity.md).

Pour commencer l’incorporation de contenus Power BI dans une application, consultez la page [Guide pratique pour incorporer vos tableaux de bord, rapports et vignettes Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Des questions ? [Essayer d’interroger la Communauté Power BI](http://community.powerbi.com/).