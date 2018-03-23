---
title: Guide de référence des vignettes du Concepteur de vues dans Azure Log Analytics | Microsoft Docs
description: Grâce au Concepteur de vues de Log Analytics, vous pouvez créer des vues personnalisées dans le portail Azure qui affichent différentes visualisations de données dans votre espace de travail Log Analytics. Cet article est un guide de référence pour les paramètres des vignettes disponibles dans vos vues personnalisées.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: f341cb9430c7750909c1fc1f50c15f0620e74366
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Guide de référence des vignettes du Concepteur de vues dans Log Analytics
Grâce au Concepteur de vues d’Azure Log Analytics, vous pouvez créer des vues personnalisées dans le portail Azure qui présentent différentes visualisations de données dans votre espace de travail Log Analytics. Cet article est un guide de référence pour les paramètres des vignettes disponibles dans vos vues personnalisées.

Pour plus d’informations sur le Concepteur de vues, consultez :

* [Concepteur de vues](log-analytics-view-designer.md) : fournit une présentation du Concepteur de vues et des procédures de création et de modification des vues personnalisées.
* [Référence des composants de visualisation](log-analytics-view-designer-parts.md) : fournit un guide de référence pour les paramètres des composants de visualisation disponibles dans vos vues personnalisées.


Les vignettes du Concepteur de vues disponibles sont décrites dans le tableau suivant :  

