---
title: "Explorez la nouvelle expérience Alertes (préversion) dans Azure Monitor | Microsoft Docs"
description: "Découvrez comment la nouvelle expérience d’alertes simple et évolutive d’Azure facilite la création, l’affichage et la gestion des alertes"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 316dcd53509897a6efc387749ca6f9ec268cb7ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Découvrez la nouvelle expérience Alertes (préversion) dans Azure Monitor

## <a name="overview"></a>Vue d'ensemble
 L’expérience Alertes dans Azure a une nouvelle présentation et a été mise à jour. Cette nouvelle expérience est disponible à partir de l’onglet **Alertes (préversion)** sous Azure Monitor. Voici quelques-uns des avantages de l’utilisation de la nouvelle expérience Alertes (préversion) :

 - **Séparation des alertes déclenchées et des règles d’alerte** : dans l’expérience Alertes (préversion), les règles d’alerte (la définition des conditions qui déclenchent une alerte) et les alertes déclenchées (il s’agit d’une instance du déclenchement de règles d’alerte) sont différenciées, les vues opérationnelles et de configuration sont donc séparées.
 - **Une expérience de création unifiée pour les alertes de journaux et de métrique** : la nouvelle expérience de création Alertes (préversion) guide l’utilisateur tout au long du processus de configuration d’une règle d’alerte, ce qui facilite la découverte des éléments appropriés aux alertes.
 - **Affichage des alertes Log Analytics déclenchées dans le portail Azure** : l’expérience Alertes (préversion) permet désormais d’afficher les alertes Log Analytics déclenchées dans votre abonnement.  
 - **Expérience de création unifiée pour les alertes de journal d’activité** : vous pouvez désormais créer des alertes de journal d’activité directement à partir de **Surveiller** > **Alertes (préversion)**. Précédemment, vous pouviez en créer uniquement via **Surveiller** > **Journal d’activité**.

Les sections suivantes décrivent plus en détail le fonctionnement de la nouvelle expérience.

## <a name="taxonomy"></a>Taxonomie
L’expérience Alertes(préversion) utilise les concepts suivants pour séparer les objets de règle d’alerte et d’alerte déclenchée lors de l’unification de l’expérience de création sur différents types d’alerte.

- **Ressource cible** : une cible peut être n’importe quelle ressource Azure. Une ressource cible définit l’étendue et les signaux disponibles pour les fonctions de génération d’alertes. Exemples de cibles : une machine virtuelle, un compte de stockage, un groupe de machines virtuelles identiques, un espace de travail Log Analytics ou une solution.

- **Critères** : les critères sont une combinaison du signal et de la logique appliqués à une ressource cible. Exemples : pourcentage d’UC > 70 %, temps de réponse du serveur > 4 ms, nombre de résultats d’une requête de journal > 100, etc. 

- **Signal** : des signaux sont émis par la ressource cible et peuvent être de plusieurs types. Cette préversion prend en charge les types de signaux **Métrique**, **Journal d’activité**, **Application Insights** et **Journal**.

- **Logique** : la logique définie par l’utilisateur sert à vérifier si le signal respecte la plage ou les valeurs attendues.  
 
- **Action** : appel émis vers le destinataire d’une notification (par exemple, en envoyant un e-mail ou en publiant une URL Webhook). Les notifications peuvent généralement déclencher plusieurs actions. Les types d’alertes pris en charge dans cette préversion, prennent en charge les groupes d’actions.  
 
- **Règle d’alerte** : la définition d’une condition qui déclenche l’alerte. Dans cette préversion, une règle d’alerte capture la cible et les critères d’alerte. La règle d’alerte peut être activée ou désactivée.
 
- **Alerte déclenchée** : créée lorsqu’une règle d’alerte activée est déclenchée. L’objet alerte déclenchée peut être déclenché ou non résolu.

    > [!NOTE]
    > Cela diffère de l’expérience d’alertes actuelle où l’alerte représente à la fois la règle et l’alerte déclenchée et par conséquent, peut présenter les états avertissement, actif ou désactivé.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Emplacement unique pour afficher et gérer les alertes
L’objectif de l’expérience Alertes (préversion) est d’être le seul emplacement pour l’affichage et la gestion de toutes vos alertes Azure. Les sous-sections suivantes décrivent les fonctions de chaque écran de la nouvelle expérience.

