---
title: "Solution Capacité et performances d’Azure Log Analytics | Microsoft Docs"
description: "Utilisez la solution Capacité et performances dans Log Analytics pour vous aider à comprendre la capacité de vos serveurs Hyper-V."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: 26e87da60dc02dce8122c82a2208477a8b1813a7
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>Planifier la capacité des machines virtuelles Hyper-V avec la solution Capacité et performances (version préliminaire)

![Symbole de Capacity and Performance](./media/log-analytics-capacity/capacity-solution.png)

Vous pouvez utiliser la solution Capacité et performances dans Log Analytics pour vous aider à comprendre la capacité de vos serveurs Hyper-V. La solution fournit des informations sur votre environnement Hyper-V en vous indiquant l’utilisation globale (processeur, mémoire et disque) des hôtes et des machines virtuelles en cours d’exécution sur ces hôtes Hyper-V. Des mesures sont collectées pour le processeur, la mémoire et les disques sur la totalité des hôtes et des machines virtuelles qui s’exécutent sur ceux-ci.

La solution :

-   Indique les hôtes dont les utilisations de processeur et de mémoire sont la plus élevée et la plus faible
-   Indique les machines virtuelles dont les utilisations de processeur et de mémoire sont la plus élevée et la plus faible
-   Indique les machines virtuelles avec le nombre d’E/S par seconde et l’utilisation de débit les plus élevés et les plus faibles
-   Indique quelles machines virtuelles sont exécutées sur tels hôtes
-   Indique les principaux disques ayant un débit, un nombre d’E/S par seconde et une latence élevés dans les volumes partagés de cluster
- Vous permet de personnaliser et de filtrer des données en fonction de groupes

> [!NOTE]
> La version précédente de la solution Capacité et performances, appelée Gestion de la capacité, nécessitait System Center Operations Manager et System Center Virtual Machine Manager. Cette solution mise à jour ne comporte plus ces dépendances.


## <a name="connected-sources"></a>Sources connectées

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Support | Description |
|---|---|---|
| [Agents Windows](log-analytics-windows-agent.md) | OUI | La solution collecte des informations sur la capacité et les données de performances des agents Windows. |
| [Agents Linux](log-analytics-linux-agents.md) | Non     | La solution ne collecte pas d’informations sur la capacité ni sur les données de performances des agents Linux directs.|
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | OUI |La solution collecte les données de capacité et de performances des agents dans un groupe d’administration SCOM connecté. Une connexion directe entre l’agent SCOM et Log Analytics n’est pas obligatoire.|
| [Compte Azure Storage](log-analytics-azure-storage.md) | Non  | Le stockage Azure n’inclut pas de données de performances ni de capacité.|

## <a name="prerequisites"></a>configuration requise

- Des agents Windows ou Operations Manager doivent être installés sur Windows Server 2012 ou des hôtes Hyper-V supérieurs, mais pas sur des machines virtuelles.


## <a name="configuration"></a>Configuration

Exécutez l’étape suivante pour ajouter la solution Capacité et performances dans votre espace de travail.

- Ajoutez la solution Capacity and Performance dans votre espace de travail Log Analytics en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

## <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration SCOM est connecté à votre espace de travail Log Analytics, les packs d’administration suivants sont installés dans SCOM lorsque vous ajoutez cette solution. Ces packs d’administration ne nécessitent aucune opération de configuration ou de maintenance.

- Microsoft.IntelligencePacks.CapacityPerformance

L’événement 1201 ressemble à ce qui suit :


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

Lorsque la solution Capacité et performances est mise à jour, le numéro de version change.

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](log-analytics-om-agents.md).

## <a name="using-the-solution"></a>Utilisation de la solution

Si vous ajoutez la solution Capacité et performances à votre espace de travail, elle est également ajoutée au tableau de bord Vue d’ensemble. Cette vignette affiche le nombre d’hôtes Hyper-V actuellement actifs et le nombre de machines virtuelles actives qui ont été analysés pendant la période sélectionnée.

