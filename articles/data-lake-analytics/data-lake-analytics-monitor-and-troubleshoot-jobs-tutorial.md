---
title: Dépanner les travaux Azure Data Lake Analytics à l’aide du portail Azure | Microsoft Docs
description: 'Apprenez à utiliser le portail Azure afin de dépanner les travaux Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: f6168997c449be5354bd223c516d4f929a1bf894
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Résoudre les erreurs des travaux Azure Data Lake Analytics à l’aide du portail Azure
Découvrez comment utiliser le portail Azure pour résoudre les erreurs des travaux Data Lake Analytics.

Dans ce didacticiel, vous allez identifier un problème de fichier source manquant et utiliser le portail Azure pour résoudre le problème.

## <a name="submit-a-data-lake-analytics-job"></a>Envoyer le travail Analytique Data Lake

Envoyer la tâche U-SQL suivante :

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
Le fichier source défini dans le script est **/Samples/Data/SearchLog.tsv1**, alors qu’il devrait s’agir de **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>Résolution des problèmes

**Pour voir tous les travaux**

1. À partir du portail Azure, cliquez sur **Microsoft Azure** dans le coin supérieur gauche.
2. Cliquez sur la vignette indiquant le nom de votre compte Analytique Data Lake.  Le résumé du travail est affiché sur la vignette **Gestion des tâches** .

    ![Gestion du travail Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    La gestion des tâches vous offre un aperçu de l’état du travail. Notez qu’il s’agit d’une tâche ayant échoué.
3. Cliquez sur la vignette **Gestion de la tâche** pour rechercher les travaux. Les travaux sont classés dans les catégories **En cours**, **En file d’attente** et **Terminé**. Vous devez voir le travail qui a échoué dans la section **Terminé** . Il doit être le premier sur la liste. Lorsque vous avez un grand nombre de travaux, vous pouvez cliquer sur **Filtrer** pour vous aider à localiser les travaux.

    ![Travail de filtre d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Cliquez sur le travail ayant échoué dans la liste pour ouvrir les détails du travail :

    ![Travail d’échec d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Remarquez le bouton **Renvoyer** . Après avoir résolu le problème, vous pouvez renvoyer le travail.
5. Cliquez sur la partie mise en évidence de la capture d’écran précédente pour ouvrir les détails de l’erreur.  Le résultat suivant doit s’afficher :

    ![Détails sur le travail d’échec d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Elle vous indique que le dossier source est introuvable.
6. Cliquez sur **Dupliquer le Script**.
7. Mettez à jour le chemin d’accès **FROM** sur :

    « Samples/Data/SearchLog.tsv »
8. Cliquez sur **Envoyer le travail**.

## <a name="see-also"></a>Voir aussi
* [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md)
* [Prise en main d’Analytique Data Lake à l’aide d’Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main d’Analytique Data Lake Azure et U-SQL à l’aide de Visual Studio.](data-lake-analytics-u-sql-get-started.md)
* [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
