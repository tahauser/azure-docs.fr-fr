---
title: "Didacticiel : Vérifier l’utilisation et les coûts dans Azure Cost Management | Microsoft Docs"
description: "Dans ce didacticiel, vous vérifiez l’utilisation et les coûts pour effectuer le suivi des tendances, détecter le manque d’efficacité et créer des alertes."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: e8f09a90ae9a4dd54d81335de2494f0dcbe09485
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Didacticiel : Vérifier l’utilisation et les coûts

La Gestion des coûts Azure par Cloudyn vous indique l’utilisation et les coûts pour que vous puissiez effectuer le suivi des tendances, détecter le manque d’efficacité et créer des alertes. Toutes les données d’utilisation et de coût sont affichées dans les rapports et tableaux de bord Cloudyn. Les exemples de ce didacticiel vous montrent comment réviser l’utilisation et les coûts à l’aide de tableaux de bord et rapports. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Suivre les tendances d’utilisation et de coût
> * Détecter le manque d’efficacité au niveau de l’utilisation
> * Créer des alertes pour dépenses inhabituelles ou excessives

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Vous devez disposer d’un compte Azure.
- Vous devez disposer d’une inscription d’évaluation ou d’un abonnement payant pour Azure Cost Management.

## <a name="open-the-cloudyn-portal"></a>Ouvrir le portail Cloudyn

Vous révisez toutes les données d’utilisation et de coût dans le portail Cloudyn. Ouvrez le portail Cloudyn à partir du Portail Azure ou accédez à la page https://azure.cloudyn.com et connectez-vous.

## <a name="track-usage-and-cost-trends"></a>Suivre les tendances d’utilisation et de coût

Vous suivez l’argent dépensé au niveau de l’utilisation et des coûts à travers des rapports dans le temps pour identifier les tendances. Pour commencer à analyser les tendances, utilisez le rapport des coûts réels dans le temps. Dans le menu des rapports en haut du portail, cliquez sur **Cost (Coût)**  > **Cost Analysis (Analyse des coûts)** > **Actual Cost Over Time (Rapport des coûts réels dans le temps)**. Lorsque vous ouvrez le rapport pour la première fois, aucun groupe ou filtre n’est appliqué à ce dernier.

Voici un exemple de rapport :

![exemple de rapport](./media/tutorial-review-usage/actual-cost01.png)

Le rapport affiche toutes les dépenses au cours des 30 derniers jours. Pour afficher uniquement les dépenses pour les services Azure, appliquez le groupe Service et filtrez tous les services Azure. L’illustration suivante montre les services filtrés.

![services filtrés](./media/tutorial-review-usage/actual-cost02.png)

Dans l’exemple précédent, la quantité d’argent dépensée a baissé à partir du 31/08/2017. Cette tendance de coût se poursuit pour les différents services pendant neuf jours environ. Les dépenses commencent ensuite à augmenter. Notez toutefois qu’un trop grand nombre de colonnes peut masquer une tendance évidente. Vous pouvez modifier l’affichage du rapport sous forme d’un graphique en courbes ou en aires pour visualiser les données affichées dans d’autres vues. L’illustration suivante montre plus clairement la tendance.

![tendance reflétée dans le rapport](./media/tutorial-review-usage/actual-cost03.png)

Dans l’exemple, vous observez clairement que les coûts du stockage Azure ont baissé à partir du 31/08/2017 tandis que les dépenses pour d’autres services Azure se sont maintenus au même niveau. Pourquoi cette baisse des dépenses ? Dans cet exemple, certains employés étaient en vacances et n’ont donc pas utilisé le service de stockage.

