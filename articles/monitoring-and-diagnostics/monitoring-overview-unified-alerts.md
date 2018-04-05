---
title: Explorer la nouvelle expérience Alertes dans Azure Monitor | Microsoft Docs
description: Découvrez comment la nouvelle expérience d’alertes simple et évolutive d’Azure facilite la création, l’affichage et la gestion des alertes
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nouvelle expérience Alertes dans Azure Monitor

## <a name="overview"></a>Vue d'ensemble
Azure Monitor offre une nouvelle expérience pour les alertes. L’ancienne expérience d’alertes se trouve désormais dans l’onglet Alertes (classiques). La nouvelle expérience Alertes présente les avantages suivants par rapport à l’expérience Alertes (classiques) :

 - **Séparation des alertes déclenchées et des règles d’alerte** : les règles d’alerte (c’est-à-dire la définition des conditions qui déclenchent des alertes) et les alertes déclenchées (c’est-à-dire les instances d’activation de règles d’alerte) sont différenciées de manière à offrir des vues distinctes pour les opérations et la configuration.
 - **Une expérience de création unifiée** : la création de toutes les alertes pour les métriques, les journaux et le journal d’activité pour Azure Monitor, Log Analytics et Application Insights se fait à partir d’un même emplacement. 
 - **Affichage des alertes Log Analytics déclenchées dans le portail Azure** : à présent, vous pouvez également afficher les alertes Log Analytics déclenchées dans votre abonnement. Auparavant, elles se trouvaient dans un portail distinct. 
 - **Un flux de travail amélioré** : la nouvelle expérience de création Alertes guide l’utilisateur tout au long du processus de configuration d’une règle d’alerte, ce qui facilite la découverte des éléments appropriés aux alertes.
 

Les sections suivantes décrivent plus en détail le fonctionnement de la nouvelle expérience.

## <a name="alert-rules-terminology"></a>Terminologie des règles d’alerte
La nouvelle expérience Alertes utilise les concepts suivants pour distinguer les objets de règle d’alerte et d’alerte déclenchée, tout en unifiant l’expérience de création pour les différents types d’alerte.

- **Ressource cible** : une cible peut être n’importe quelle ressource Azure. Une ressource cible définit l’étendue et les signaux disponibles pour les fonctions de génération d’alertes. Exemples de cibles : une machine virtuelle, un compte de stockage, un groupe de machines virtuelles identiques, un espace de travail Log Analytics ou une ressource Application Insights.

- **Critères** : les critères sont une combinaison du signal et de la logique appliqués à une ressource cible. Exemples : pourcentage d’UC > 70 %, temps de réponse du serveur > 4 ms, nombre de résultats d’une requête de journal > 100, etc. 

- **Signal** : des signaux sont émis par la ressource cible et peuvent être de plusieurs types. Les types de signaux pris en charge incluent **Métrique**, **Journal d’activité**, **Application Insights** et **Journal**.

- **Logique** : la logique définie par l’utilisateur sert à vérifier si le signal respecte la plage ou les valeurs attendues.  
 
- **Action** : action spécifique mise en œuvre quand l’alerte est déclenchée. Exemple : envoi d’un courrier électronique à une adresse e-mail ou appel d’une URL de webhook. Plusieurs actions peuvent se produire lors du déclenchement d’une alerte. Ces alertes prennent en charge les groupes d’actions.  
 
- **Règle d’alerte** : condition qui provoque le déclenchement de l’alerte. La règle d’alerte comprend la cible et les critères d’alerte. La règle d’alerte peut être activée ou désactivée.
 
    > [!NOTE]
    > Cela diffère de l’expérience Alertes (classiques), où l’alerte représente à la fois la règle et l’alerte déclenchée et peut par conséquent présenter les états Avertissement, Active ou Désactivée.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Emplacement unique pour afficher et gérer les alertes
L’objectif de l’expérience Alertes est d’offrir un emplacement unique pour l’affichage et la gestion de toutes vos alertes Azure. Les sous-sections suivantes décrivent les fonctions de chaque écran de la nouvelle expérience.

### <a name="alerts-overview-page"></a>Page de présentation des alertes
La page de présentation **Analyse - Alertes** affiche un résumé agrégé de toutes les alertes déclenchées et des règles d’alerte configurées/activées. Elle affiche également une liste de toutes les alertes déclenchées. Modifier les paramètres d’abonnement ou de filtre met à jour les agrégats et la liste des alertes déclenchées.

