---
title: "Analyser l’utilisation des données dans Log Analytics | Microsoft Docs"
description: "Utilisez le tableau de bord Utilisation de Log Analytics pour afficher la quantité de données envoyées au service Log Analytics pour résoudre les problèmes empêchant d’envoyer de grandes quantités de données."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2018
ms.author: magoedte
ms.openlocfilehash: 8d6baa6b46290c7a332f5cf780c7f70680fb6a2c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analyser l’utilisation des données dans Log Analytics
Log Analytics inclut des informations sur la quantité de données collectées, les systèmes qui envoient les données et les différents types de données envoyées.  Utilisez le tableau de bord **Utilisation de Log Analytics** pour voir la quantité de données envoyées au service Log Analytics. Le tableau de bord affiche la quantité de données collectées par chaque solution et la quantité de données que vos ordinateurs envoient.

## <a name="understand-the-usage-dashboard"></a>Comprendre le tableau de bord Utilisation
Le tableau de bord **Utilisation de Log Analytics** affiche les informations suivantes :

- Volume de données
    - Volume de données dans le temps (en fonction de la portée temporelle actuelle)
    - Volume de données par solution
    - Données non associées à un ordinateur
- Ordinateurs
    - Ordinateurs envoyant des données
    - Ordinateurs sans données dans les dernières 24 heures
- Offres
    - Nœuds Insight & Analytics
    - Nœuds d’automatisation et de contrôle
    - Nœuds de sécurité  
- Performances
    - Temps nécessaire pour recueillir et indexer les données  
- Liste de requêtes

![tableau de bord utilisation](./media/log-analytics-usage/usage-dashboard01.png)

### <a name="to-work-with-usage-data"></a>Utilisation des données d’utilisation
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Log Analytics**.  
    ![Hub Azure](./media/log-analytics-usage/hub.png)
3. Le tableau de bord **Log Analytics** affiche la liste de vos espaces de travail. Sélectionnez un espace de travail.
4. Dans le tableau de bord *Espace de travail*, cliquez sur **Utilisation de Log Analytics**.
5. Dans le tableau de bord **Utilisation de Log Analytics**, cliquez sur **Heure : 24 dernières heures** pour modifier l’intervalle de temps.  
    ![Intervalle de temps](./media/log-analytics-usage/time.png)
6. Affichez les panneaux de catégorie d’utilisation qui montrent les domaines qui vous intéressent. Choisissez un panneau, puis cliquez dessus pour afficher plus de détails dans [Recherche de journal](log-analytics-log-searches.md).  
    ![Exemple de panneau d’utilisation des données](./media/log-analytics-usage/blade.png)
