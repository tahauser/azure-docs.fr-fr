---
title: Créer des vues pour analyser les données dans Azure Log Analytics | Microsoft Docs
description: Grâce au Concepteur de vues de Log Analytics, vous pouvez créer des vues personnalisées affichées dans le portail Azure qui contiennent différentes visualisations de données dans l’espace de travail Log Analytics. Cet article contient une présentation du Concepteur de vues et présente des procédures de création et de modification des vues personnalisées.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: d63d47c39054230307416e24ed1c8295fbf68d93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Créer des vues personnalisées à l’aide du Concepteur de vues dans Log Analytics
Grâce au Concepteur de vues d’[Azure Log Analytics](log-analytics-overview.md), vous pouvez créer plusieurs vues personnalisées dans le portail Azure qui peuvent vous aider à visualiser les données dans votre espace de travail Log Analytics. Cet article fournit une présentation du Concepteur de vues et des procédures de création et de modification des vues personnalisées.

Pour plus d’informations sur le Concepteur de vues, consultez :

* [Référence de la vignette](log-analytics-view-designer-tiles.md) : fournit un guide de référence pour les paramètres de chacune des vignettes disponibles dans vos vues personnalisées.
* [Référence des composants de visualisation](log-analytics-view-designer-parts.md) : fournit un guide de référence pour les paramètres des composants de visualisation disponibles dans vos vues personnalisées.


## <a name="concepts"></a>Concepts
Les vues sont affichées dans la page **Vue d’ensemble** de votre espace de travail Log Analytics dans le portail Azure. Les vignettes de chaque vue personnalisée sont affichées par ordre alphabétique, et les vignettes pour les solutions sont installées dans le même espace de travail.

![Page Vue d’ensemble](media/log-analytics-view-designer/overview-page.png)

Les vues que vous créez avec le Concepteur de vues contiennent les éléments décrits dans le tableau suivant :

| Partie | Description |
|:--- |:--- |
| Vignettes | Affichées dans la page **Vue d’ensemble** de votre espace de travail Log Analytics. Chaque vignette affiche une synthèse visuelle de la vue personnalisée qu’elle représente. Chaque type de vignette fournit une visualisation différente de vos enregistrements. Vous sélectionnez une vignette pour afficher une vue personnalisée. |
| Vue personnalisée | Affichée quand vous sélectionnez une vignette. Chaque vue contient un ou plusieurs composants de visualisation. |
| Composants de visualisation | Présente une visualisation de données dans l’espace de travail Log Analytics en fonction d’une ou plusieurs [recherches dans les journaux](log-analytics-log-searches.md). La plupart des composants incluent un en-tête, qui fournit une visualisation d’ensemble, et une liste, qui montre les premiers résultats. Chaque type de composant produit différentes visualisations des enregistrements dans l’espace de travail Log Analytics. Vous sélectionnez des éléments dans le composant pour effectuer une recherche dans les journaux qui fournit des enregistrements détaillés. |


## <a name="work-with-an-existing-view"></a>Utiliser une vue existante
Les vues créées avec le Concepteur de vues affichent les options suivantes :

![Menu Vue d’ensemble](media/log-analytics-view-designer/overview-menu.png)

Les options sont décrites dans le tableau suivant :

| Option | Description |
|:--|:--|
| Actualiser   | Actualise la vue avec les données les plus récentes. | 
| Analyse | Ouvre le [portail Analytique avancée](log-analytics-log-search-portals.md#advanced-analytics-portal) pour analyser des données avec des recherches dans les journaux. |
| Filtrer    | Définit un filtre de temps pour les données incluses dans la vue. |
| Modifier      | Ouvre la vue dans le Concepteur de vues pour modifier son contenu et sa configuration.  |
| Cloner     | Crée une vue et l’ouvre dans le Concepteur de vues. Le nom de la nouvelle vue est identique à celui de la vue d’origine, avec le mot *Copy* ajouté à la fin. |


## <a name="create-a-new-view"></a>Créer une vue
Vous pouvez créer une vue dans le Concepteur de vues en sélectionnant la vignette **Concepteur de vues** dans la page **Vue d’ensemble** de votre espace de travail Log Analytics.

![Vignette Concepteur de vues](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Utiliser le Concepteur de vues
Vous utilisez le Concepteur de vues pour créer des vues ou les modifier. 

Le Concepteur de vues comporte trois volets : 
* **Conception** : contient la vue personnalisée que vous créez ou modifiez. 
* **Contrôles** : contient les vignettes et les composants que vous ajoutez au volet **Conception**. 
* **Propriétés** : affiche les propriétés des vignettes ou des composants sélectionnés.

![Concepteur de vues](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurer la vignette de vue
Un vue personnalisée ne peut avoir qu’une seule vignette. Pour afficher la vignette active ou en sélectionner une autre, dans le volet **Contrôle** sélectionnez l’onglet **Vignette**. Le volet **Propriétés** affiche les propriétés de la vignette active. 

Vous pouvez configurer les propriétés de la vignette conformément aux informations fournies dans [Référence de la vignette](log-analytics-view-designer-tiles.md), puis cliquer sur **Appliquer** pour enregistrer les modifications.

### <a name="configure-the-visualization-parts"></a>Configurer les composants de visualisation
Une vue peut inclure une nombre quelconque de composants de visualisation. Pour ajouter des composants à une vue, sélectionnez l’onglet **Vue**, puis sélectionnez un composant de visualisation. Le volet **Propriétés** affiche les propriétés du composant sélectionné. 

Vous pouvez configurer les propriétés de la vue conformément aux informations fournies dans la [Référence du composant de visualisation](log-analytics-view-designer-parts.md), puis cliquer sur **Appliquer** pour enregistrer les modifications.

Les vues ne comprennent qu’une seule ligne de composants de visualisation. Vous pouvez réorganiser les composants existants en les faisant glisser vers un nouvel emplacement.

Vous pouvez supprimer un composant de visualisation de la vue en sélectionnant le bouton **X** dans l’angle supérieur droit du composant.


### <a name="menu-options"></a>Options de menu
Les options pour l’utilisation des vues en mode d’édition sont décrites dans le tableau suivant.

![Menu Edition](media/log-analytics-view-designer/edit-menu.png)

| Option | Description |
|:--|:--|
| Enregistrer        | Enregistre les modifications et ferme la vue. |
| Annuler      | Ignore les modifications et ferme la vue. |
| Supprimer la vue | Supprime la vue. |
| Exportation      | Exporte la vue vers un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que vous pouvez importer dans un autre espace de travail. Le nom du fichier est identique à celui de la vue, avec une extension *omsview*. |
| Importer      | Importe le fichier *omsview* que vous avez exporté à partir d’un autre espace de travail. Cette opération remplace la configuration de la vue existante. |
| Cloner       | Crée une vue et l’ouvre dans le Concepteur de vues. Le nom de la nouvelle vue est identique à celui de la vue d’origine, avec le mot *Copy* ajouté à la fin. |
| Publish     | Exporte la vue vers un fichier JSON que vous pouvez insérer dans une [solution de gestion](../operations-management-suite/operations-management-suite-solutions-resources-views.md). Le nom du fichier est identique à celui de la vue, mais avec une extension *json*. Un deuxième fichier, créé avec l’extension *resjson*, inclut des valeurs pour les ressources définies dans le fichier JSON.

## <a name="next-steps"></a>Étapes suivantes
* Ajouter des [vignettes](log-analytics-view-designer-tiles.md) à votre vue personnalisée.
* Ajouter des [composants de visualisation](log-analytics-view-designer-parts.md) à votre vue personnalisée.
