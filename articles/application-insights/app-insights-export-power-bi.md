---
title: "Exportation vers Power BI à partir d’Application Insights | Microsoft Docs"
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
ms.openlocfilehash: ae204b79be228d55b30bb543dd25efdd9c3f0436
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Alimentation de Power BI à partir d’Application Insights
[Power BI](http://www.powerbi.com/) est une suite d’outils d’analyse métiers permettant d’analyser les données et de partager les informations. Chaque périphérique bénéficie de tableaux de bord riches. Vous pouvez combiner des données provenant de nombreuses sources, notamment des requêtes Analytics d’[Azure Application Insights](app-insights-overview.md).

Pour exporter des données d’Application Insights vers Power BI, nous vous recommandons trois méthodes. Vous pouvez les utiliser séparément ou ensemble.

* [**Adaptateur Power BI**](#power-pi-adapter) : configurez un tableau de bord complet des données de télémétrie à partir de votre application. L’ensemble de graphiques est prédéfini, mais vous pouvez ajouter vos propres requêtes à partir d’autres sources.
* [**Exporter des requêtes Analytics**](#export-analytics-queries) : écrivez une requête souhaitée à l’aide d’Analytics ou à partir des entonnoirs d’utilisation et exportez-la vers Power BI. Vous pouvez placer cette requête sur un tableau de bord, avec d’autres données.
* [**Exportation continue et Stream Analytics**](app-insights-export-stream-analytics.md) : implique un travail supplémentaire de configuration. Cela est utile si vous souhaitez conserver vos données pendant de longues périodes. Sinon, les autres méthodes sont recommandées.

## <a name="power-bi-adapter"></a>Adaptateur Power BI
Cette méthode crée un tableau de bord complet des données de télémétrie. Le jeu de données initial est prédéfini, mais vous pouvez y ajouter plus de données.

### <a name="get-the-adapter"></a>Obtenir l’adaptateur
1. Connectez-vous à [Power BI](https://app.powerbi.com/).
2. Ouvrez **Obtenir des données**, **Services**, **Application Insights**
   
    ![Obtenir à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Fournissez les détails de votre ressource Application Insights.
   
    ![Obtenir à partir d’une source de données Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Patientez une minute à deux minutes avant l’importation des données.
   
    ![Adaptateur Power BI](./media/app-insights-export-power-bi/010.png)

Vous pouvez modifier le tableau de bord en associant des graphiques Application Insights avec ceux d’autres sources et avec des requêtes Analytics. Il existe une galerie de visualisation où vous pouvez obtenir plus de graphiques. Chaque graphique comporte des paramètres que vous pouvez définir.

Après l’importation initiale, le tableau de bord et les rapports sont mis à jour quotidiennement. Vous pouvez contrôler la planification de l’actualisation du jeu de données.

## <a name="export-analytics-queries"></a>Exporter des requêtes Analytics
Cet itinéraire permet d’écrire la requête Analytics souhaitée ou de l’exporter à partir des entonnoirs d’utilisation, puis de l’exporter vers un tableau de bord Power BI. (Vous pouvez ajouter au tableau de bord créé par l’adaptateur).

### <a name="one-time-install-power-bi-desktop"></a>Une fois : installez Power BI Desktop
Pour importer votre requête Application Insights, vous utilisez la version pour ordinateur de bureau de Power BI. Ensuite vous pouvez la publier sur le web ou vers votre espace de travail cloud Power BI. 

Installez [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exporter une requête Analytics
1. [Ouvrez Analytics et écrivez votre requête](app-insights-analytics-tour.md).
2. Testez et affinez la requête jusqu'à ce que vous soyez satisfait des résultats.

   **Vérifiez que la requête s’exécute correctement dans Analytics avant de l’exporter.**
3. Dans le menu **Exporter**, choisissez **Power BI (M)**. Enregistrez le fichier texte.
   
    ![Exporter une requête Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Dans Power BI Desktop, sélectionnez **Obtenir des données, Requête vide** puis, dans l’éditeur de requête, sous **Affichage**, sélectionnez **Éditeur de requêtes avancé**.

    Collez le script de langage M exporté dans l’Éditeur de requêtes avancé.

    ![Éditeur de requêtes avancé](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Vous devrez peut-être fournir des informations d’identification pour autoriser Power BI à accéder à Azure. Utilisez « Compte professionnel » pour vous connecter avec votre compte Microsoft.
   
    ![Fournissez les informations d’identification Azure pour permettre à Power BI d’exécuter votre requête Application Insights.](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Si vous devez vérifier les informations d’identification, utilisez la commande de menu Paramètres de la source de données dans l’Éditeur de requête. Veillez à spécifier les informations d’identification que vous utilisez pour Azure, qui peuvent-être différentes de vos informations d’identification pour Power BI.)
2. Choisissez une visualisation de votre requête et sélectionnez les champs pour l’axe x, l’axe y et la dimension de segmentation.
   
    ![Sélectionner une visualisation](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publiez votre rapport sur votre espace de travail cloud Power BI. À partir de là, vous pouvez incorporer une version synchronisée dans d’autres pages web.
   
    ![Sélectionner une visualisation](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Actualisez le rapport manuellement selon des intervalles ou configurez une actualisation planifiée dans la page des options.

### <a name="export-a-funnel"></a>Exporter un entonnoir
1. [Créez votre entonnoir](usage-funnels.md).
2. Cliquez sur le bouton Power BI. 

   ![Bouton Power BI](./media/app-insights-export-power-bi/button.png)
   
3. Dans Power BI Desktop, sélectionnez **Obtenir des données, Requête vide** puis, dans l’éditeur de requête, sous **Affichage**, sélectionnez **Éditeur de requêtes avancé**.

   ![Requête vide](./media/app-insights-export-power-bi/blankquery.png)

   Collez le script de langage M exporté dans l’Éditeur de requêtes avancé. 

   ![Éditeur de requêtes avancé](./media/app-insights-export-power-bi/advancedquery.png)

4. Sélectionnez des éléments dans la requête et choisissez la visualisation en entonnoir.

   ![Sélectionner la séquence et entonnoir](./media/app-insights-export-power-bi/selectsequence.png)

5. Changez le titre pour le rendre plus significatif et publiez votre rapport sur votre espace de travail cloud Power BI. 

   ![Changer le titre](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="401-or-403-unauthorized"></a>401 ou 403 non autorisé 
Cela peut se produire si votre jeton d’actualisation n’a pas été mis à jour. Essayez les opérations suivantes pour vérifier que vous avez toujours accès. Si vous avez accès et que l’actualisation des informations d’identification échoue, ouvrez un ticket de support.

1. Connectez-vous au portail Azure et vérifiez que vous avez accès à la ressource.
2. Essayez d’actualiser les informations d’identification du tableau de bord.

### <a name="502-bad-gateway"></a>502 Passerelle incorrecte
Cela est généralement dû à une requête Analytics qui renvoie trop de données. Essayez d’utiliser une plage de temps plus réduite ou, en utilisant les fonctions [ago](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) ou [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) de [projeter](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator) les champs dont vous avez besoin.

Si la réduction du jeu de données provenant de la requête Analytics ne vous convient pas, utilisez l’[API](https://dev.applicationinsights.io/documentation/overview) pour en extraire un plus grand. Voici les instructions permettant de convertir l’export M-Query pour utiliser l’API.

1. Créez une [clé d’API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Mettez à jour le script Power BI M que vous avez exporté d’Analytics en remplaçant l’URL ARM par l’API AI (voir l’exemple ci-dessous).
   * Remplacez **https://management.azure.com/subscriptions/...**
   * par **https://api.applicationinsights.io/beta/apps/...**
3. Enfin, mettez à jour les informations d’identification de base et utilisez votre clé d’API.
  

**Script existant**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script mis à jour**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>À propos de l’échantillonnage
Si votre application envoie beaucoup de données, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et envoyer seulement un pourcentage de vos données de télémétrie. Il en est de même si vous avez défini manuellement l’échantillonnage dans le Kit SDK ou sur ingestion. [En savoir plus sur l’échantillonnage.](app-insights-sampling.md)


## <a name="next-steps"></a>Étapes suivantes
* [Power BI - En savoir plus](http://www.powerbi.com/learning/)
* [Didacticiel Analytics](app-insights-analytics-tour.md)