![Vignette Capacité et performances](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>Vérifier l’utilisation

Cliquez sur la vignette Capacité et performances pour ouvrir le tableau de bord du même nom. Le tableau de bord comprend les colonnes figurant dans le tableau suivant. Chaque colonne répertorie jusqu’à dix éléments répondant aux critères de cette colonne pour l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche de journal qui envoie l’ensemble des enregistrements. Pour cela, cliquez sur **Afficher tout** en bas de la colonne ou cliquez sur l’en-tête de colonne.

- **Hôtes**
    - **Utilisation de l’UC de l’hôte** : affiche une tendance graphique de l’utilisation du processeur des ordinateurs hôtes ainsi qu’une liste d’hôtes, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom d’hôte quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur de processeur des machines virtuelles hébergées.
    - **Utilisation de la mémoire de l’hôte** : affiche une tendance graphique de l’utilisation de la mémoire des ordinateurs hôtes ainsi qu’une liste d’hôtes, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom d’hôte quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur de mémoire des machines virtuelles hébergées.
- **Machines virtuelles**
    - **Utilisation de l’UC de la machine virtuelle** : affiche une tendance graphique de l’utilisation du processeur des machines virtuelles ainsi qu’une liste de machines virtuelles, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps pour les 3 machines virtuelles principales. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom de machine virtuelle quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur de processeur agrégé de la machine virtuelle.
    - **Utilisation de la mémoire de la machine virtuelle** : affiche une tendance graphique de l’utilisation de la mémoire des machines virtuelles ainsi qu’une liste de machines virtuelles, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps pour les 3 machines virtuelles principales. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom de machine virtuelle quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur de mémoire agrégé de la machine virtuelle.
    - **Nombre total d’E/S par seconde des disques de machine virtuelle** : affiche une tendance graphique du nombre total d’E/S par seconde des disques des machines virtuelles, ainsi qu’une liste de machines virtuelles avec le nombre d’E/S par seconde pour chacune d’elles, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps pour les 3 machines virtuelles principales. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom de machine virtuelle quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur d’E/S par seconde de disque agrégé de la machine virtuelle.
    - **Débit total des disques de machine virtuelle** : affiche une tendance graphique du débit total des disques des machines virtuelles, ainsi qu’une liste de machines virtuelles avec le débit total des disques pour chacune d’elles, en fonction de la période sélectionnée. Placez le curseur sur le graphique en courbes pour afficher les détails d’un point spécifique dans le temps pour les 3 machines virtuelles principales. Cliquez sur le graphique pour afficher plus de détails dans Recherche dans les journaux. Cliquez sur un nom de machine virtuelle quelconque pour ouvrir Recherche dans les journaux et pour afficher les détails du compteur de débit total des disques agrégé de la machine virtuelle.
- **Volumes en cluster partagés**
    - **Débit total** : affiche la somme des lectures et écritures dans les volumes en cluster partagés.
    - **E/S par seconde totales** : affiche la somme des opérations d’entrée/sortie par seconde sur les volumes en cluster partagés.
    - **Latence totale** : affiche la latence totale sur les volumes en cluster partagés.
- **Densité de l’hôte** : la vignette supérieure affiche le nombre total d’hôtes et de machines virtuelles disponibles pour la solution. Cliquez sur cette vignette pour afficher des détails supplémentaires dans Recherche dans les journaux. Elle répertorie également tous les hôtes ainsi que le nombre de machines virtuelles hébergées. Cliquez sur un hôte pour examiner plus en détail les résultats de la machine virtuelle dans une recherche dans les journaux.


![panneau Hôtes du tableau de bord](./media/log-analytics-capacity/dashboard-hosts.png)

![panneau Machines virtuelles du tableau de bord](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>Évaluer les performances

Les environnements informatiques de production varient considérablement d’une organisation à l’autre. En outre, les charges de travail de capacité et de performance peuvent dépendre du mode d’exécution de vos machines virtuelles et de ce que vous considérez normal. Des procédures spécifiques pour vous aider à mesurer les performances ne seraient probablement pas applicables à votre environnement. Des conseils plus généraux sont donc mieux appropriés pour vous aider. Microsoft publie de nombreux articles regroupant des conseils pour vous aider à mesurer les performances.

En résumé, la solution collecte les données de capacité et de performances à partir de nombreuses sources, notamment les compteurs de performances. Utilisez ces données de capacité et de performances, qui sont présentées dans différentes surfaces dans la solution et comparez vos résultats à ceux de l’article [Measuring Performance on Hyper-V (Mesure des performances sur Hyper-V)](https://msdn.microsoft.com/library/cc768535.aspx). Même si cet article a été publié il y a quelque temps, les mesures, les remarques à prendre en compte et les instructions restent valides. Cet article contient des liens vers d’autres ressources utiles.


## <a name="sample-log-searches"></a>Exemples de recherches dans les journaux

Le tableau ci-dessous contient des exemples de recherche dans les journaux pour les données de capacité et de performances collectées et calculées par cette solution.

| Requête | Description |
|---|---|
| Toutes les configurations mémoire d’hôte | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="Host Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Toutes les configurations mémoire de machine virtuelle | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="VM Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| Répartition de toutes les E/S par seconde des disques entre toutes les machines virtuelles | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Reads/s" OR CounterName="VHD Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Répartition du débit total des disques entre toutes les machines virtuelles | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Read MB/s" OR CounterName="VHD Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Répartition de toutes les E/S par seconde entre tous les volumes partagés de cluster | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Reads/s" OR CounterName="CSV Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Répartition du débit total entre tous les volumes partagés de cluster | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read MB/s" OR CounterName="CSV Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| Répartition de la latence totale entre tous les volumes partagés de cluster | <code> Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read Latency" OR CounterName="CSV Write Latency") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |

>[!NOTE]
> Si votre espace de travail a été mis à niveau vers le [nouveau langage de requête Log Analytics](log-analytics-log-search-upgrade.md), les requêtes ci-dessus seront remplacées par les suivantes.

> | Requête | Description |
|:--- |:--- |
| Toutes les configurations mémoire d’hôte | Perf &#124; où ObjectName == « Capacité et performances » et CounterName == « Mo de mémoire attribuée Mo à l’hôte » &#124; résumer Mo = avg(CounterValue) par InstanceName |
| Toutes les configurations mémoire de machine virtuelle | Perf &#124; où ObjectName == « Capacité et performance » et CounterName == « Mo de mémoire attribuée à la machine virtuelle » &#124; résumer Mo = avg(CounterValue) par InstanceName |
| Répartition de toutes les E/S par seconde des disques entre toutes les machines virtuelles | Perf &#124; où ObjectName == « Capacité et performance » et (CounterName == « Lectures/s de disque dur virtuel » ou CounterName == « Écritures/s de disque dur virtuel ») &#124; résumer AggregatedValue = avg(CounterValue) par emplacement (TimeGenerated, 1 h), CounterName, InstanceName |
| Répartition du débit total des disques entre toutes les machines virtuelles | Perf &#124; où ObjectName == « Capacité et performance » et (CounterName == « Lecture Mo/s de disque dur virtuel » ou CounterName == « Écriture Mo/s de disque dur virtuel ») &#124; résumer AggregatedValue = avg(CounterValue) par emplacement (TimeGenerated, 1 h), CounterName, InstanceName |
| Répartition de toutes les E/S par seconde entre tous les volumes partagés de cluster | Perf &#124; où ObjectName == « Capacité et performance » et (CounterName == « Lectures/s de volume partagé de cluster » ou CounterName == « Écritures/s de volume partagé de cluster ») &#124; résumer AggregatedValue = avg(CounterValue) par emplacement (TimeGenerated, 1 h), CounterName, InstanceName |
| Répartition du débit total entre tous les volumes partagés de cluster | Perf &#124; où ObjectName == « Capacité et performance » et (CounterName == « Lectures/s de volume partagé de cluster » ou CounterName == « Écritures/s de volume partagé de cluster ») &#124; résumer AggregatedValue = avg(CounterValue) par emplacement (TimeGenerated, 1 h), CounterName, InstanceName |
| Répartition de la latence totale entre tous les volumes partagés de cluster | Perf &#124; où ObjectName == « Capacité et performance » et (CounterName == « Latence de lectures/s de volume partagé de cluster » ou CounterName == « Latence d’écritures/s de volume partagé de cluster ») &#124; résumer AggregatedValue = avg(CounterValue) par emplacement (TimeGenerated, 1 h), CounterName, InstanceName |


## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [Recherches dans les journaux de Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées de capacité et de performances.
