---
title: Entonnoirs Azure Application Insights
description: "Apprenez à utiliser les entonnoirs pour découvrir de quelle façon les clients interagissent avec votre application."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Découvrez comment les clients utilisent votre application avec les entonnoirs Application Insights

Comprendre l’expérience de vos utilisateurs est primordial pour votre entreprise. Si votre application implique plusieurs étapes, vous devrez savoir si la plupart des clients vont au bout du processus, ou s’ils arrêtent celui-ci à un moment donné. La progression via une série d’étapes dans une application web est appelée « entonnoir ». Vous pouvez utiliser les entonnoirs Application Insights pour obtenir des informations sur vos utilisateurs et suivre les taux de conversion étape par étape. 

## <a name="create-your-funnel"></a>Créer votre entonnoir
Avant de créer votre entonnoir, vous devez choisir la question à laquelle vous souhaitez répondre. Par exemple, vous souhaitez peut-être savoir combien d’utilisateurs sont en train d’afficher la page d’accueil, de consulter le profil d’un client ou de créer un ticket. Dans cet exemple, les propriétaires de la société Fabrikam Fiber souhaitent connaître le pourcentage de clients créant correctement un ticket client.

Voici les étapes qu’ils suivent pour créer leur entonnoir.

1. Cliquez sur le bouton Nouveau de l’outil Entonnoirs.
1. Sélectionnez l’intervalle de temps « 90 derniers jours » dans la liste déroulante **Intervalle de temps**. Sélectionnez « Mes entonnoirs » ou « Entonnoirs partagés ».
1. Sélectionnez l’événement **Index** dans la liste déroulante **Étape 1**. 
1. Sélectionnez l’événement **Client** dans la liste déroulante **Étape 2**.
1. Sélectionnez l’événement **Créer** dans la liste déroulante **Étape 3**.
1. Donnez un nom à l’entonnoir, puis cliquez sur **Enregistrer**.

L’illustration suivante montre les données générées par l’outil Entonnoirs. Les propriétaires de Fabrikam peuvent alors constater qu’au cours des 90 derniers jours, 54,3 % de leurs clients ayant visité la page d’accueil ont créé un ticket client. Ils peuvent également constater que 2 700 clients ont accédé à l’index à partir de la page d’accueil, ce qui peut indiquer un problème d’actualisation.


![Outil Entonnoirs avec des données](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Fonctionnalités des entonnoirs
1. Si votre application est échantillonnée, une bannière d’échantillonnage est affichée. Cliquez sur la bannière pour ouvrir un volet contextuel indiquant comment désactiver l’échantillonnage. 
2. Vous pouvez exporter votre entonnoir vers [Power BI](app-insights-export-power-bi.md).
3. Cliquez sur une étape pour obtenir des informations plus détaillées sur la droite. 
4. La conversion historique indique la conversion au cours des 90 derniers jours. 
5. L’outil Utilisateurs des entonnoirs vous permet de mieux comprendre vos utilisateurs. Chaque étape met à votre disposition des filtres utilisateur organisés. 

## <a name="next-steps"></a>Étapes suivantes
  * [Vue d’ensemble de l’utilisation](app-insights-usage-overview.md)
  * [Utilisateurs, Sessions et Événements](app-insights-usage-segmentation.md)
  * [Rétention](app-insights-usage-retention.md)
  * [Classeurs](app-insights-usage-workbooks.md)
  * [Ajouter du contexte utilisateur](app-insights-usage-send-user-context.md)
  * [Exporter vers Power BI](app-insights-export-power-bi.md)

