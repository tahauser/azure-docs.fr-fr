---
title: "Créer des tableaux de bord personnalisés dans Azure Application Insights | Microsoft Docs"
description: "Didacticiel concernant la création de tableaux de bord d’indicateur de performance clé à l’aide d’Azure Application Insights."
keywords: 
services: application-insights
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 50d2545d5145f1d93a1ea9fed3e4f98b474d41b2
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Créer des tableaux de bord d’indicateur de performance clé à l’aide d’Azure Application Insights

Vous pouvez créer dans le portail Azure plusieurs tableaux de bord incluant des vignettes présentant des données de plusieurs ressources Azure dans différents groupes de ressources et abonnements.  Vous pouvez épingler différents graphiques et affichages d’Azure Application Insights pour créer des tableaux de bord personnalisés qui donnent une image complète de l’intégrité et des performances de votre application.  Ce didacticiel vous guide dans la création d’un tableau de bord personnalisé incluant plusieurs types de données et visualisations d’Azure Application Insights.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un tableau de bord personnalisé dans Azure
> * Ajouter une vignette à partir de la Galerie de vignettes
> * Ajouter des métriques standard au tableau de bord dans Application Insights 
> * Ajouter un graphique de métrique personnalisé Application Insights au tableau de bord
> * Ajouter les résultats d’une requête Analytics au tableau de bord 



## <a name="prerequisites"></a>configuration requise

Pour suivre ce didacticiel :

- Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Créer un tableau de bord
Un tableau de bord unique peut contenir des ressources de plusieurs applications, groupes de ressources et abonnements.  Démarrez le didacticiel en créant un tableau de bord pour votre application.  