### <a name="alerts-preview-overview-page"></a>Page de présentation d’Alertes (préversion)
La page de présentation **Analyse - Alertes (préversion)** affiche une synthèse agrégée de toutes les alertes déclenchées et des règles d’alerte configurées/activées. Elle affiche également une liste de toutes les alertes déclenchées. Modifier les paramètres d’abonnement ou de filtre met à jour les agrégats et la liste des alertes déclenchées.

> [!NOTE]
> Les alertes déclenchées affichées dans Alertes (préversion) sont limitées aux alertes de journaux et de métrique prises en charge ; Azure Monitor affiche le nombre d’alertes déclenchées, y compris celles figurant dans les anciennes alertes Azure

 ![alerts-preview-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gestion des règles d’alerte
**Analyse - Alertes (préversion) > Règles** est une page unique permettant de gérer toutes les règles d’alerte de vos abonnements Azure. Elle répertorie toutes les règles d’alerte (activées ou désactivées) et peut être triée en fonction des ressources cibles, des groupes de ressources, du nom de règle ou de l’état. Les règles d’alerte peuvent également être activées/désactivées ou modifiées à partir de cette page.  

 ![alerts-preview-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Une expérience de création d’alerte pour toutes les sources d’analyse
Dans l’expérience Alertes (préversion), les alertes peuvent être créées de manière cohérente quel que soit le service de surveillance ou le type de signal. Toutes les alertes déclenchées et les détails connexes sont disponibles sur une seule page.  
 
La création d’une alerte est une tâche en trois étapes, l’utilisateur sélectionne tout d’abord une cible pour l’alerte, il sélectionne ensuite le signal approprié, puis spécifie la logique à appliquer sur le signal dans le cadre de la règle d’alerte. Ce processus de création simplifié ne nécessite plus que l’utilisateur connaisse la source de surveillance ou les signaux pris en charge avant de sélectionner une ressource Azure. L’expérience de création commune filtre automatiquement la liste des signaux disponibles en fonction de la ressource cible sélectionnée et guide la création de la logique d’alerte

Vous trouverez plus d’informations sur la création des types d’alerte suivants [ici](monitor-alerts-unified-usage.md).
- Alertes de métrique (appelées alertes de métrique en quasi temps réel dans l’expérience actuelle)
- Alertes de journal (Log Analytics)
- Alertes de journal (Journaux d’activité)
- Alertes de journal (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Types d’alertes pris en charge dans cette préversion


| **Type de signal** | **Source de l’analyse** | **Description** | 
|-------------|----------------|-------------|
| Métrique | Azure Monitor | Appelé [**alertes de métrique en quasi temps réel**](monitoring-near-real-time-metric-alerts.md) dans l’expérience actuelle, ces alertes de métrique prennent en charge l’évaluation des conditions de métrique jusqu’à une fréquence de 1 minute et autorisent les règles de métriques multiples. Une liste de types de ressources pris en charge est disponible [ici](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). D’autres alertes de métrique, définies [ici](monitoring-overview-alerts.md#alerts-in-different-azure-services), ne sont pas prises en charge dans l’expérience Alertes (préversion).|
| Journaux  | Log Analytics | Reçoivent des notifications ou exécutent des actions automatisées lorsqu’une requête de recherche dans les journaux concernant les métriques et/ou des données d’événement répond à certains critères.|
| Journaux  | Journaux d’activité | Cette catégorie contient les enregistrements de toutes les actions Créer, Mettre à jour et Supprimer exécutées via la cible sélectionnée (ressource/groupe de ressources/abonnement). |
| Journaux  | Journaux d’état du service | Non pris en charge dans l’expérience Alertes (préversion).   |
| Journaux  | Application Insights | Cette catégorie comprend des journaux contenant les détails des performances de votre application. Vous pouvez utiliser une requête Analytics pour définir les conditions applicables aux actions à accomplir sur la base des données de l’application. |
| Métrique | Application Insights | Non pris en charge dans l’expérience Alertes (préversion). |
| Tests de disponibilité | Application Insights | Non pris en charge dans l’expérience Alertes (préversion). |


## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment utiliser la nouvelle expérience Alertes (préversion) pour créer, afficher et gérer les alertes](monitor-alerts-unified-usage.md)
- [En savoir plus sur les alertes de journal dans l’expérience Alertes (préversion)](monitor-alerts-unified-log.md)
- [En savoir plus sur les alertes de métriques dans l’expérience Alertes (préversion)](monitoring-near-real-time-metric-alerts.md)
- [En savoir plus sur les Alertes de journal d’activité dans Alertes (préversion)](monitoring-activity-log-alerts-new-experience.md)
