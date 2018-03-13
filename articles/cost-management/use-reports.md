---
title: "Utiliser des rapports de gestion des coûts dans Azure Cost Management | Microsoft Docs"
description: "Cet article décrit comment utiliser les différents rapports Azure Cost Management dans le portail Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 0a4f6a5940ccffe897a5609b837ddaeea98d87aa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-cost-management-reports"></a>Utiliser les rapports Azure Cost Management

Cet article décrit comment utiliser les différents rapports Azure Cost Management dans le portail Cloudyn. La plupart des rapports Cloudyn sont intuitifs et ont une apparence uniforme. Pour obtenir une vue d’ensemble des rapports Cloudyn, consultez [Présentation des rapports de coût](understading-cost-reports.md). Cet article décrit également les différentes options et les champs utilisés dans la plupart des rapports.

## <a name="cost-analysis-reports"></a>Rapports d’analyse des coûts

Les rapports d’analyse des coûts présentent les données de facturation émises par vos fournisseurs cloud. En utilisant les rapports, vous pouvez regrouper et explorer divers segments de données détaillés dans le fichier de facturation. Les rapports permettent de parcourir précisément les coûts dans toutes les données de facturation brutes des fournisseurs cloud.

Les rapports d’analyse des coûts ne regroupent pas les coûts par balises. La création de rapports en fonction de balises est disponible uniquement dans les rapports de répartition des coûts définis après avoir créé un modèle de coût à l’aide de Cost Allocation 360.

### <a name="actual-cost-analysis"></a>Analyse du coût réel

Le rapport d’analyse du coût réel présente vos principaux contributeurs de coût, notamment les coûts récurrents et les frais ponctuels.

 Utilisez le rapport d’analyse du coût réel pour :

- Analyser et surveiller les coûts réels dépensés pendant un laps de temps spécifié
- Planifier une alerte de seuil
- Analyser des coûts de récupération des données de facturation et de facturation interne

#### <a name="to-use-the-actual-cost-analysis-report"></a>Pour utiliser le rapport d’analyse du coût réel

Effectuez au moins les étapes suivantes. Vous pouvez également utiliser d’autres options et champs.

1. Sélectionnez une plage de dates.
2. Sélectionnez un filtre.

Vous pouvez cliquer avec le bouton droit sur des résultats de rapport pour les explorer et afficher des informations plus précises.