> [!NOTE]
> Les alertes déclenchées affichées dans Alertes sont limitées aux alertes de journaux et de métrique prises en charge ; la vue d’ensemble d’Azure Monitor affiche le nombre d’alertes déclenchées, y compris celles figurant dans les anciennes alertes Azure.

 ![alertes-présentation](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Gestion des règles d’alerte
**Analyse - Alertes > Règles** est une page unique permettant de gérer toutes les règles d’alerte de vos abonnements Azure. Elle répertorie toutes les règles d’alerte (activées ou désactivées) et peut être triée en fonction des ressources cibles, des groupes de ressources, du nom de règle ou de l’état. Les règles d’alerte peuvent également être activées/désactivées ou modifiées à partir de cette page.  

 ![alertes-règles](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Une expérience de création d’alerte pour toutes les sources d’analyse
Dans la nouvelle expérience Alertes, les alertes peuvent être créées de manière homogène, quel que soit le service de surveillance ou le type de signal. Toutes les alertes déclenchées et les détails connexes sont disponibles sur une seule page.  
 
La création d’une alerte est une tâche en trois étapes, l’utilisateur sélectionne tout d’abord une cible pour l’alerte, il sélectionne ensuite le signal approprié, puis spécifie la logique à appliquer sur le signal dans le cadre de la règle d’alerte. Ce processus de création simplifié ne nécessite plus que l’utilisateur connaisse la source de surveillance ou les signaux pris en charge avant de sélectionner une ressource Azure. L’expérience de création commune filtre automatiquement la liste des signaux disponibles en fonction de la ressource cible sélectionnée et guide la création de la logique d’alerte

Vous trouverez plus d’informations sur la création des types d’alerte suivants [ici](monitor-alerts-unified-usage.md).
- Alertes de métrique
- Alertes de journal (Log Analytics)
- Alertes de journal (Journaux d’activité)
- Alertes de journal (Application Insights)

 

## <a name="alert-types-supported"></a>Types d’alertes pris en charge


| **Type de signal** | **Source de l’analyse** | **Description** | 
|-------------|----------------|-------------|
| Métrique | Azure Monitor | Également appelées [**alertes de métrique en temps quasi réel**](monitoring-near-real-time-metric-alerts.md), ces alertes de métrique prennent en charge l’évaluation des conditions de métrique jusqu’à une fréquence de 1 minute et autorisent les règles de métriques multiples. Une liste de types de ressources pris en charge est disponible [ici](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Les alertes de métrique plus anciennes définies [ici](monitoring-overview-alerts.md#alerts-in-different-azure-services) ne sont pas prises en charge dans la nouvelle expérience Alertes. Vous les trouverez dans l’onglet Alertes (classiques).|
| Journaux  | Log Analytics | Reçoivent des notifications ou exécutent des actions automatisées lorsqu’une requête de recherche dans les journaux concernant les métriques et/ou des données d’événement répond à certains critères.|
| Journal d’activité | Journaux d’activité | Cette catégorie contient les enregistrements de toutes les actions Créer, Mettre à jour et Supprimer exécutées via la cible sélectionnée (ressource/groupe de ressources/abonnement). |
| Journaux  | Journaux d’état du service | Alerte non prise en charge dans l’expérience Alertes.   |
| Journaux  | Application Insights | Cette catégorie comprend des journaux contenant les détails des performances de votre application. Vous pouvez utiliser une requête Analytics pour définir les conditions applicables aux actions à accomplir sur la base des données de l’application. |
| Métrique | Application Insights | Alerte non prise en charge dans l’expérience Alertes. Vous la trouverez dans l’onglet Alertes (classiques). |
| Tests de disponibilité | Application Insights | Alerte non prise en charge dans l’expérience Alertes. Vous la trouverez dans l’onglet Alertes (classiques). |


## <a name="next-steps"></a>Étapes suivantes
- [Découvrez comment utiliser la nouvelle expérience Alertes pour créer, afficher et gérer des alertes](monitor-alerts-unified-usage.md)
- [Approfondissez vos connaissances sur les alertes de journal dans l’expérience Alertes](monitor-alerts-unified-log.md)
- [Approfondissez vos connaissances sur les alertes de métrique dans l’expérience Alertes](monitoring-near-real-time-metric-alerts.md)
- [Approfondissez vos connaissances sur les alertes de journal d’activité dans l’expérience Alertes](monitoring-activity-log-alerts-new-experience.md)