2.  Dans l’écran principal du portail, sélectionnez **Nouveau tableau de bord**.

    ![Nouveau tableau de bord](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Tapez un nom pour le tableau de bord.
4. Examinez la **Galerie de vignettes** pour voir l’éventail des vignettes que vous pouvez ajouter à votre tableau de bord.  Outre l’ajout de vignettes de la galerie, vous pouvez épingler directement des graphiques et d’autres affichages d’Application Insights au tableau de bord.
5. Recherchez la vignette **Markdown**, puis faites-la glisser sur votre tableau de bord.  Cette vignette permet d’ajouter du texte mis en forme dans markdown, ce qui est idéal pour l’ajout d’un texte descriptif à votre tableau de bord.
6. Ajoutez du texte au propriétés de la vignette, puis redimensionnez celle-ci sur le canevas du tableau de bord.
    
    ![Modifier une vignette markdown](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Cliquez sur **Personnalisation terminée** en haut de l’écran pour quitter le mode personnalisation de vignette, puis cliquez sur **Publier les modifications** pour enregistrer vos changements.

    ![Tableau de bord avec vignette markdown](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Ajouter une vue d'ensemble de l'intégrité
Un tableau de bord contenant uniquement du texte statique n’est pas très attrayant. Ajoutez donc une vignette d’Application Insights pour présenter les informations relatives à votre application.  Vous pouvez ajouter des vignettes d’Application Insights à partir de la Galerie de vignettes, ou les épingler directement à partir des écrans d’Application Insights.  Cela vous permet de configurer des graphiques et affichages qui vous sont déjà familiers avant de les épingler à votre tableau de bord.  Commencez par ajouter la vue d'ensemble standard de l'intégrité pour votre application.  Cela ne nécessite aucune configuration et permet une personnalisation minimale du tableau de bord.


1. Sélectionnez **Application Insights** dans le menu Azure, puis sélectionnez votre application.
2. Dans la **Chronologie d'ensemble**, sélectionnez le menu contextuel, puis cliquez sur **Épingler au tableau de bord**.  Cela a pour effet d’ajouter la vignette au dernier tableau de bord consulté.  

    ![Épingler la chronologie d'ensemble](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. En haut de l’écran, cliquez sur **Afficher le tableau de bord** pour revenir à votre tableau de bord.
4. La Chronologie d'ensemble est désormais épinglée à votre tableau de bord.  Cliquez dessus et faites-la glisser vers la position de votre choix, puis cliquez sur **Personnalisation terminée** et **Publier les modifications**.  Votre tableau de bord affiche à présent une mosaïque avec des informations utiles.

    ![Tableau de bord avec Chronologie d'ensemble](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Ajouter un graphique de métrique personnalisée
Le panneau **Métriques** vous permet de tracer le graphique d’une métrique collectée par Application Insights au fil du temps avec des filtres et un regroupement facultatifs.  Comme tout autre élément dans Application Insights, vous pouvez ajouter ce graphique au tableau de bord.  Cela nécessite au préalable un peu de personnalisation.

1. Sélectionnez **Application Insights** dans le menu Azure, puis sélectionnez votre application.
1. Sélectionnez **Métriques**.  
2. Un graphique vide a déjà été créé, et vous êtes invité à ajouter une métrique.  Ajoutez une métrique au graphique, et éventuellement un filtre et un regroupement.  L’exemple ci-dessous présente le nombre de demandes de serveur regroupées par réussite.  Cela produit une vue dynamique des requêtes ayant réussi ou échoué.

    ![Ajouter une métrique](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Sélectionnez le menu contextuel du graphique, puis choisissez **Épingler au tableau de bord**.  Cela a pour effet d’ajouter l’affichage au dernier tableau de bord que vous avez utilisé.

    ![Épingler un graphique de métrique](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. En haut de l’écran, cliquez sur **Afficher le tableau de bord** pour revenir à votre tableau de bord.

4. Le graphique des métriques de la chronologie est désormais épinglé à votre tableau de bord. Cliquez dessus et faites-la glisser vers la position de votre choix, puis cliquez sur **Personnalisation terminée**, puis sur **Publier les modifications**. 

    ![Tableau de bord avec des mesures](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Metrics Explorer
**Metrics Explorer** est similaire à Métriques, mais offre davantage de possibilités de personnalisation lors de l’ajout au tableau de bord.  La solution que vous utilisez pour représenter graphiquement vos métriques dépend de votre préférence et de vos exigences.

1. Sélectionnez **Application Insights** dans le menu Azure, puis sélectionnez votre application.
1. Sélectionnez **Metrics Explorer**. 
2. Cliquez pour modifier le graphique, puis sélectionnez une ou plusieurs métriques et éventuellement une configuration détaillée.  L’exemple présente un graphique en courbes suivant le temps moyen de réponse de la page.
3. Cliquez sur l’icône d’épingle en haut à droite pour ajouter le graphique à votre tableau de bord, puis faites glisser le graphique vers sa position.

    ![Metrics Explorer](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. La vignette Metrics Explorer permet d’effectuer une personnalisation supplémentaire une fois ajoutée au tableau de bord.  Cliquez avec le bouton droit sur la vignette, puis sélectionnez **Modifier le titre** pour ajouter un titre personnalisé.  Poursuivez et apportez d’autres personnalisations si vous le souhaitez.

    ![Tableau de bord avec Metrics Explorer](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Le graphique Metrics Explorer a maintenant été ajouté à votre tableau de bord.

    ![Tableau de bord avec Metrics Explorer](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Ajouter une requête Analytics
Azure Application Insights Analytics utilise un langage de requête riche permettant d’analyser toutes les données collectées par Application Insights.  Tout comme aux graphiques et autres affichages, vous pouvez ajouter à votre tableau de bord la sortie d’une requête Analytics.   

Azure Application Insights Analytics étant un service distinct, vous devez partager votre tableau de bord de façon à ce qu’il inclue une requête Analytics.  Lorsque vous partagez un tableau de bord Azure, vous publiez celui-ci en tant que ressource Azure, ce qui permet de le mettre à la disposition d’autres utilisateurs et ressources.  

1. En haut de l’écran du tableau de bord, cliquez sur **Partager**.

    ![Publier un tableau de bord](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Conservez le **Nom du tableau de bord**, puis sélectionnez le **Nom d'abonnement** pour partager le tableau de bord.  Cliquez sur **Publier**.  Le tableau de bord est désormais à la disposition d’autres services et abonnements.  Vous pouvez éventuellement définir des utilisateurs spécifiques pouvant accéder au tableau de bord.
1. Sélectionnez **Application Insights** dans le menu Azure, puis sélectionnez votre application.
2. Cliquez sur **Analytics** en haut de l’écran pour ouvrir le portail Azure Log Analytics.

    ![Démarrer Azure Log Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Tapez la requête suivante qui renvoie les 10 pages les plus demandées et le nombre de demandes :

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Cliquez sur **OK** pour valider les résultats de la requête.
5. Cliquez sur l’icône d’épingle, puis sélectionnez le nom de votre tableau de bord.  La raison pour laquelle vous devez sélectionner un tableau de bord contrairement aux étapes précédentes où le dernier tableau de bord était utilisé, est que la console Analytics est un service distinct qui doit opérer une sélection parmi tous les tableaux de bord partagés disponibles.

    ![Épingler une requête Analytics](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Avant de revenir au tableau de bord, ajoutez une autre requête, mais cette fois sous la forme d’un graphique, afin de voir les différentes façons de visualiser une requête Analytics sur un tableau de bord.  Commencez avec la requête suivante qui résume les 10 opérations avec le plus grand nombre d’exceptions.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Sélectionnez **Graphique**, puis choisissez l’option **Anneau** pour visualiser la sortie.

    ![Graphique Analytics](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Cliquez sur l’icône d’épingle pour épingler le graphique à votre tableau de bord, en sélectionnant cette fois le lien pour revenir à votre tableau de bord.
4. Les résultats des requêtes sont à présent ajoutés à votre tableau de bord au format que vous avez sélectionné.  Cliquez sur chaque élément et faites-le glisser en position, puis cliquez sur **Modification terminée**.
5. Cliquez avec le bouton droit sur chacun des vignettes, puis sélectionnez **Modifier le titre** pour leur donner un titre descriptif.

    ![Tableau de bord avec Analytics](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Cliquez sur **Publier les modifications** pour valider les modifications apportées à votre tableau de bord qui comprend désormais une série de graphiques et de visualisations d’Application Insights.


## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris à créer des tableaux de bord personnalisés, jetez un coup d’œil au reste de la documentation d’Application Insights, notamment à une étude de cas.

> [!div class="nextstepaction"]
> [Diagnostics profonds](app-insights-devops.md)
