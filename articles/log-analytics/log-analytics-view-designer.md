---
title: "Créer des vues pour analyser les données dans Azure Log Analytics | Microsoft Docs"
description: "Le Concepteur de vues de Log Analytics permet de créer des vues personnalisées affichées dans le portail Azure, qui contiennent différentes visualisations de données dans l’espace de travail Log Analytics. Cet article contient une présentation du Concepteur de vues et des procédures de création et modification des vues personnalisées."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics
Le Concepteur de vues de [Log Analytics](log-analytics-overview.md) permet de créer des vues personnalisées dans le portail Azure, qui contiennent différentes visualisations de données de votre espace de travail Log Analytics. Cet article contient une présentation du Concepteur de vues et des procédures de création et modification des vues personnalisées.

Autres articles disponibles concernant le Concepteur de vues :

* [Référence de vignette](log-analytics-view-designer-tiles.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées.
* [Référence des composants de visualisation](log-analytics-view-designer-parts.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées.

>[!NOTE]
> Si votre espace de travail a été mis à niveau vers le [nouveau langage de requête Log Analytics](log-analytics-log-search-upgrade.md), les requêtes de toutes les vues doivent être écrites à l’aide du [nouveau langage de requête](https://go.microsoft.com/fwlink/?linkid=856078).  Toutes les vues créées avant la mise à niveau de l’espace de travail sont automatiquement converties.

## <a name="concepts"></a>Concepts
Les vues sont affichées dans la page **Vue d’ensemble** de votre espace de travail Log Analytics dans le portail Azure.  La vignette de chaque vue personnalisée est affichée par ordre alphabétique avec les vignettes pour les solutions installées dans le même espace de travail.

![Page Vue d’ensemble](media/log-analytics-view-designer/overview-page.png)

Les vues créées avec le Concepteur de vues contiennent les éléments répertoriés dans le tableau suivant.

| Partie | DESCRIPTION |
|:--- |:--- |
| Vignette |Affichée dans la page Vue d’ensemble de votre espace de travail Log Analytics.  Inclut un résumé visuel des informations contenues dans la vue personnalisée.  Les différents types de vignettes fournissent différentes visualisations d’enregistrements.  Cliquez sur la vignette pour ouvrir la vue personnalisée. |
| Vue personnalisée |Affichée lorsque l’utilisateur clique sur la vignette.  Contient un ou plusieurs composants de visualisation. |
| Composants de visualisation |Visualisation de données dans l’espace de travail Log Analytics en fonction d’une ou plusieurs [recherches dans les journaux](log-analytics-log-searches.md).  La plupart des composants incluent un en-tête qui fournit une visualisation d’ensemble et une liste des premiers résultats.  Les différents types de composants produisent différentes visualisations d’enregistrements dans l’espace de travail Log Analytics.  Cliquez sur des éléments dans le composant pour effectuer une recherche de journal fournissant des enregistrements détaillés. |


## <a name="work-with-an-existing-view"></a>Utiliser une vue existante
Quand vous ouvrez une vue créée avec le Concepteur de vues, elle comporte un menu avec les options décrites dans le tableau suivant.

![Menu Vue d’ensemble](media/log-analytics-view-designer/overview-menu.png)


| Option | DESCRIPTION |
|:--|:--|
| Actualiser   | Actualiser la vue avec les données les plus récentes. | 
| Analyse | Ouvrir le [portail Analytique avancée](log-analytics-log-search-portals.md#advanced-analytics-portal) pour analyser les données avec des recherches dans les journaux.(log-analytics-log-search-portals.md#advanced-analytics-portal). |
| Filtrer    | Définir un filtre de temps pour les données incluses dans la vue. |
| Modifier      | Ouvrir la vue dans le Concepteur de vues pour modifier son contenu et sa configuration.   |
| Cloner     | Créer une vue et l’ouvrir dans le Concepteur de vues.  La nouvelle vue porte le même nom que la vue d’origine, avec le mot « Copy » ajouté à la fin. |


## <a name="create-a-new-view"></a>Créer une vue
Créer une vue dans le **Concepteur de vues** en cliquant sur la vignette du Concepteur de vues dans la page Vue d’ensemble de votre espace de travail Log Analytics dans le portail Azure.

![Vignette Concepteur de vues](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Utilisation du Concepteur de vues
Que vous créiez une vue ou modifiiez une vue existante, vous travaillerez dans le Concepteur de vues.  

Le Concepteur de vues comporte trois volets.  Le volet **Conception** contient la vue personnalisée que vous créez ou modifiez.  Vous ajoutez des vignettes et des composants en les faisant glisser du volet **Contrôle** vers le volet **Conception**.  Le volet **Propriétés** affiche les propriétés de la vignette ou du composant sélectionnés.

![Concepteur de vues](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Vignette Configurer la vue
Un vue personnalisée ne peut avoir qu’une seule vignette.  Pour afficher la vignette active ou en sélectionner une autre, dans le volet **Contrôle** sélectionnez l’onglet **Vignette**.  Le volet **Propriétés** affiche les propriétés de la vignette active.  Configurez les propriétés de la vignette conformément aux informations détaillées de la [Référence de la vignette](log-analytics-view-designer-tiles.md), puis cliquez sur **Appliquer** pour enregistrer les modifications.

### <a name="configure-visualization-parts"></a>Configurer les composants de visualisation
Une vue peut inclure une nombre quelconque de composants de visualisation.  Sélectionnez l’onglet **Vue**, puis un composant de visualisation à ajouter à la vue.  Le volet **Propriétés** affiche les propriétés du composant sélectionné.  Configurez les propriétés de la vue conformément aux informations détaillées de la [Référence du composant de visualisation](log-analytics-view-designer-parts.md), puis cliquez sur **Appliquer** pour enregistrer les modifications.

Les vues ne comprennent qu’une seule ligne de composants de visualisation.  Réorganisez les composants existants dans une vue en cliquant dessus, puis en les faisant glisser vers un nouvel emplacement.

Vous pouvez supprimer un composant de visualisation de la vue en cliquant sur le bouton **X** dans l’angle supérieur droit du composant.


### <a name="menu-options"></a>Options de menu
Quand vous travaillez avec une vue en mode édition, vous disposez des options de menu décrites dans le tableau suivant.

![Menu Edition](media/log-analytics-view-designer/edit-menu.png)

| Option | DESCRIPTION |
|:--|:--|
| Enregistrer        | Enregistrer vos modifications et fermer la vue. |
| Annuler      | Ignorer vos modifications et fermer la vue. |
| Supprimer la vue | Supprimer la vue. |
| Exporter      | Exporter la vue vers un [modèle Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que vous pouvez importer dans un autre espace de travail.  Le nom du fichier celui de la vue, avec l’extension *omsview*. |
| Importer      | Importer un fichier *omsview* que vous avez exporté à partir d’un autre espace de travail.  Cette opération remplace la configuration de la vue existante. |
| Cloner       | Créer une vue et l’ouvrir dans le Concepteur de vues.  La nouvelle vue porte le même nom que la vue d’origine, avec le mot « Copy » ajouté à la fin. |
| Publier     | Exporter la vue vers un fichier JSON qui peut être inséré dans une [solution de gestion](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  Le nom du fichier sera celui de la vue, avec l’extension *json*. Un deuxième fichier est créé avec l’extension *resjson*. Il inclut des valeurs pour les ressources définies dans le fichier JSON.

## <a name="next-steps"></a>Étapes suivantes
* Ajouter des [vignettes](log-analytics-view-designer-tiles.md) à votre vue personnalisée.
* Ajouter des [composants de visualisation](log-analytics-view-designer-parts.md) à votre vue personnalisée.
