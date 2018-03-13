---
title: "Créer des planifications complexes et une périodicité avancée avec Azure Scheluler"
description: "Découvrez comment créer des planifications complexes et une périodicité avancée avec Azure Scheluler."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Créer des planifications complexes et une périodicité avancée avec Azure Scheluler

La planification constitue le cœur d’un travail Azure Scheduler. Elle détermine quand et comment Azure Scheduler exécute le travail. 

Vous pouvez utiliser Scheduler pour définir plusieurs planifications uniques et récurrentes pour un travail. Les planifications uniques se déclenchent une seule fois à un moment précis. Il s’agit en fait de planifications récurrentes qui ne s’exécutent qu’une seule fois. Les planifications récurrentes se déclenchent selon une fréquence prédéfinie.

Grâce à cette flexibilité, vous pouvez utiliser Scheduler pour un large éventail de scénarios professionnels :

* **Nettoyage périodique des données**. Par exemple, chaque jour, supprimer tous les tweets qui datent de plus de trois mois.
* **Archivage**. Par exemple, tous les mois, envoyer l’historique de facturation vers un service de sauvegarde.
* **Demandes de données externes**. Par exemple, toutes les 15 minutes, extraire un rapport météo des neiges de NOAA.
* **Traitement des images**. Par exemple, tous les jours ouvrables, pendant les heures creuses, utiliser le cloud computing pour compresser les images téléchargées ce même jour.

