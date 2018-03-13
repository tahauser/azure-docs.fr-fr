---
title: "Gérer le coût de la rétention des données dans Azure Log Analytics | Microsoft Docs"
description: "Découvrez comment changer le plan tarifaire et la stratégie de rétention des données de votre espace de travail Log Analytics dans le portail Azure."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5143abdde715424a41a53bb661db342acf817e0c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Gérer le coût de la rétention des données avec votre espace de travail Log Analytics
Quand vous vous inscrivez à Log Analytics, selon le plan que vous sélectionnez, il existe une limite quant à la durée pendant laquelle les données générées par vos sources connectées sont stockées dans votre espace de travail.  Cet article met en évidence les considérations pouvant influer sur les coûts de rétention des données pendant différentes périodes et indique comment configurer cette limite.   

Étant donné que Log Analytics peut consommer de grandes quantités de données à partir de sources locales, du cloud et d’environnements hybrides, le coût de stockage de ces données pendant une période de temps peut être considérable en fonction des facteurs suivants :

* Nombre de systèmes, de composants d’infrastructure, de ressources cloud, etc., à partir desquels vous effectuez des collectes
* Type de données créées par la source, telles que des files d’attente de messages, des journaux, des événements, des données relatives à la sécurité ou des métriques de performances
* Volume de données générées par ces sources 
* Nombre de solutions de gestion activées, source de données et fréquence des collectes

> [!NOTE]
> Consultez la documentation de chaque solution, car elle fournit une estimation de la quantité de données collectée.   

Si vous avez opté pour le plan *Gratuit*, la rétention des données est limitée à sept jours.  Pour les niveaux *Autonome* ou *Payant*, les données collectées disponibles couvrent les 31 derniers jours.  

Si vous utilisez le plan *Gratuit* et que vous constatez que vous dépassez constamment les quantités autorisées, vous pouvez définir votre espace de travail sur un plan payant pour collecter des données au-delà de cette limite. 

> [!NOTE]
> Des frais s’appliquent si vous choisissez de sélectionner une période de rétention plus longue pour le niveau payant. Vous pouvez changer de type de plan à tout moment ; pour plus d’informations sur les tarifs, consultez les [détails des tarifs](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Changer la période de rétention des données 

1. Connectez-vous au [portail Azure](http://portal.azure.com). 
2. Cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
3. Dans le volet des abonnements Log Analytics, sélectionnez l’espace de travail à modifier dans la liste.
4. Dans la page Espace de travail, cliquez sur **Rétention** dans le volet gauche.
5. Dans le volet de rétention de l’espace de travail, déplacez le curseur pour augmenter ou diminuer le nombre de jours, puis cliquez sur **Enregistrer**.  Si vous avez opté pour le niveau *Gratuit*, vous ne pouvez pas modifier la période de rétention de données et vous devez passer au niveau payant afin de contrôler ce paramètre.<br><br> ![Changer le paramètre de rétention de données de l’espace de travail](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Étapes suivantes  

Pour déterminer la quantité de données collectée, les sources qui les envoient et les différents types de données envoyées pour faciliter la gestion de la consommation et des coûts, consultez [Analyser l’utilisation des données dans Log Analytics](log-analytics-usage.md).