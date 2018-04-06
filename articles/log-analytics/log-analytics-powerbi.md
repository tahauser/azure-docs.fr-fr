---
title: Importation de données Azure Log Analytics dans Power BI | Microsoft Docs
description: Power BI est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Cet article explique comment configurer l'importation de données Log Analytics dans Power BI et les configurer pour s'actualiser automatiquement.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importation de données Azure Log Analytics dans Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Vous pouvez importer les résultats d'une recherche dans les journaux Log Analytics dans un jeu de données Power BI afin de bénéficier de ses fonctionnalités telles que la combinaison de données provenant de différentes sources et le partage de rapports sur le Web et sur des appareils mobiles.

## <a name="overview"></a>Vue d'ensemble
Pour importer les données d'un espace de travail Log Analytics dans Power BI, créez un jeu de données dans Power BI à partir d'une requête de recherche dans les journaux dans Log Analytics.  La requête est exécutée chaque fois que le jeu de données est actualisé.  Vous pouvez ensuite créer des rapports Power BI qui utilisent les informations du jeu de données.  Pour créer le jeu de données dans Power BI, vous exportez votre requête depuis Log Analytics vers le [langage Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Vous l'utilisez ensuite pour créer une requête dans Power BI Desktop, avant de la publier dans Power BI en tant que jeu de données.  Les détails de ce processus sont décrits ci-dessous.

![Log Analytics vers Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Exporter une requête
Commencez par créer une [recherche de journal](log-analytics-log-search-new.md) qui renvoie les données Log Analytics que vous voulez ajouter au jeu de données Power BI.  Vous exportez ensuite cette requête dans le [langage Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) qui peut être utilisé par Power BI Desktop.

1. Créez la recherche de journal Log Analytics pour extraire les informations de votre jeu de données.
2. Si vous utilisez le portail de recherche de journal, cliquez sur **Power BI**.  Si vous utilisez le portail Analytics, sélectionnez **Exporter** > **Requête Power BI (M)**.  Ces deux options exportent la requête vers un fichier texte intitulé **PowerBIQuery.txt**. 

    ![Exporter une recherche de journal](media/log-analytics-powerbi/export-logsearch.png) ![Exporter une recherche de journal](media/log-analytics-powerbi/export-analytics.png)

3. Ouvrez le fichier texte et copiez son contenu.

## <a name="import-query-into-power-bi-desktop"></a>Importation d'une requête dans Power BI Desktop
Power BI Desktop est une application de bureau qui vous permet de créer des jeux de données et des rapports pouvant être publiés sur Power BI.  Vous pouvez également l'utiliser pour créer une requête à l'aide du langage Power Query exporté à partir de Log Analytics. 

1. Installez [Power BI Desktop](https://powerbi.microsoft.com/desktop/) si vous ne l'avez pas déjà, puis ouvrez l'application.
2. Sélectionnez **Obtenir les données** > **Requête vide** pour ouvrir une nouvelle requête.  Sélectionnez ensuite **Éditeur avancé** puis collez le contenu du fichier dans la requête. Cliquez sur **Done**.

    ![Requête Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La requête s'exécute puis les résultats s'affichent.  Vous pouvez être invité à entrer vos informations d'identification pour vous connecter à Azure.  
6. Entrez un nom descriptif pour la requête.  La valeur par défaut est **Query1**. Cliquez sur **Fermer et appliquer** pour ajouter le jeu de données au rapport.

    ![Nom Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publier vers Power BI
Lorsque vous publiez sur Power BI, un jeu de données et un rapport sont créés.  Si vous créez un rapport dans Power BI Desktop, celui-ci sera publié avec vos données.  Sinon, un rapport vide sera créé.  Vous pouvez modifier le rapport dans Power BI, ou en créer un basé sur le jeu de données.

8. Créez un rapport basé sur vos données.  Consultez la [documentation Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) si vous avez besoin d'aide.  Lorsque vous être prêt à envoyer le rapport à Power BI, cliquez sur **Publier**.  À l'invite, sélectionnez une destination dans votre compte Power BI.  À moins de vouloir choisir une destination spécifique, utilisez **Mon espace de travail**.

    ![Publication Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Une fois la publication terminée, cliquez sur **Ouvrir dans Power BI** pour ouvrir Power BI avec votre nouveau jeu de données.


### <a name="configure-scheduled-refresh"></a>Configuration d'une actualisation planifiée
Le jeu de données créé dans Power BI contiendra les mêmes informations que celles que vous avez déjà consultées dans Power BI Desktop.  Vous devez régulièrement actualiser le jeu de données pour réexécuter la requête et la remplir avec les dernières informations de Log Analytics.  

1. Cliquez sur l'espace de travail dans lequel vous avez chargé votre rapport puis sélectionnez le menu **Jeux de données**. Sélectionnez le menu contextuel en regard de votre nouveau jeu de données puis choisissez **Paramètres**. Sous **Informations d'identification de la source de données**, vous devriez voir un message indiquant que les informations d'identification ne sont pas valides.  Cela est dû au fait que vous n'avez pas encore fourni d'informations d'identification pour le jeu de données à utiliser lors de l'actualisation de ses informations.  Cliquez sur **Modifier les informations d'identification** et spécifiez les informations d'identification pour accéder à Log Analytics.

    ![Planification Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. Sous **Actualisation planifiée**, activez l'option pour **conserver vos données à jour**.  Vous pouvez aussi modifier la **fréquence d'actualisation** et une ou plusieurs heures spécifiques pour exécuter l'actualisation.

    ![Actualisation Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment les [recherches de journaux](log-analytics-log-searches.md) peuvent vous aider à générer des requêtes pouvant être exportées vers Power BI.
* Découvrez comment utiliser [Power BI](http://powerbi.microsoft.com) pour créer des visualisations basées sur des exportations Log Analytics.