![Exemple de rapport d’analyse du coût réel](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Coûts réels dans le temps

Le rapport des coûts réels dans le temps est un rapport d’analyse des coûts standard qui répartit les coûts sur une résolution de temps définie. Le rapport présente les dépenses au fil du temps pour vous permettre d’observer les tendances et de détecter des irrégularités dans les dépenses. Il montre vos principaux contributeurs de coût, comme les coûts récurrents et les frais d’instances réservées ponctuels pendant un laps de temps sélectionné.

Utilisez le rapport des coûts réels dans le temps pour :

- Voir les tendances des coûts dans le temps.
- Trouver des irrégularités dans les coûts.
- Trouver toutes les questions liées aux coûts concernant Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Pour utiliser le rapport des coûts réels dans le temps :

Effectuez au moins les étapes suivantes. Vous pouvez également utiliser d’autres options et champs.

- Sélectionnez une plage de dates.

Par exemple, vous pouvez sélectionner des groupes pour afficher leurs coûts au fil du temps. Vous ajoutez ensuite des filtres pour affiner vos résultats.

![Exemple de rapport des coûts réels dans le temps](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Rapports des coûts amortis

Cet ensemble de rapports des coûts amortis indique les frais de service linéarisés non liés à l’utilisation ou les coûts payables une seule fois. Les coûts sont étalés dans le temps de manière régulière tout au long de leur durée de vie.

Par exemple, les frais ponctuels peuvent inclure :

- Les frais d’assistance annuels
- Les frais annuels liés au composant de sécurité
- Les frais d’achat d’instances réservées
- Certains articles de la Place de marché Azure

Dans le fichier de facturation, les frais ponctuels sont caractérisés lorsque les dates de début et de fin de consommation de services, ou l’horodatage, ont des valeurs égales. Cloudyn les identifie alors en tant que frais ponctuels qui peuvent être amortis. Les autres services basés sur la consommation avec des coûts d’utilisation à la demande ne peuvent pas être amortis.

Pour illustrer les coûts amortis, examinez l’exemple d’image suivante d’un rapport des coûts réels dans le temps. Dans l’exemple, un pic de coût a lieu le 23 août. Cela peut sembler être une anomalie par rapport à la tendance des coûts quotidiens habituels. L’analyse de la cause racine et la navigation dans les données ont permis d’identifier ce coût comme une réservation APN du service AWS annuel, à savoir des frais ponctuels liés à un achat et une facturation propres à ce jour. Vous pouvez voir comment ce coût est amorti dans la section suivante.

![Exemple de rapport des coûts réels dans le temps montrant des frais ponctuels](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Pour utiliser le rapport des coûts amortis dans le temps :

Effectuez au moins les étapes suivantes. Vous pouvez également utiliser d’autres options et champs.

1. Sélectionnez une plage de dates.
2. Sélectionnez un service et un fournisseur.

En reprenant l’exemple précédent, vous pouvez voir que le coût ponctuel est désormais amorti dans l’image suivante :

![Exemple de rapport des coûts amortis dans le temps](./media/use-reports/amort-cost-over-time.png)

L’image précédente montre le coût amorti du coût de réservation APN au fil du temps. Ce rapport présente l’amortissement des frais ponctuels et le coût APN comme un achat de réservation annuel. Le coût APN est réparti uniformément de façon quotidienne à hauteur de 1/365ème du coût initial de réservation.

## <a name="cost-allocation-analysis-reports"></a>Rapports d’analyse de l’affectation des coûts

Les rapports d’analyse de l’affectation des coûts sont disponibles une fois que vous avez créé un modèle de coût à l’aide de Cost Allocation 360. Cloudyn traite les données de coût/facturation et les fait correspondre aux données d’utilisation et de balise de vos comptes cloud. Pour faire correspondre les données, Cloudyn requiert un accès à vos données d’utilisation. Les comptes auxquels il manque les informations d’identification sont étiquetés comme des ressources sans catégorie.

### <a name="cost-analysis-report"></a>Rapport d’analyse des coûts

Le rapport d’analyse des coûts fournit un aperçu de votre consommation cloud et des dépenses pendant un laps de temps sélectionné. Les stratégies définies dans Cost Allocation Manager sont utilisées dans le rapport d’analyse des coûts.

Comment Cloudyn calcule ce rapport ?

Cloudyn garantit que l’affectation conserve l’intégrité de chaque compte lié en appliquant une affinité de compte. Cette affinité garantit qu’un compte qui n’utilise pas un service spécifique n’a aucun coût de ce service qui lui est affecté. Les coûts à payer dans ce compte restent dans ce compte et ne sont pas calculés par les stratégies d’affectation. Par exemple, vous pouvez avoir cinq comptes liés. Si seulement trois d’entre eux utilisent des services de stockage, alors le coût des services de stockage est affecté uniquement aux balises de ces trois comptes.

 Utilisez le rapport d’analyse des coûts pour :

- Afficher une vue agrégée de tout votre déploiement pendant un laps de temps donné.
- Afficher les coûts par catégories de balise en fonction des stratégies créées dans le modèle de coût.

#### <a name="to-use-the-cost-analysis-report"></a>Pour utiliser le rapport d’analyse des coûts :

1. Sélectionnez une plage de dates.
2. Ajoutez des balises, selon les besoins.
3. Ajoutez des groupes.
4. Choisissez un modèle de coût que vous avez créé précédemment.

L’image suivante montre un exemple de rapport d’analyse des coûts en rayons de soleil. Les anneaux indiquent les groupes. L’anneau extérieur montre le service et le cercle intérieur indique l’unité.

![Exemple de rapport d’analyse des coûts](./media/use-reports/cost-analysis01.png)



Voici l’exemple des mêmes informations dans un tableau.

![Exemple de rapport d’analyse des coûts](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Rapport des coûts dans le temps

Le rapport des coûts dans le temps présente les dépenses au fil du temps pour vous permettre de dégager des tendances et détecter des irrégularités dans votre déploiement. Il montre avant tout les coûts répartis sur une période définie. Ce rapport inclut vos principaux contributeurs de coût, comme les coûts récurrents et les frais d’instances réservées ponctuels pendant un laps de temps sélectionné. Les stratégies définies dans Cost Manager 360° peuvent être utilisées dans ce rapport.

Utilisez le rapport des coûts dans le temps pour :

- Voir les modifications dans le temps et ce qui a une incidence sur ces modifications d’un jour (ou d’une plage de dates) à l’autre.
- Analyser les coûts au fil du temps pour une instance spécifique.
- Comprendre pourquoi une augmentation des coûts a eu lieu pour une instance spécifique.

#### <a name="to-use-the-cost-over-time-report"></a>Pour utiliser le rapport des coûts dans le temps :

1. Sélectionnez une plage de dates.
2. Ajoutez des balises, selon les besoins.
3. Ajoutez des groupes.
4. Choisissez un modèle de coût que vous avez créé précédemment.
5. Sélectionnez des coûts réels ou des coûts amortis.
6. Décidez d’appliquer ou non des règles d’affectation pour afficher les données de facturation brutes ou pour recalculer les coûts avec Cloudyn.

Voici un exemple du rapport.

![Exemple de rapport des coûts dans le temps](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Étapes suivantes

- Si vous n’avez pas encore suivi le premier didacticiel de Cost Management, consultez-le dans [Réviser l’utilisation et les coûts](tutorial-review-usage.md).