7. Dans le tableau de bord Recherche de journal, passez en revue les résultats renvoyés par la recherche.  
    ![exemple de recherche de journal d’utilisation](./media/log-analytics-usage/usage-log-search.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Créer une alerte lorsque la collection de données est plus volumineuse que prévu
Cette section décrit la création d’une alerte si :
- Le volume de données dépasse une quantité spécifiée.
- Le volume de données est censé dépasser une quantité spécifiée.

Les [alertes](log-analytics-alerts-creating.md) Log Analytics utilisent des requêtes de recherche. La requête suivante obtient un résultat quand plus de 100 Go de données sont collectés dans les dernières 24 heures :

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La requête suivante utilise une formule simple pour prévoir le moment où plus de 100 Go de données seront envoyés en une journée : 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pour alerter sur un volume de données différent, remplacez la valeur de 100 dans les requêtes par le nombre de Go pour lequel vous souhaitez créer une alerte.

Utilisez les étapes décrites dans [Création d’une règle d’alerte](log-analytics-alerts-creating.md#create-an-alert-rule) pour être averti lorsque la collection de données est plus volumineuse que prévu.

Lors de la création de l’alerte pour la première requête, lorsque plus de 100 Go de données sont collectés en 24 heures, définissez :  
- **Nom** sur *Data volume greater than 100 GB in 24 hours* (Volume de données supérieur à 10 Go en 24 heures)  
- **Gravité** : *Avertissement*  
- **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`   
- **Fenêtre de temps** sur *24 heures*
- **Fréquence de l’alerte** : une heure, car les données d’utilisation ne sont mises à jour qu’une fois par heure.
- **Générer l’alerte selon** sur *nombre de résultats*
- **Nombre de résultats** sur *Greater than 0* (Supérieur à 0)

Utilisez les étapes décrites dans [Ajouter des actions à des règles d’alerte dans Log Analytics](log-analytics-alerts-actions.md) pour configurer une action de messagerie, de webhook ou de runbook pour la règle d’alerte.

Lors de la création de l’alerte pour la seconde requête, lorsqu’il est prévu que plus de 100 Go de données seront collectés en 24 heures, définissez :
- **Nom** à *Data volume expected to greater than 100 GB in 24 hours* (Volume de données attendu supérieur à 10 Go en 24 heures)
- **Gravité** sur *Avertissement*
- **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
- **Fenêtre de temps** sur *3 Hours* (3 heures)
- **Fréquence des alertes** sur une heure, car les données d’utilisation sont uniquement mises à jour une fois par heure.
- **Générer l’alerte selon** sur *nombre de résultats*
- **Nombre de résultats** sur *Greater than 0* (Supérieur à 0)

Lorsque vous recevez une alerte, utilisez les étapes de la section suivante pour résoudre les problèmes à l’origine d’une utilisation plus importante que prévu.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Résolution des problèmes à l’origine d’une utilisation plus importante que prévu
Le tableau de bord Utilisation vous aide à identifier pourquoi l’utilisation (et donc les coûts) est plus importante que ce que vous attendez.

Une utilisation plus importante est due à l’un des éléments suivants, voire les deux :
- Plus de données que prévu sont envoyées à Log Analytics
- Plus de nœuds que prévu envoient des données à Log Analytics

### <a name="check-if-there-is-more-data-than-expected"></a>Vérifier s’il y a plus de données que prévu 
Deux sections clés de la page Utilisation vous permettent de déterminer ce qui est à l’origine du trop grand nombre de données collectées.

Le graphique *Volume de données dans le temps* représente le volume total de données envoyées et les ordinateurs envoyant le plus de données. Le graphique en haut vous permet de voir si votre utilisation des données globales augmente, reste stable ou diminue. La liste des ordinateurs montre les 10 ordinateurs envoyant le plus de données.

Le graphique *Volume de données par solution* représente le volume de données envoyé par chaque solution et les solutions envoyant le plus de données. Le graphique en haut montre affiche le volume total des données envoyées par chaque solution au fil du temps. Ces informations vous permettent de déterminer si une solution envoie plus de données, environ la même quantité de données, ou moins de données au fil du temps. La liste des solutions montre les 10 solutions envoyant le plus de données. 

Ces deux graphiques affichent toutes les données. Certaines données sont facturables, et les autres données sont libres. Pour vous concentrer uniquement sur les données facturables, modifiez la requête sur la page de recherche pour inclure `IsBillable=true`.  

![graphiques de volume de données](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Examinez le graphique *Volume de données dans le temps*. Pour voir les solutions et les types de données qui envoient le plus de données d’un ordinateur spécifique, cliquez sur le nom de l’ordinateur. Cliquez sur le nom du premier ordinateur dans la liste.

Dans la capture d’écran suivante, le type de données *Gestion des journaux/Performance* envoie le plus de données pour l’ordinateur.<br><br> ![volume de données pour un ordinateur](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Ensuite, revenez au tableau de bord *Utilisation* et examinez le graphique *Volume de données par solution*. Pour voir les ordinateurs envoyant le plus de données pour une solution, cliquez sur le nom de la solution dans la liste. Cliquez sur le nom de la première solution dans la liste. 

La capture d’écran suivante confirme que l’ordinateur *acmetomcat* envoie le plus de données pour la solution de gestion des journaux.<br><br> ![volume de données pour une solution](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Si nécessaire, effectuez des analyses supplémentaires pour identifier des volumes importants au sein d’une solution ou d’un type de données. Les exemples de requêtes incluent :

+ une solution de **sécurité**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ une solution de **gestion des journaux**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ le type de données **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ le type de données **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ le type de données **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ le type de données **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Pour réduire le volume de journaux collectés, procédez comme suit :

| Source du volume de données important | Comment réduire le volume de données |
| -------------------------- | ------------------------- |
| Événements de sécurité            | Sélectionnez [les événements de sécurité courants ou minimaux](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Modifier la stratégie d’audit de sécurité pour collecter les événements nécessaires uniquement. Plus particulièrement, examinez la nécessité de collecter des événements pour : <br> - [plateforme de filtrage de l’audit](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [registre de l’audit](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [système de fichiers de l’audit](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [objet de noyau d’audit](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [manipulation du descripteur de l’audit](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [stockage amovible de l’audit](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| Compteurs de performances       | Modifiez la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md) de façon à : <br> - Réduire la fréquence de collecte <br> - Réduire le nombre de compteurs de performance |
| Journaux d’événements                 | Modifiez la [configuration du journal d’événements](log-analytics-data-sources-windows-events.md) de façon à : <br> - Réduire le nombre de journaux des événements collectés <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations*. |
| syslog                     | Modifiez la [configuration du syslog](log-analytics-data-sources-syslog.md) de façon à : <br> - Réduire le nombre d’installations collectées <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations* et *Débogage*. |
| AzureDiagnostics           | Modifiez la collection de journaux de ressources pour : <br> - Réduire le nombre de journaux d’envoi de ressources à Log Analytics <br> - Collecter uniquement les journaux nécessaires |
| Données de solution d’ordinateurs n’ayant pas besoin de la solution | Utilisez le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Vérifier s’il y a plus de nœuds que prévu
Si vous utilisez le niveau tarifaire *Par nœud (OMS)*, vous êtes facturé en fonction du nombre de nœuds et de solutions que vous utilisez. Vous pouvez voir le nombre de nœuds utilisés par offre dans la section *Offres* du tableau de bord d’utilisation.<br><br> ![tableau de bord utilisation](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Cliquez sur **Afficher tout...**  pour consulter la liste complète des ordinateurs envoyant des données pour l’offre sélectionnée.

Utilisez le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.


## <a name="next-steps"></a>Étapes suivantes
* Consultez [Recherche de données à l’aide de recherches de journal](log-analytics-log-searches.md) pour apprendre à utiliser le langage de recherche. Vous pouvez utiliser des requêtes de recherche pour effectuer des analyses supplémentaires sur les données d’utilisation.
* Utilisez les étapes décrites dans [Création d’une règle d’alerte](log-analytics-alerts-creating.md#create-an-alert-rule) pour être averti lorsqu’un critère de recherche est rempli.
* Utiliser le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement
* Pour configurer une règle efficace de collecte d’événements de sécurité, passez en revue [Stratégie de filtrage de Azure Security Center ](../security-center/security-center-enable-data-collection.md)
* Modifier la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md)
* Pour modifier vos paramètres de collecte d’événements, consultez [Configuration du journal des événements](log-analytics-data-sources-windows-events.md)
* Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration de syslog](log-analytics-data-sources-syslog.md)