| Vignette | Description |
|:--- |:--- |
| [Nombre](#number-tile) |Nombre d’enregistrements retournés par une requête. |
| [Deux nombres](#two-numbers-tile) |Nombre d’enregistrements retournés par deux requêtes différentes. |
| [Anneau](#donut-tile) | Graphique basé sur une requête, avec une valeur de synthèse au centre. |
| [Graphique en courbes et légende](#line-chart-amp-callout-tile) | Graphique en courbes basé sur une requête, et légende avec une valeur de synthèse. |
| [Graphique en courbes](#line-chart-tile) |Graphique en courbes basé sur une requête. |
| [Deux chronologies](#two-timelines-tile) | Histogramme avec deux séries basées chacune sur une requête distincte. |

Les sections suivantes décrivent les types de vignettes et leurs propriétés en détail.

## <a name="number-tile"></a>Vignette Nombre
La vignette **Nombre** affiche à la fois le nombre d’enregistrements retournés par une requête de journal et une étiquette.

![Vignette Nombre](media/log-analytics-view-designer/tile-number.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| **Vignette** | |
| Légende |Texte affiché sous la valeur. |
| Requête |Requête exécutée. Le nombre d’enregistrements retournés par la requête est affiché. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="two-numbers-tile"></a>Vignette Deux nombres
Cette vignette affiche le nombre d’enregistrements retournés par deux requêtes de journal distinctes, et une étiquette pour chacune d’elles.

![Vignette Deux nombres](media/log-analytics-view-designer/tile-two-numbers.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| **Première vignette** | |
| Légende |Texte affiché sous la valeur. |
| Requête |Requête exécutée. Le nombre d’enregistrements retournés par la requête est affiché. |
| **Deuxième vignette** | |
| Légende |Texte affiché sous la valeur. |
| Requête |Requête exécutée. Le nombre d’enregistrements retournés par la requête est affiché. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="donut-tile"></a>Vignette Anneau
La vignette **Anneau** affiche un nombre unique qui résume une colonne de valeur dans une requête de journal. L’anneau affiche sous forme graphique les résultats des trois premiers enregistrements.

![Vignette Anneau](media/log-analytics-view-designer/tile-donut.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| **Anneau** | |
| Requête |Requête exécutée pour l’anneau. La première propriété est une valeur de texte et la seconde une valeur numérique. Cette requête utilise habituellement le mot clé *measure* pour synthétiser les résultats. |
| **Anneau** |**&gt; Centrer** |
| Texte |Texte affiché sous la valeur à l’intérieur de l’anneau. |
| Opération |Opération effectuée sur la valeur de propriété afin de la résumer en une valeur unique.<ul><li>Sum : additionner les valeurs de tous les enregistrements avec la valeur de propriété.</li><li>Percentage : pourcentage des valeurs additionnées des enregistrements avec la valeur de propriété, comparé aux valeurs additionnées de tous les enregistrements.</li></ul> |
| Valeurs de résultat utilisées dans l’opération relative au centre |Vous pouvez sélectionner le signe plus (+) pour ajouter une ou plusieurs valeurs. Les résultats de la requête sont alors limités aux enregistrements dont vous avez spécifié les valeurs de propriété. Si aucune valeur n’est ajoutée, tous les enregistrements sont inclus dans la requête. |
| **Anneau** |**&gt; Options supplémentaires** |
| Couleurs |Couleur affiché pour chacune des trois premières propriétés. Pour spécifier d’autres couleurs pour des valeurs de propriété spécifiques, utilisez l’option *Mappage avancé des couleurs*. |
| Mappage avancé des couleurs |Affiche une couleur qui représente des valeurs de propriété spécifiques. Si la valeur spécifiée figure parmi les trois premières, l’autre couleur s’affiche au lieu de la couleur standard. Si la propriété ne figure pas parmi les trois premières, la couleur ne s’affiche pas. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="line-chart-tile"></a>Vignette Graphique en courbes
Cette vignette est un graphique en courbes qui affiche plusieurs séries à partir d’une requête de journal dans le temps. 

![Vignette Graphique en courbes et légende](media/log-analytics-view-designer/tile-line-chart.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| **Graphique en courbes** | |
| Requête |Requête exécutée pour le graphique en courbes. La première propriété est une valeur de texte et la seconde une valeur numérique. Cette requête utilise habituellement le mot clé *measure* pour synthétiser les résultats. Si la requête utilise le mot clé *interval*, l’axe des abscisses (X) utilise cet intervalle de temps. Si la requête n’utilise pas le mot clé *interval*, l’axe des abscisses utilise des intervalles d’une heure. |
| **Graphique en courbes** |**> Axe Y** |
| Utiliser l’échelle logarithmique |Sélectionnez ce lien pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête. Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs. Le **Type d’unité** spécifie la catégorie de l’unité et définit les valeurs de **Type d’unité en cours** disponibles. Si vous sélectionnez une valeur dans **Convertir en**, les valeurs numériques sont converties du type **Unité actuelle** dans le type **Convertir en**. |
| Étiquette personnalisée |Texte affiché pour l’axe Y en regard de l’étiquette du type d’*Unité*. Si aucune étiquette n’est spécifiée, seul le type d’*Unité* est affiché. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="line-chart-and-callout-tile"></a>Vignette Graphique en courbes et légende
Cette vignette contient à la fois un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps, et une légende avec une valeur de synthèse. 

![Vignette Graphique en courbes et légende](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| **Graphique en courbes** | |
| Requête |Requête exécutée pour le graphique en courbes. La première propriété est une valeur de texte et la seconde une valeur numérique. Cette requête utilise habituellement le mot clé *measure* pour synthétiser les résultats. Si la requête utilise le mot clé *interval*, l’axe des abscisses (X) utilise cet intervalle de temps. Si la requête n’utilise pas le mot clé *interval*, l’axe des abscisses utilise des intervalles d’une heure. |
| **Graphique en courbes** |**&gt; Légende** |
| Titre de la légende | Texte affiché au-dessus de la valeur de la légende. |
| Nom de la série |Valeur de propriété de série à utiliser comme valeur de la légende. Si aucune série n’est fournie, tous les enregistrements de la requête sont utilisés. |
| Opération |Opération effectuée sur la valeur de propriété afin de la résumer en une valeur unique pour la légende.<ul><li>Average : moyenne des valeurs de tous les enregistrements.</li><li>Count : nombre d’enregistrements retournés par la requête.</li><li>Last Sample : valeur du dernier intervalle inclus dans le graphique.</li><li>Max : valeur maximale des intervalles inclus dans le graphique.</li><li>Min : valeur minimale des intervalles inclus dans le graphique.</li><li>Sum : somme des valeurs de tous les enregistrements.</li></ul> |
| **Graphique en courbes** |**> Axe Y** |
| Utiliser l’échelle logarithmique |Sélectionnez ce lien pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête. Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir les valeurs. Le type d’*Unité* spécifie la catégorie de l’unité, et définit les valeurs de type *Unité actuelle* disponibles. Si vous sélectionnez une valeur pour l’option *Convertir en*, les valeurs numériques sont converties du type *Unité actuelle* au type *Convertir en*. |
| Étiquette personnalisée |Texte affiché pour l’axe Y en regard de l’étiquette du type d’*Unité*. Si aucune étiquette n’est spécifiée, seul le type d’*Unité* est affiché. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="two-timelines-tile"></a>Vignette Deux chronologies
La vignette **Deux chronologies** affiche les résultats de deux requêtes de journal dans le temps sous la forme de graphiques à barres. Une légende est affichée pour chaque série. 

![Vignette Deux chronologies](media/log-analytics-view-designer/tile-two-timelines.png)

| Paramètre | Description |
|:--- |:--- |
| NOM |Texte affiché en haut de la vignette. |
| Description |Texte affiché sous le nom de la vignette. |
| Premier graphique | |
| Légende |Texte affiché sous la légende de la première série. |
| Couleur |Couleur utilisée pour les colonnes de la première série. |
| Requête de graphique |Requête exécutée pour la première série. Le nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes de graphique. |
| Opération |Opération effectuée sur la valeur de propriété afin de la résumer en une valeur unique pour la légende.<ul><li>Average : moyenne des valeurs de tous les enregistrements.</li><li>Count : nombre d’enregistrements retournés par la requête.</li><li>Last Sample : valeur du dernier intervalle inclus dans le graphique.</li><li>Max : valeur maximale des intervalles inclus dans le graphique.</li></ul> |
| **Deuxième graphique** | |
| Légende |Texte affiché sous la légende de la deuxième série. |
| Couleur |Couleur utilisée pour les colonnes de la deuxième série. |
| Requête de graphique |Requête exécutée pour la deuxième série. Le nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes de graphique. |
| Opération |Opération effectuée sur la valeur de propriété afin de la résumer en une valeur unique pour la légende.<ul><li>Average : moyenne des valeurs de tous les enregistrements.</li><li>Count : nombre d’enregistrements retournés par la requête.</li><li>Last Sample : valeur du dernier intervalle inclus dans le graphique.</li><li>Max : valeur maximale des intervalles inclus dans le graphique. |
| **Avancée** |**&gt; Vérification du flux de données** |
| activé |Sélectionnez ce lien si la vérification du flux de données doit être activée pour la vignette. Cette approche fournit un autre message si les données ne sont pas disponibles. Normalement, vous adoptez cette approche pour fournir un message entre l’installation de la vue et le moment où les données deviennent disponibles. |
| Requête |Requête exécutée pour déterminer si les données sont disponibles pour la vue. Si la requête ne retourne aucun résultat, un message s’affiche à la place de la valeur de la requête principale. |
| Message |Message affiché si la requête de vérification du flux de données ne retourne aucune donnée. Si vous ne fournissez aucun message, un message d’état *Exécution de l’évaluation* s’affiche. |


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [recherche dans les journaux](log-analytics-log-searches.md) pour prendre en charge les requêtes dans les vignettes.
* Ajouter des [composants de visualisation](log-analytics-view-designer-parts.md) à votre vue personnalisée.
