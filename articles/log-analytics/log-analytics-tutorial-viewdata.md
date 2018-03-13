---
title: "Consulter ou analyser les données Log Analytics collectées | Microsoft Docs"
description: "Cet article contient un didacticiel qui explique comment créer des recherches dans les journaux et comment analyser les données stockées dans votre ressource Log Analytics à partir du portail Recherche dans les journaux.  Le didacticiel comprend des requêtes simples qui retournent différents types de données et une description des résultats des analyses."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fc5dcc945750b4ab4eef337dbd96bd051bb4dd81
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="view-or-analyze-data-collected-with-log-analytics-log-search"></a>Consulter ou analyser les données collectées avec la recherche dans les journaux Log Analytics

Dans Log Analytics, vous pouvez exploiter les recherches dans les journaux en créant des requêtes destinées à analyser les données collectées, à utiliser les tableaux de bord préexistants que vous pouvez personnaliser avec des vues graphiques des recherches les plus utiles.  Maintenant que vous avez défini la collecte des données opérationnelles à partir de vos machines virtuelles Azure et des journaux d’activité, ce didacticiel va vous apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Mettre à niveau votre ressource Azure Log Analytics avec le nouveau langage de requête 
> * Effectuer une recherche simple dans les données d’événements et utiliser les fonctionnalités permettant de modifier et filtrer les résultats 
> * Apprendre à exploiter les données de performances

Pour effectuer l’exemple de ce didacticiel, vous devez disposer d’une machine virtuelle [connectée à l’espace de travail Log Analytics](log-analytics-quick-collect-azurevm.md).  

Vous pouvez créer et modifier des requêtes, ou manipuler de manière interactive les données retournées, de deux manières différentes.  Dans le cas de requêtes de base, utilisez la page Recherche dans les journaux du portail Azure. Pour les requêtes avancées, vous pouvez utiliser le portail d’analytique avancée. Pour en savoir plus sur les différences entre les fonctionnalités des deux portails, voir [Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics.](log-analytics-log-search-portals.md)

Dans ce didacticiel, nous travaillerons avec la fonction Recherche dans les journaux du portail Azure. 

## <a name="log-in-to-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="open-the-log-search-portal"></a>Ouvrir le portail Recherche dans les journaux 
Commencez par ouvrir le portail Recherche dans les journaux.   

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans le volet des abonnements Log Analytics, sélectionnez un espace de travail, puis la vignette **Recherche dans les journaux**.<br> ![Bouton Recherche dans les journaux](media/log-analytics-tutorial-viewdata/azure-portal-01.png)

Vous avez peut-être noté la présence de la bannière en haut de votre page de ressource Log Analytics du portail vous invitant à effectuer une mise à niveau.<br> ![Avis de mise à niveau Log Analytics dans le portail Azure](media/log-analytics-tutorial-viewdata/log-analytics-portal-upgradebanner.png)

Log Analytics a inauguré dernièrement un nouveau langage de requête destiné à faciliter la création de requêtes, à mettre en corrélation des données issues de différentes sources et à procéder à une analyse pour identifier rapidement les tendances ou les problèmes.

La mise à niveau est simple.  Pour lancer le processus, cliquez sur la bannière intitulée **En savoir plus et mise à niveau**.  Prenez connaissance des informations complémentaires sur la mise à niveau dans la page d’informations sur la mise à niveau, puis cliquez sur **Mettre à niveau maintenant**.

Ce processus prend quelques minutes. Pendant ce temps, vous pouvez suivre sa progression sous **Notifications** dans le menu. Pour plus d’informations, consultez [Avantages du nouveau langage de requête](log-analytics-log-search-upgrade.md#why-the-new-language).

## <a name="create-a-simple-search"></a>Créer une recherche simple
Le moyen le plus rapide de récupérer des données à utiliser consiste à faire appel à une requête simple qui retourne tous les enregistrements d’une table.  Si vous avez des clients Windows ou Linux connectés à votre espace de travail, vous aurez des données dans la table Event (Windows) ou Syslog (Linux).

Tapez l’une des requêtes suivantes dans la zone de recherche, puis cliquez sur le bouton de recherche.  

```
Event
```
```
Syslog
```

Les données sont retournées dans la vue Liste par défaut, et vous pouvez voir le nombre total d’enregistrements retournés.

![Requête simple](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-01.png)

Seules les premières propriétés de chaque enregistrement sont affichées.  Cliquez sur **afficher plus** pour afficher toutes les propriétés d’un enregistrement.

## <a name="filter-results-of-the-query"></a>Filtrer les résultats de la requête
Sur le côté gauche de l’écran se trouve le volet de filtre, qui vous permet d’ajouter un filtrage à la requête sans la modifier directement.  Plusieurs propriétés d’enregistrement s’affichent pour ce type d’enregistrement, et vous pouvez sélectionner une ou plusieurs valeurs de propriété pour affiner vos résultats de recherche.

Si vous travaillez avec **Event**, cochez la case en regard de **Error** sous **EVENTLEVELNAME**.   Si vous travaillez avec **Syslog**, cochez la case en regard de **err** sous **SEVERITYLEVEL**.  Cela remplace la requête par l’une des suivantes, afin de limiter les résultats aux événements d’erreur.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtrer](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-02.png)

