---
title: Utilisation du portail Recherche dans les journaux dans Azure Log Analytics | Microsoft Docs
description: Cet article contient un didacticiel qui explique comment créer des recherches dans les journaux et analyser les données stockées dans votre espace de travail Log Analytics à l’aide du portail Recherche dans les journaux.  Le didacticiel comprend des requêtes simples qui retournent différents types de données et une description des résultats des analyses.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 37213012e817f0fae21a47a4334a519bbbca206b
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Créer des recherches dans les journaux dans Azure Log Analytics à l’aide du portail Recherche dans les journaux

> [!NOTE]
> Cet article décrit le portail Recherche dans les journaux dans Azure Log Analytics avec le nouveau langage de requête.  Pour en savoir plus sur le nouveau langage et connaître la procédure de mise à niveau de votre espace de travail, consultez [Mettre à niveau votre espace de travail Azure Log Analytics avec la nouvelle recherche dans les journaux](log-analytics-log-search-upgrade.md).  
>
> Si votre espace de travail n’a pas été mis à niveau avec le nouveau langage de requête, consultez [Rechercher des données avec les recherches dans les journaux dans Log Analytics](log-analytics-log-searches.md) pour plus d’informations sur la version actuelle du portail Recherche dans les journaux.

Cet article contient un didacticiel qui explique comment créer des recherches dans les journaux et analyser les données stockées dans votre espace de travail Log Analytics à l’aide du portail Recherche dans les journaux.  Le didacticiel comprend des requêtes simples qui retournent différents types de données et une description des résultats des analyses.  Il est axé sur les fonctionnalités du portail Recherche dans les journaux qui permettent de modifier la requête, plutôt que de la modifier directement.  Pour plus d’informations sur la modification directe de la requête, consultez les [informations de référence sur le langage de requête](https://go.microsoft.com/fwlink/?linkid=856079).

Pour créer des recherches dans le portail Advanced Analytics plutôt que dans le portail Recherche dans les journaux, consultez [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856587) (Bien démarrer avec le portail Analytics).  Les deux portails utilisent le même langage de requête pour accéder aux mêmes données dans l’espace de travail Log Analytics.

## <a name="prerequisites"></a>Prérequis

Ce didacticiel part du principe que vous disposez déjà d’un espace de travail Log Analytics avec au moins une source connectée qui génère des données pour les requêtes à analyser.  

- Si vous n’avez pas d’espace de travail, vous pouvez en créer un gratuitement à l’aide de la procédure décrite dans [Prise en main d’un espace de travail Log Analytics](log-analytics-get-started.md).
- Connectez au moins un [agent Windows](log-analytics-windows-agent.md) ou un [agent Linux](log-analytics-linux-agents.md) à l’espace de travail.  

## <a name="open-the-log-search-portal"></a>Ouvrir le portail Recherche dans les journaux
Commencez par ouvrir le portail Recherche dans les journaux. 

1. Ouvrez le portail Azure.
2. Accédez à Log Analytics et sélectionnez votre espace de travail.
3. Sélectionnez **Recherche de journaux**.

![Bouton Recherche dans les journaux](media/log-analytics-log-search-log-search-portal/log-search-button.png)

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

![Requête simple](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Seules les premières propriétés de chaque enregistrement sont affichées.  Cliquez sur **afficher plus** pour afficher toutes les propriétés d’un enregistrement.

![Détails des enregistrements](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Définir la période
Chaque enregistrement recueilli par Log Analytics a une propriété **TimeGenerated** qui contient la date et l’heure de création de l’enregistrement.  Une requête dans le portail Recherche dans les journaux retourne uniquement les enregistrements dont la propriété **TimeGenerated** se trouve dans la période affichée sur le côté gauche de l’écran.  

Vous pouvez changer le filtre temporel en sélectionnant la liste déroulante ou en modifiant le curseur.  Le curseur affiche un graphique à barres qui indique le nombre relatif d’enregistrements pour chaque segment de temps dans la plage.  Ce segment varie en fonction de la plage.

La période par défaut est **1 jour**.  Remplacez cette valeur par **7 jours**. Le nombre total d’enregistrements devrait alors augmenter.

![Période](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrer les résultats de la requête
Sur le côté gauche de l’écran se trouve le volet de filtre, qui vous permet d’ajouter un filtrage à la requête sans la modifier directement.  Plusieurs propriétés des enregistrements retournés sont affichées, avec leurs dix premières valeurs et le nombre d’enregistrements.

Si vous travaillez avec **Event**, cochez la case en regard de **Error** sous **EVENTLEVELNAME**.   Si vous travaillez avec **Syslog**, cochez la case en regard de **err** sous **SEVERITYLEVEL**.  Cela remplace la requête par l’une des suivantes, afin de limiter les résultats aux événements d’erreur.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtrer](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Ajoutez des propriétés au volet de filtre en sélectionnant **Ajouter aux filtres** dans le menu de propriété sur l’un des enregistrements.

![Menu Ajouter au filtre](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Vous pouvez définir le même filtre en sélectionnant **Filtrer** dans le menu de propriété d’un enregistrement avec la valeur que vous souhaitez filtrer.  

L’option **Filtrer** est visible uniquement pour les propriétés dont le nom est en bleu.  Il s’agit de champs *dans lesquels une recherche peut être effectuée* et qui sont indexés pour les conditions de recherche.  Les champs en gris sont des champs *dans lesquels une recherche en texte libre peut être effectuée*. Ils ont uniquement l’option **Afficher les références**.  Cette option retourne les enregistrements qui ont cette valeur dans une propriété quelconque.

![Menu Filtrer](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Vous pouvez regrouper les résultats sur une propriété unique en sélectionnant l’option **Regrouper par** dans le menu d’enregistrement.  Cette opération ajoute un opérateur [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) à votre requête qui affiche les résultats dans un graphique.  Vous pouvez regrouper sur plusieurs propriétés, mais vous devrez modifier la requête directement.  Sélectionnez le menu d’enregistrement à côté de la propriété **Computer** et sélectionnez **Regrouper par « Computer »**.  

![Regrouper par ordinateur](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Utiliser les résultats
Le portail Recherche dans les journaux offre un large éventail de fonctionnalités pour utiliser les résultats d’une requête.  Vous pouvez trier, filtrer et regrouper les résultats pour analyser les données sans modifier la requête proprement dite.  Les résultats d’une requête ne sont pas triés par défaut.

Pour afficher les données sous forme de table qui fournit des options supplémentaires pour le filtrage et le tri, cliquez sur **Table**.  

![Vue Table](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Cliquez sur la flèche en regard d’un enregistrement pour afficher les détails de cet enregistrement.

![Trier les résultats](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Pour trier sur un champ, cliquez sur son en-tête de colonne.

![Trier les résultats](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Pour filtrer les résultats sur une valeur spécifique dans la colonne, cliquez sur le bouton de filtre et spécifiez une condition de filtre.

![Filtrer les résultats](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Pour regrouper sur une colonne, faites glisser son en-tête en haut des résultats.  Vous pouvez regrouper sur plusieurs champs en faisant glisser plusieurs colonnes vers le haut.

![Regrouper les résultats](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Utiliser des données de performances
Les données de performances pour les agents Windows et Linux sont stockées dans l’espace de travail Log Analytics dans la table **Perf**.  Les enregistrements de performances ressemblent aux autres enregistrements, et nous pouvons écrire une requête simple qui retourne tous les enregistrements de performances, tout comme avec les événements.

```
Perf
```

![Données de performances](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

Retourner plusieurs millions d’enregistrements pour tous les objets de performances et compteurs n’est cependant pas très utile.  Vous pouvez appliquer les mêmes méthodes que ci-dessus pour filtrer les données, ou simplement taper la requête suivante directement dans la zone de recherche dans les journaux.  Cela retourne uniquement les enregistrements d’utilisation des processeurs pour les ordinateurs Windows et Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilisation des processeurs](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Cela limite les données à un compteur spécifique, mais sous une forme qui n’est encore pas particulièrement utile.  Vous pouvez afficher les données dans un graphique en courbes, mais vous devez d’abord les regrouper par Computer et TimeGenerated.  Pour regrouper sur plusieurs champs, vous devez modifier la requête directement, comme suit.  Cette requête utilise la fonction [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) sur la propriété **CounterValue** pour calculer la valeur moyenne durant chaque heure.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Graphique de données de performances](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Maintenant que les données sont regroupées convenablement, vous pouvez les afficher dans un graphique en ajoutant l’opérateur [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Graphique en courbes](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le langage de requête Log Analytics, consultez [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856079) (Bien démarrer avec le portail Analytics).
- Suivez un didacticiel à l’aide du [portail Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587) pour exécuter les mêmes requêtes et accéder aux mêmes données que le portail Recherche dans les journaux.
