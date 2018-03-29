---
title: Vue d’ensemble d’Azure Cost Management | Microsoft Docs
description: Azure Cost Management est une solution de gestion des coûts multicloud qui vous aide dans votre utilisation d’Azure et autres ressources cloud.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3e5caff5ff1af79154baddf39bf465ddea5aadae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-cost-management"></a>Qu’est-ce que la gestion des coûts Azure ?

Azure Cost Management fourni par Cloudyn, affilié à Microsoft, vous permet de suivre l’utilisation du cloud et les dépenses liées à vos ressources Azure et celles d’autres fournisseurs de services cloud, notamment AWS et Google. Les rapports du tableau de bord vous aident à comprendre la répartition des coûts, de même que la rétrofacturation et la facturation interne. La gestion des coûts vous permet d’optimiser vos dépenses cloud en identifiant les ressources sous-utilisées que vous pouvez ainsi gérer et ajuster.

Pour visionner une vidéo de présentation, consultez [Introduction à Azure Cost Management](https://azure.microsoft.com/en-us/resources/videos/azure-cost-management-overview-and-demo).

## <a name="monitor-usage-and-spending"></a>Surveiller l’utilisation et les dépenses

Il est extrêmement important de surveiller l’utilisation et les dépenses en infrastructures cloud, car les organisations sont facturées sur les ressources qu’elles consomment dans le temps. Quand l’utilisation dépasse les seuils contractuels, les coûts peuvent augmenter de façon rapide et inattendue. La surveillance ad-hoc peut s’avérer difficile, et ce pour plusieurs raisons. Tout d’abord, la projection des coûts en fonction de l’utilisation moyenne suppose que votre consommation reste constante sur une période de facturation donnée. En second lieu, quand les coûts sont proches de votre budget ou le dépassent, il est important que vous en soyez averti à un stade précoce pour ajuster vos dépenses. Enfin, les fournisseurs de services cloud ne proposent pas nécessairement de rapports de projection des coûts par rapport aux seuils ou des rapports de comparaison de périodes.

Les rapports vous aident à surveiller les dépenses et ainsi d’analyser et suivre l’utilisation du cloud, les coûts et les tendances. Grâce aux rapports d’utilisation dans le temps, vous pouvez détecter des anomalies par rapport aux tendances normales. La mauvaise utilisation des ressources dans votre déploiement cloud est visible dans les rapports d’optimisation. Vous pouvez aussi identifier une mauvaise utilisation des ressources dans les rapports d’analyse des coûts.

![Rapport des coûts dans le temps](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Gérer les coûts

Les données historiques peuvent vous aider à gérer les coûts quand vous analysez l’utilisation et les coûts dans le temps pour identifier les tendances. Celles-ci permettent ensuite de prévoir les dépenses futures. La gestion des coûts propose aussi des rapports d’estimation des coûts très utiles.

La répartition des coûts permet de gérer les coûts en analysant vos coûts en fonction de votre stratégie de balisage. Vous pouvez utiliser des balises sur vos comptes, ressources et entités personnalisés pour affiner la répartition des coûts. Le Gestionnaire de catégories organise vos balises pour optimiser la gouvernance. Enfin, la répartition des coûts dans la rétrofacturation et la facturation interne met en évidence l’utilisation des ressources et les coûts associés dans le but d’influencer les comportements de consommation ou de facturer les clients du locataire.

Le contrôle d’accès facilite la gestion des coûts en autorisant les utilisateurs et les équipes à accéder uniquement aux données de gestion des coûts dont ils ont besoin. Vous vous servez de la structure des entités, de la gestion des utilisateurs et des rapports planifiés avec les listes de destinataires pour attribuer l’accès.

La gestion des coûts est aussi facilitée par les alertes, qui vous avertissent automatiquement en cas de dépenses inhabituelles ou excessives. Les alertes peuvent aussi être envoyées automatiquement à d’autres parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Plusieurs rapports prennent en charge les alertes basées sur les seuils budgétaires et de coûts. Toutefois, les alertes ne sont actuellement pas prises en charge pour les comptes ou abonnements de partenaires CSP.

## <a name="improve-efficiency"></a>Améliorer l’efficacité

Grâce à la gestion des coûts, vous pouvez déterminer quelle est l’utilisation optimale des machines virtuelles, identifier ou supprimer les machines virtuelles inactives, ainsi que les disques non attachées. Les informations fournies par les rapports d’optimisation du dimensionnement et les rapports de mauvaise utilisation des ressources permettent d’élaborer un plan visant à réduire la taille ou supprimer les machines virtuelles inactives. Toutefois, les rapports d’optimisation ne sont actuellement pas pris en charge pour les comptes ou abonnements de partenaires CSP.

![Recommandations de dimensionnement](.\media\overview\sizing.png)

Si vous avez approvisionné des instances réservées AWS, vous pouvez optimiser leur utilisation grâce aux rapports d’optimisation. Vous y trouverez des recommandations d’achat et ils vous permettront de modifier les réservations inutilisées et de planifier l’approvisionnement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez un peu plus sur la gestion des coûts, passez à l’étape suivante en inscrivant votre environnement cloud et commencez à explorer vos données.

- [Inscrire un abonnement Azure individuel](quick-register-azure-sub.md)