Pour visionner un didacticiel vidéo sur le suivi de l’utilisation et des tendances de coût, consultez [Analyse des données de facturation cloud en fonction du temps avec Azure Cost Management de Cloudyn](https://youtu.be/7LsVPHglM0g).

## <a name="detect-usage-inefficiencies"></a>Détecter le manque d’efficacité au niveau de l’utilisation

Les rapports de l’optimiseur améliorent l’efficacité, optimisent l’utilisation et identifient les moyens d’économiser de l’argent au niveau des ressources de cloud. Ils sont particulièrement utiles avec des recommandations de dimensionnement économique destinées à faciliter la réduction du nombre de machines virtuelles inactives ou coûteuses.

La stratégie de virtualisation des organisations est un problème qui affecte souvent ces dernières quand elles déplacent au départ des ressources vers le cloud. Elles utilisent souvent une approche similaire à celle qu’elles utilisaient pour créer des machines virtuelles pour l’environnement de virtualisation local. Elles partent également du principe que les coûts sont réduits en déplaçant leurs machines virtuelles locales vers le cloud, telles quelles. Mais il est peu probable que cette approche permette de réduire les coûts.

Le problème est que leur infrastructure existante a été déjà payée. Les utilisateurs pouvaient créer et faire fonctionner des machines virtuelles de taille importante s’ils le souhaitaient, qu’elles soient inactives ou pas, sans grandes conséquences. Le déplacement de machines virtuelles inactives ou de taille importante vers le cloud risque *d’augmenter* les coûts. L’affection des coûts des ressources est importante quand vous signez des contrats avec des fournisseurs de services cloud. Vous devez payer en fonction de votre engagement, que vous utilisiez entièrement la ressource ou non.

Le rapport de recommandations de dimensionnement économique identifie les économies annuelles potentielles en comparant la capacité des types d’instance de machine virtuelle à leurs données d’historique d’utilisation de la mémoire et de l’UC.  

Dans le menu des rapports en haut du portail, cliquez sur **Optimizer (Optimiseur)** > **Pricing Optimization (Optimisation des prix)** > **Cost Effective Sizing Recommendations (Recommandations de dimensionnement économique)**. Filtrez le fournisseur sur Azure pour analyser uniquement des machines virtuelles Azure. Voici un exemple :

![Machines virtuelles Azure](./media/tutorial-review-usage/sizing01.png)

Dans cet exemple, l’organisation pourrait économiser 3 114 $ en suivant les recommandations pour modifier les types d’instance de machine virtuelle. Cliquez sur le signe plus (+) sous **Details (Détails)** pour la première recommandation. Voici les détails de la première recommandation.

![détails de la recommandation](./media/tutorial-review-usage/sizing02.png)

Pour afficher les ID d’instance de machine virtuelle, cliquez sur le signe plus situé en regard de **List of Candidates (Liste de candidats)**.

![Liste de candidats](./media/tutorial-review-usage/sizing03.png)

Pour visionner un didacticiel vidéo sur la détection des inefficacités d’utilisation, consultez [Optimisation de la taille de machine virtuelle dans Azure Cost Management de Cloudyn](https://youtu.be/1xaZBNmV704).

## <a name="create-alerts-for-unusual-spending"></a>Créer des alertes pour dépenses inhabituelles

Vous pouvez alerter automatiquement d’autres parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Vous pouvez créer rapidement et facilement des alertes à l’aide de rapports prenant en charge les alertes basées sur des seuils budgétaires et des seuils de coût.

Vous pouvez créer une alerte pour une dépense quelconque avec tout rapport de coût. Dans cet exemple, utilisez le rapport des coûts réels dans le temps pour vous avertir quand les dépenses de la machine virtuelle Azure s’approchent de votre budget total. Dans le menu des rapports en haut du portail, cliquez sur **Cost (Coût)**  > **Cost Analysis (Analyse des coûts)** > **Actual Cost Over Time (Rapport des coûts réels dans le temps)**. Définissez **Groups (Groupes)** sur **Service** et **filtrez le service** sur **Azure/VM**. Dans le coin supérieur droit du rapport, cliquez sur **Actions**, puis sélectionnez **Schedule report (Planifier le rapport)**.

Utilisez l’onglet **Scheduling (Planification)** pour recevoir un e-mail du rapport à la fréquence que vous souhaitez. Tous les regroupements, filtrages et balises que vous avez utilisés sont inclus dans le rapport envoyé par e-mail. Cliquez sur l’onglet **Threshold (Seuil)** et sélectionnez **Actual Cost vs. Threshold** (Coût réel vs seuil). Si vous avez un budget total de 500 000 $ et souhaitez recevoir une notification dès que les coûts s’approchent de la moitié environ, créez une **alerte rouge** sur 250 000 $ et une **alerte jaune** sur 240 000 $. Sélectionnez ensuite le nombre d’alertes consécutives. Quand vous aurez reçu le nombre total d’alertes que vous avez spécifié, vous ne recevrez plus d’alertes. Enregistrez le rapport planifié.

![exemple de rapport](./media/tutorial-review-usage/schedule-alert01.png)

Vous pouvez également sélectionner la métrique de seuil Cost Percentage vs. Budget (Pourcentage du coût vs budget) pour créer des alertes. Cette métrique vous permet d’utiliser des pourcentages budgétaires au lieu de valeurs de devise.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Suivre les tendances d’utilisation et de coût
> * Détecter le manque d’efficacité au niveau de l’utilisation
> * Créer des alertes pour dépenses inhabituelles ou excessives


Passez au didacticiel suivant pour apprendre à prévoir les dépenses à partir des données historiques.

> [!div class="nextstepaction"]
> [Prévoir les dépenses futures](tutorial-forecast-spending.md)