Ajoutez des propriétés au volet de filtre en sélectionnant **Ajouter aux filtres** dans le menu de propriété sur l’un des enregistrements.

![Menu Ajouter au filtre](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-03.png)

Vous pouvez définir le même filtre en sélectionnant **Filtrer** dans le menu de propriété d’un enregistrement avec la valeur que vous souhaitez filtrer.  

Seule l’option **Filtrer** s’affiche pour les propriétés dont le nom apparaît en bleu quand vous placez le curseur dessus.  Il s’agit de champs *dans lesquels une recherche peut être effectuée* et qui sont indexés pour les conditions de recherche.  Les champs en gris sont des champs *dans lesquels une recherche en texte libre peut être effectuée*. Ils ont uniquement l’option **Afficher les références**.  Cette option retourne les enregistrements qui ont cette valeur dans une propriété quelconque.

Vous pouvez regrouper les résultats sur une propriété unique en sélectionnant l’option **Regrouper par** dans le menu d’enregistrement.  Cette opération ajoute un opérateur [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) à votre requête qui affiche les résultats dans un graphique.  Vous pouvez regrouper sur plusieurs propriétés, mais vous devrez modifier la requête directement.  Sélectionnez le menu d’enregistrement à côté de la propriété **Computer** et sélectionnez **Regrouper par « Computer »**.  

![Regrouper par ordinateur](media/log-analytics-tutorial-viewdata/log-analytics-portal-eventlist-04.png)

## <a name="work-with-results"></a>Utiliser les résultats
Le portail Recherche dans les journaux offre un large éventail de fonctionnalités pour utiliser les résultats d’une requête.  Vous pouvez trier, filtrer et regrouper les résultats pour analyser les données sans modifier la requête proprement dite.  Les résultats d’une requête ne sont pas triés par défaut.

Pour afficher les données sous forme de table qui fournit des options supplémentaires pour le filtrage et le tri, cliquez sur **Table**.  

![Vue Table](media/log-analytics-tutorial-viewdata/log-search-portal-table-01.png)

Cliquez sur la flèche en regard d’un enregistrement pour afficher les détails de cet enregistrement.

![Trier les résultats](media/log-analytics-tutorial-viewdata/log-search-portal-table-02.png)

Pour trier sur un champ, cliquez sur son en-tête de colonne.

![Trier les résultats](media/log-analytics-tutorial-viewdata/log-search-portal-table-03.png)

Pour filtrer les résultats sur une valeur spécifique dans la colonne, cliquez sur le bouton de filtre et spécifiez une condition de filtre.

![Filtrer les résultats](media/log-analytics-tutorial-viewdata/log-search-portal-table-04.png)

Pour regrouper sur une colonne, faites glisser son en-tête en haut des résultats.  Vous pouvez regrouper sur plusieurs champs en faisant glisser plusieurs colonnes vers le haut.

![Regrouper les résultats](media/log-analytics-tutorial-viewdata/log-search-portal-table-05.png)


## <a name="work-with-performance-data"></a>Utiliser des données de performances
Les données de performances pour les agents Windows et Linux sont stockées dans l’espace de travail Log Analytics dans la table **Perf**.  Les enregistrements de performances ressemblent à n’importe quel autre enregistrement, et nous allons écrire une requête simple qui retourne tous les enregistrements de performances, comme pour les événements.

```
Perf
```

![Données de performances](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-01.png)

Retourner plusieurs millions d’enregistrements pour tous les objets de performances et compteurs n’est cependant pas très utile.  Vous pouvez appliquer les mêmes méthodes que ci-dessus pour filtrer les données, ou simplement taper la requête suivante directement dans la zone de recherche dans les journaux.  Cela retourne uniquement les enregistrements d’utilisation des processeurs pour les ordinateurs Windows et Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilisation des processeurs](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-02.png)

Cela limite les données à un compteur spécifique, mais sous une forme qui n’est encore pas particulièrement utile.  Vous pouvez afficher les données dans un graphique en courbes, mais vous devez d’abord les regrouper par Computer et TimeGenerated.  Pour regrouper sur plusieurs champs, vous devez modifier la requête directement, comme suit.  Cette requête utilise la fonction [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) sur la propriété **CounterValue** pour calculer la valeur moyenne durant chaque heure.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Graphique de données de performances](media/log-analytics-tutorial-viewdata/log-analytics-portal-perfsearch-03.png)

Maintenant que les données sont regroupées convenablement, vous pouvez les afficher dans un graphique en ajoutant l’opérateur [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Graphique en courbes](media/log-analytics-tutorial-viewdata/log-analytics-portal-linechart-01.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à créer des recherches de base dans les journaux en vue d’analyser les données de performances et d’événements.  Passez au didacticiel suivant pour apprendre à visualiser les données en créant un tableau de bord.

> [!div class="nextstepaction"]
> [Créer et partager des tableaux de bord Log Analytics](log-analytics-tutorial-dashboards.md)