Dans cet article, nous traitons d’exemples de travaux que vous pouvez créer à l’aide de Scheduler. Nous fournissons les données JSON qui décrivent chaque planification. Si vous utilisez l’[API REST Scheduler](https://msdn.microsoft.com/library/mt629143.aspx), vous pouvez utiliser les mêmes données JSON pour [créer un travail Scheduler](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scénarios pris en charge
Les exemples de cet article illustrent l’éventail de scénarios pris en charge par Scheduler. Globalement, ils illustrent comment créer des planifications pour de nombreux modèles de comportement, notamment :

* Exécuter une seule fois à une date et une heure spécifiques
* Exécuter et répéter un certain nombre de fois
* Exécuter immédiatement et répéter
* Exécuter et répéter toutes les *n* minutes, heures, jours, semaines ou mois, en commençant à un moment spécifique
* Exécuter et répéter selon une fréquence hebdomadaire ou mensuelle, mais uniquement des jours spécifiques de la semaine ou du mois
* Exécuter et répéter plusieurs fois durant une période. Par exemple, le dernier vendredi et le dernier lundi de chaque mois, ou à 5h15 et à 17h15 chaque jour

## <a name="date-and-date-time"></a>Date et date-heure
Les références de date des travaux Scheduler respectent la [spécification ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent uniquement la date.

Les références de date-heure des travaux Scheduler respectent la [spécification ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent la date et l’heure. Une date-heure qui ne spécifie pas de décalage UTC est considérée comme étant d etype UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Utiliser JSON et l’API REST pour créer une planification
Pour créer une planification de base à l’aide de l’[API REST Scheduler](https://msdn.microsoft.com/library/mt629143), vous devez d’abord [inscrire votre abonnement auprès d’un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx). Le nom du fournisseur pour Scheduler est **Microsoft.Scheduler**. Ensuite, [créez une collection de travaux](https://msdn.microsoft.com/library/mt629159.aspx). Pour finir, [créez un travail](https://msdn.microsoft.com/library/mt629145.aspx). 

Quand vous créez un travail, vous pouvez spécifier la planification et la périodicité à l’aide de JSON, comme dans cet extrait :

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Notions fondamentales du schéma de travail
Le tableau suivant fournit une vue d’ensemble des principaux éléments que vous utilisez pour définir la périodicité et la planification d’un travail :

| Nom JSON | Description |
|:--- |:--- |
| **startTime** |Valeur de date-heure. Pour les planifications de base, **startTime** correspond à la première exécution. Pour les planifications complexes, le travail ne démarre pas avant **startTime**. |
| **recurrence** |Spécifie les règles de périodicité pour le travail, et la périodicité de son exécution. L’objet recurrence prend en charge les éléments suivants **frequency**, **interval**, **endTime**, **count** et **schedule**. Si **recurrence** est défini, **frequency** est obligatoire. Les autres éléments de **recurrence** sont facultatifs. |
| **frequency** |Chaîne qui représente l’unité de fréquence selon laquelle le travail se répète. Les valeurs prises en charge sont "minute", "hour", "day", "week" et "month". |
| **interval** |Entier positif. **interval** indique l’intervalle de la valeur **frequency** qui détermine la fréquence d’exécution du travail. Par exemple, si **interval** est égal à 3 et que la valeur **frequency** est définie sur "week", le travail se répète toutes les trois semaines.<br /><br />Scheduler prend en charge une valeur **interval** maximale de 18 pour la fréquence mensuelle, 78 pour la fréquence hebdomadaire, et 548 pour la fréquence quotidienne. Pour la fréquence heure et minute, la plage prise en charge est 1 <= **interval** <= 1 000. |
| **endTime** |Chaîne qui spécifie la date-heure au-delà de laquelle le travail ne s’exécute pas. Vous pouvez définir une valeur **endTime** qui se trouve dans le passé. Si **endTime** et **count** ne sont pas spécifiés, le travail s’exécute à l’infini. Vous ne pouvez pas inclure à la fois **endTime** et **count** dans le même travail. |
| **count** |Entier positif (supérieur à zéro) qui spécifie le nombre de fois où ce travail s’exécute avant la fin.<br /><br />La valeur **count** représente le nombre de fois où le travail s’exécute avant d’être considéré comme étant terminé. Par exemple, pour un travail qui est exécuté quotidiennement avec une valeur **count** égale à 5 et une date de début définie sur lundi, le travail se termine après son exécution le vendredi. Si la date de début se situe dans le passé, la première exécution est calculée à partir de l'heure de création.<br /><br />Si aucune valeur **endTime** ou **count** n’est spécifiée, le travail s’exécute à l’infini. Vous ne pouvez pas inclure à la fois **endTime** et **count** dans le même travail. |
| **schedule** |Un travail avec une fréquence spécifiée modifie sa périodicité selon une planification périodique. Une valeur **schedule** contient des modifications basées sur des minutes, heures, jours de la semaine, jour du mois et numéro de semaine. |

## <a name="job-schema-defaults-limits-and-examples"></a>Valeurs par défaut, limites et exemples du schéma de travail
Plus loin dans l’article, nous examinerons chacun des éléments suivants en détail :

| Nom JSON | Type de valeur | Requis ? | Valeur par défaut | Valeurs valides | Exemple |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |chaîne |Non  |Aucun |Dates-Heures ISO 8601 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |objet |Non  |Aucun |Objet de périodicité |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |chaîne |OUI |Aucun |"minute", "hour", "day", "week", "month" |`"frequency" : "hour"` |
| **interval** |number |OUI |Aucun |1 à 1000 |`"interval":10` |
| **endTime** |chaîne |Non  |Aucun |Valeur date-heure représentant une heure dans le futur |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |number |Non  |Aucun |>= 1 |`"count": 5` |
| **schedule** |objet |Non  |Aucun |Objet de planification |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Présentation approfondie : startTime
Le tableau suivant décrit comment **startTime** contrôle la façon dont un travail s’exécute :

| Valeur startTime | Aucune périodicité | Périodicité, aucune planification | Périodicité avec planification |
|:--- |:--- |:--- |:--- |
| **Aucune heure de début** |Exécuter une fois immédiatement. |Exécuter une fois immédiatement. Lancer les exécutions suivantes calculées à partir de la dernière exécution. |Exécuter une fois immédiatement.<br /><br />Lancer les exécutions suivantes à partir d’une planification de périodicité. |
| **Heure de début dans le passé** |Exécuter une fois immédiatement. |Calculer la première exécution ultérieure après l’heure de début, puis l’exécuter à ce moment<br /><br />Lancer les exécutions suivantes calculées à partir de la dernière exécution. <br /><br />Pour plus d’informations, consultez l’exemple qui suit ce tableau. |Le travail *ne démarre pas avant* l’heure de début spécifiée. La première occurrence dépend de la planification calculée à partir de l’heure de début.<br /><br />Lancer les exécutions suivantes à partir d’une planification de périodicité. |
| **Heure de début dans le futur ou heure actuelle** |Exécuter une fois à l’heure de début spécifiée. |Exécuter une fois à l’heure de début spécifiée.<br /><br />Lancer les exécutions suivantes calculées à partir de la dernière exécution.|Le travail *ne démarre pas avant* l’heure de début spécifiée. La première occurrence dépend de la planification, calculée à partir de l’heure de début.<br /><br />Lancer les exécutions suivantes à partir d’une planification de périodicité. |

Voyons ce qui se passe quand l’heure de début **startTime** se situe dans le passé, avec une périodicité mais sans planification.  Supposez que la date et l’heure actuelles sont le 2015-04-08 à 13:00, la valeur **startTime** est le 2015-04-07 à 14:00, et la valeur **recurrence** est tous les deux jours (définie avec **frequency** : day et **interval** : 2) Notez que la valeur **startTime** se situe dans le passé et se produit avant l’heure actuelle

Dans ces conditions, la première exécution aura lieu le 2015-04-09 à 14:00. Le moteur d'Azure Scheduler calcule les occurrences de l'exécution à partir de l'heure de début. Toutes les instances dans le passé sont ignorées. Le moteur utilise l'instance suivante qui se produit dans le futur. Dans ce cas, **startTime** étant le 2015-04-07 à 14:00, l’instance suivante aura lieu deux jours après cette date, c’est-à-dire le 2015-04-09 à 14:00.

Notez que la première exécution serait la même, que la valeur **startTime** soit égale à 2015-04-05 14:00 ou à 2015-04-01 14:00\.. Après la première exécution, les exécutions suivantes sont calculées à l’aide de la planification. Elles auront lieu le 2015-04-11 à 14:00, puis le 2015-04-13 à 14:00, puis le 2015-04-15 à 14:00, et ainsi de suite.

Pour finir, quand un travail a une planification, si les heures et les minutes ne sont pas définies dans la planification, elles prennent la valeur par défaut des heures et minutes de la première exécution, respectivement.

## <a name="deep-dive-schedule"></a>Immersion : schedule
Vous pouvez utiliser **schedule** pour *limiter* le nombre d’exécutions du travail. Par exemple, si un travail avec une valeur **frequency** définie sur "month" a une planification qui s’exécute uniquement le 31, le travail s’exécute uniquement pendant les mois qui comptent un 31ème jour.

Vous pouvez également utiliser **schedule** pour *étendre* le nombre d’exécutions du travail. Par exemple, si un travail avec une valeur **frequency** définie sur "month" a une planification qui s’exécute les jours 1 et 2 du mois, le travail s’exécute le 1er et le 2ème jour du mois au lieu d’une seule fois par mois.

Si vous spécifiez plusieurs éléments de planification, l’ordre d’évaluation va du plus grand au plus petit : numéro de semaine, jour du mois, jour de la semaine, heure et minute.

Le tableau suivant décrit les éléments schedule en détail :

| Nom JSON | Description | Valeurs valides |
|:--- |:--- |:--- |
| **minutes** |Minutes de l’heure où le travail s’exécute. |Tableau d’entiers. |
| **hours** |Heures de la journée où le travail s’exécute. |Tableau d’entiers. |
| **weekDays** |Jours de la semaine où le travail s’exécute. Peut être spécifié uniquement avec une fréquence hebdomadaire. |Tableau de l’une des valeurs suivantes (la taille de tableau maximale est 7) :<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />Ne respecte pas la casse. |
| **monthlyOccurrences** |Détermine les jours du mois où le travail s’exécute. Peut être spécifié uniquement avec une fréquence mensuelle. |Tableau d’objets **monthlyOccurrence** :<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** est le jour de la semaine où le travail s’exécute. Par exemple, *{Sunday}* correspond à tous les dimanche du mois. Obligatoire.<br /><br />**occurrence** est l’occurrence du jour au cours du mois. Par exemple, *{Sunday, -1}* correspond au dernier dimanche du mois. Facultatif. |
| **monthDays** |Jour du mois où le travail s’exécute. Peut être spécifié uniquement avec une fréquence mensuelle. |Tableau des valeurs suivantes :<br />- Toute valeur <= -1 et >= -31<br />- Toute valeur >= 1 et <= 31|

## <a name="examples-recurrence-schedules"></a>Exemples : planifications de périodicité
Les exemples suivants montrent différentes planifications de périodicité. Ils se concentrent sur l’objet de planification et ses sous-éléments.

Ces planifications partent du principe que **interval** a la valeur 1\., et que les valeurs de **frequency** pour les valeurs spécifiées dans **schedule** sont correctes. Par exemple, vous ne pouvez pas utiliser une valeur **frequency** "day" et avoir une modification **monthDays** dans **schedule**. Nous décrivons ces restrictions plus haut dans l’article.

| Exemples | Description |
|:--- |:--- |
| `{"hours":[5]}` |Exécution à 5h00 tous les jours.<br /><br />Scheduler fait correspondre chaque valeur dans "hours" avec chaque valeur dans "minutes", une par une, pour créer une liste de toutes les fois où le travail s’exécute. |
| `{"minutes":[15], "hours":[5]}` |Exécution à 5h15 tous les jours. |
| `{"minutes":[15], "hours":[5,17]}` |Exécution à 5h15 et 17h15 tous les jours. |
| `{"minutes":[15,45], "hours":[5,17]}` |Exécution à 5h15, 5h45, 17h15 et 17h45 tous les jours. |
| `{"minutes":[0,15,30,45]}` |Exécution toutes les 15 minutes. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Exécution toutes les heures.<br /><br />Ce travail s'exécute toutes les heures. La minute est contrôlée par la valeur de **startTime**, si elle est spécifiée. Si aucune valeur **startTime** n’est spécifiée, les minutes sont contrôlées par l’heure de création. Par exemple, si l’heure de début ou l’heure de création (selon le cas) est 00h25, le travail s’exécute à 00h25, 01h25, 02h25, ..., 23h25.<br /><br />La planification équivaut à un travail avec une valeur **frequency** définie sur "hour", une valeur **interval** égale à 1 et aucune value **schedule**. La différence est que vous pouvez utiliser cette planification avec des valeurs **frequency** et **interval** différentes pour créer d’autres travaux. Par exemple, si **frequency** est définie sur "month", la planification s’exécute une seule fois par mois, plutôt que tous les jours (si **frequency** est définie sur "day"). |
| `{minutes:[0]}` |Exécution toutes les heures sur l’heure.<br /><br />Ce travail s’exécute également toutes les heures, mais sur l’heure (par exemple, minuit, 1h, 2h, et ainsi de suite). Cela équivaut à un travail avec une valeur **frequency** définie sur "hour", une valeur **startTime** de zéro minute, et aucune valeur **schedule**, si la fréquence est "day". Si la valeur **frequency** est définie sur "week" » ou "month", la planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. |
| `{"minutes":[15]}` |Exécution 15 minutes après l’heure toutes les heures.<br /><br />Ce travail s’exécute toutes les heures, en commençant à 00h15, 01h15, 02h15, et ainsi de suite. Il se termine à 23h15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Exécution à 17h00 le samedi chaque semaine. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Exécution à 17h00 le lundi, le mercredi et le vendredi chaque semaine. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Exécution à 17h15 et 17h45 le lundi, le mercredi et le vendredi chaque semaine. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Exécution à 5h00 et 17h00 le lundi, le mercredi et le vendredi chaque semaine. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Exécution à 5h15, 5h45, 17h15 et 17h45 le lundi, le mercredi et le vendredi chaque semaine. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Exécution toutes les 15 minutes les jours de semaine. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Exécution toutes les 15 minutes les jours de semaine entre 9h00 et 16h45. |
| `{"weekDays":["sunday"]}` |Exécution le dimanche à l’heure de début. |
| `{"weekDays":["tuesday", "thursday"]}` |Exécution le mardi et le jeudi à l’heure de début. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Exécution à 6h00 le 28e jour de chaque mois (en supposant que la valeur **frequency** est définie sur "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Exécution à 6h00 le dernier jour du mois.<br /><br />Si vous souhaitez exécuter un travail le dernier jour du mois, utilisez -1 au lieu de jour 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Exécution à 6h00 le premier et le dernier jour de chaque mois. |
| `{monthDays":[1,-1]}` |Exécution le premier et le dernier jour de chaque mois à l’heure de début. |
| `{monthDays":[1,14]}` |Exécution le premier et le quatorzième jour de chaque mois à l’heure de début. |
| `{monthDays":[2]}` |Exécution le deuxième jour du mois à l’heure de début. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Exécution le premier vendredi de chaque mois à 5h00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Exécution le premier vendredi de chaque mois à l’heure de début. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Exécution le troisième vendredi à partir de la fin du mois, chaque mois, à l’heure de début. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Exécution le premier et le dernier vendredi de chaque mois à 5h15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Exécution le premier et le dernier vendredi de chaque mois à l’heure de début. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Exécution le cinquième vendredi de chaque mois à l’heure de début.<br /><br />S’il n’y a pas de cinquième vendredi dans un mois, le travail ne s’exécute pas. Vous pouvez utiliser -1 plutôt que 5 pour l’occurrence si vous souhaitez exécuter le travail le dernier vendredi du mois. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Exécution toutes les 15 minutes le dernier vendredi du mois. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Exécution à 5h15, 5h45, 17h15 et 17h45 le troisième mercredi de chaque mois. |

## <a name="see-also"></a>Voir aussi

- [Présentation d'Azure Scheduler](scheduler-intro.md)
- [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)
- [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)
- [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)
- [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)
- [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)
- [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)
- [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)
- [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

