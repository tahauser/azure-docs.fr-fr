---
title: "Exporter vers Power BI à partir d’Azure Application Insights | Microsoft Docs"
description: "Les requêtes Analytics peuvent être affichées dans Power BI."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 19595983ba49a88d9139c85afbf38d3106d4a81d
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Alimentation de Power BI à partir d’Application Insights
[Power BI](http://www.powerbi.com/) est une suite d’outils métier permettant d’analyser les données et de partager les informations. Chaque appareil bénéficie de tableaux de bord riches. Vous pouvez combiner des données provenant de nombreuses sources, notamment des requêtes Analytics d’[Azure Application Insights](app-insights-overview.md).

Pour exporter des données d’Application Insights vers Power BI, nous vous recommandons trois méthodes. Vous pouvez les utiliser séparément ou ensemble.

* [**Adaptateur Power BI**](#power-pi-adapter). Configurez un tableau de bord complet des données de télémétrie à partir de votre application. L’ensemble de graphiques est prédéfini, mais vous pouvez ajouter vos propres requêtes à partir d’autres sources.
* [**Exporter des requêtes Analytics**](#export-analytics-queries). Écrivez une requête et exportez-la vers Power BI. Vous pouvez écrire votre requête à l’aide d’Analytics ou à partir des entonnoirs d’utilisation. Vous pouvez placer cette requête sur un tableau de bord, avec d’autres données.
* [**Exportation continue et Azure Stream Analytics**](app-insights-export-stream-analytics.md). Cette méthode est utile si vous souhaitez conserver vos données pendant de longues périodes. Dans le cas contraire, utilisez une des autres méthodes, car celle-ci implique davantage de travail de configuration.

## <a name="power-bi-adapter"></a>Adaptateur Power BI
Cette méthode crée un tableau de bord complet des données de télémétrie. Le jeu de données initial est prédéfini, mais vous pouvez y ajouter plus de données.

### <a name="get-the-adapter"></a>Obtenir l’adaptateur
1. Connectez-vous à [Power BI](https://app.powerbi.com/).
2. Ouvrez **Obtenir des données**, **Services**, puis **Application Insights**.
   
    ![Captures d’écran de l’obtention à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Fournissez les détails de votre ressource Application Insights.
   
    ![Capture d’écran de l’obtention à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Patientez une minute à deux minutes avant l’importation des données.
   
    ![Capture d’écran de l’adaptateur Power BI](./media/app-insights-export-power-bi/010.png)

Vous pouvez modifier le tableau de bord en associant des graphiques Application Insights avec ceux d’autres sources et avec des requêtes Analytics. Vous pouvez obtenir plus de graphiques dans la galerie de visualisation. Chaque graphique comporte des paramètres que vous pouvez définir.

Après l’importation initiale, le tableau de bord et les rapports sont mis à jour quotidiennement. Vous pouvez contrôler la planification de l’actualisation du jeu de données.

## <a name="export-analytics-queries"></a>Exporter des requêtes Analytics
Cet itinéraire permet d’écrire la requête Analytics souhaitée ou de l’exporter à partir des entonnoirs d’utilisation, puis de l’exporter vers un tableau de bord Power BI. (Vous pouvez ajouter au tableau de bord créé par l’adaptateur).

### <a name="one-time-install-power-bi-desktop"></a>Une fois : installez Power BI Desktop
Pour importer votre requête Application Insights, vous utilisez la version pour ordinateur de bureau de Power BI. Ensuite, vous pouvez la publier sur le web ou sur votre espace de travail cloud Power BI. 

Installez [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exporter une requête Analytics
1. [Ouvrez Analytics et écrivez votre requête](app-insights-analytics-tour.md).
2. Testez et affinez la requête jusqu'à ce que vous soyez satisfait des résultats. Vérifiez que la requête s’exécute correctement dans Analytics avant de l’exporter.
3. Dans le menu **Exporter**, choisissez **Power BI (M)**. Enregistrez le fichier texte.
   
    ![Capture d’écran d’Analytics, avec le menu Exporter mis en surbrillance](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Dans Power BI Desktop, sélectionnez **Obtenir des données** > **Requête vide**. Ensuite, dans l’éditeur de requête, sous **Afficher**, sélectionnez **Éditeur avancé**.

    Collez le script de langage M exporté dans l’Éditeur avancé.

    ![Capture d’écran de Power BI Desktop, avec l’Éditeur avancé mis en surbrillance](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Pour autoriser Power BI à accéder à Azure, vous devrez peut-être fournir des informations d’identification. Utilisez **Compte professionnel** pour vous connecter avec votre compte Microsoft.
   
    ![Capture d’écran de la boîte de dialogue Paramètres de requête Power BI](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Si vous devez vérifier les informations d’identification, utilisez la commande de menu **Paramètres de la source de données** dans l’Éditeur de requête. Veillez à spécifier les informations d’identification que vous utilisez pour Azure, qui peuvent être différentes de vos informations d’identification pour Power BI.
2. Choisissez une visualisation de votre requête et sélectionnez les champs pour l’axe x, l’axe y et la dimension de segmentation.
   
    ![Capture d’écran des options de visualisation de Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publiez votre rapport sur votre espace de travail cloud Power BI. À partir de là, vous pouvez incorporer une version synchronisée dans d’autres pages web.
   
    ![Capture d’écran de Power BI Desktop, avec le bouton Publier mis en surbrillance](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Actualisez le rapport manuellement selon des intervalles ou configurez une actualisation planifiée dans la page des options.

### <a name="export-a-funnel"></a>Exporter un entonnoir
1. [Créez votre entonnoir](usage-funnels.md).
2. Sélectionnez **Power BI**. 

   ![Capture d’écran du bouton Power BI](./media/app-insights-export-power-bi/button.png)
   
3. Dans Power BI Desktop, sélectionnez **Obtenir des données** > **Requête vide**. Ensuite, dans l’éditeur de requête, sous **Afficher**, sélectionnez **Éditeur avancé**.

   ![Capture d’écran de Power BI Desktop, avec le bouton Requête vide mis en surbrillance](./media/app-insights-export-power-bi/blankquery.png)

   Collez le script de langage M exporté dans l’Éditeur avancé. 

   ![Capture d’écran de Power BI Desktop, avec l’Éditeur avancé mis en surbrillance](./media/app-insights-export-power-bi/advancedquery.png)

4. Sélectionnez des éléments dans la requête et choisissez une visualisation en entonnoir.

   ![Capture d’écran des options de visualisation de Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Changez le titre pour le rendre plus significatif et publiez votre rapport sur votre espace de travail cloud Power BI. 

   ![Capture d’écran de Power BI Desktop, avec le changement de titre mis en surbrillance](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Résolution des problèmes

Vous pouvez rencontrer des erreurs liées aux informations d’identification ou à la taille du jeu de données. Voici quelques informations sur la manière de gérer ces erreurs.

### <a name="unauthorized-401-or-403"></a>Non autorisé (401 ou 403)
Cela peut se produire si votre jeton d’actualisation n’a pas été mis à jour. Essayez les opérations suivantes pour vérifier que vous avez toujours accès :

1. Connectez-vous au portail Azure et vérifiez que vous avez accès à la ressource.
2. Essayez d’actualiser les informations d’identification du tableau de bord.

 Si vous avez accès et que l’actualisation des informations d’identification échoue, ouvrez un ticket de support.

### <a name="bad-gateway-502"></a>Passerelle incorrecte (502)
Cela est généralement dû à une requête Analytics qui renvoie trop de données. Essayez d’utiliser un intervalle de temps plus petit pour la requête. 

Si la réduction du jeu de données provenant de la requête Analytics ne vous convient pas, envisagez d’utiliser l’[API](https://dev.applicationinsights.io/documentation/overview) pour en extraire un plus grand. Voici comment convertir l’export M-Query pour utiliser l’API.

1. Créez une [clé API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Mettez à jour le script Power BI M que vous avez exporté d’Analytics en remplaçant l’URL Azure Resource Manager par l’API Application Insights.
   * Remplacez **https://management.azure.com/subscriptions/...**
   * par **https://api.applicationinsights.io/beta/apps/...**
3. Enfin, mettez à jour les informations d’identification de base et utilisez votre clé API.
  

**Script existant**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script mis à jour**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>À propos de l’échantillonnage
Si votre application envoie beaucoup de données, vous pouvez essayer d’utiliser la fonctionnalité d’échantillonnage adaptatif pour envoyer seulement un pourcentage de vos données de télémétrie. Il en est de même si vous avez défini manuellement l’échantillonnage dans le Kit SDK ou sur ingestion. [En savoir plus sur l'échantillonnage](app-insights-sampling.md).


## <a name="next-steps"></a>Étapes suivantes
* [Power BI - En savoir plus](http://www.powerbi.com/learning/)
* [Didacticiel Analytics](app-insights-analytics-tour.md)

