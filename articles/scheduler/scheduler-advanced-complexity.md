---
title: "Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler"
description: "Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler"
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
ms.openlocfilehash: e1e45d394a4c442a4fb255ed6d838a589e98860e
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler
## <a name="overview"></a>Vue d'ensemble
La *planification*constitue le cœur d'un travail Azure Scheduler. La planification détermine quand et comment Azure Scheduler exécute le travail.

Azure Scheduler vous permet de spécifier des planifications  à usage unique et récurrentes pour un travail. Les planifications *à usage unique* se déclenchent une seule fois à un moment précis. Il s’agit en fait de planifications *récurrentes* qui ne s’exécutent qu’une seule fois. Les planifications récurrentes se déclenchent selon une fréquence prédéfinie.

Grâce à cette flexibilité, Azure Scheduler vous permet de prendre en charge un large éventail de scénarios professionnels :

* Nettoyage périodique des données : par exemple, tous les jours, supprimer tous les tweets de plus de 3 mois
* Archivage : par exemple, tous les mois, envoyer l'historique de facturation au service de sauvegarde.
* Demandes de données externes : par exemple, toutes les 15 minutes, extraire un nouveau bulletin météorologique NOAA pour le ski.
* Traitement des images : par exemple, tous les jours ouvrables, pendant les heures creuses, utiliser le cloud computing pour compresser les images téléchargées ce même jour.

Dans cet article, nous traitons d'exemples de travaux que vous pouvez créer avec Azure Scheduler. Nous fournissons les données JSON qui décrivent chaque planification. Si vous utilisez [l’API REST Scheduler](https://msdn.microsoft.com/library/mt629143.aspx), vous pouvez utiliser les mêmes données JSON pour [créer un travail Azure Scheduler](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Scénarios pris en charge
Les nombreux exemples de cette rubrique illustrent l’éventail de scénarios pris en charge par Azure Scheduler. Globalement, ces exemples illustrent comment créer des planifications pour de nombreux modèles de comportement, y compris ceux ci-dessous :

* Exécuter une seule fois à une date et une heure spécifiques
* Exécuter et répéter un nombre de fois explicites
* Exécuter immédiatement et répéter
* Exécuter et répéter tous/toutes les *n* minutes, heures, jours, semaines ou mois, en commençant à un moment spécifique
* Exécuter et répéter selon une fréquence hebdomadaire ou mensuelle, mais uniquement des jours spécifiques, des jours spécifiques de la semaine ou des jours spécifiques du mois
* Exécuter et répéter à plusieurs reprises dans une période : par exemple, le dernier vendredi et lundi de chaque mois, ou à 5h15 et 17h15 chaque jour

## <a name="dates-and-datetimes"></a>Dates et dates/heures
Les dates des travaux Azure Scheduler respectent la [spécification ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent uniquement la date.

Les références date-heure des travaux Azure Scheduler respectent la [spécification ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent des parties de date et d'heure. Une date-heure qui ne spécifie pas de décalage UTC est supposée être de type UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Procédure : Utiliser JSON et l'API REST pour la création de planifications
Pour créer une simple planification à l’aide de [l’API REST Azure Scheduler](https://msdn.microsoft.com/library/mt629143), [inscrivez votre abonnement auprès d’un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx) (le nom du fournisseur pour Scheduler est *Microsoft.Scheduler*), puis [créez une collection de travaux](https://msdn.microsoft.com/library/mt629159.aspx) et [un travail](https://msdn.microsoft.com/library/mt629145.aspx). Lorsque vous créez un travail, vous pouvez spécifier la planification et la périodicité à l'aide de JSON, comme indiqué dans l'extrait ci-dessous :

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // how often to fire
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Vue d'ensemble : Notions fondamentales du schéma de travail
Le tableau suivant fournit une vue d'ensemble des éléments majeurs liés à la périodicité et la planification d'un travail :

| **Nom JSON** | **Description** |
|:--- |:--- |
| ***startTime*** |*startTime* est une Date-Heure. Pour les planifications simples, *startTime* est la première occurrence et pour les planifications complexes, le travail ne démarre pas avant la valeur *startTime*. |
| ***recurrence*** |L’objet *recurrence* spécifie les règles de périodicité pour le travail et la périodicité selon laquelle le travail s’exécutera. L’objet de périodicité prend en charge les éléments *frequency, interval, endTime, count* et *schedule*. Si la valeur *recurrence* est définie, l’élément *frequency* est obligatoire. Les autres éléments de la valeur *recurrence* sont facultatifs. |
| ***frequency*** |La chaîne *frequency* représente l’unité de fréquence selon laquelle le travail se répète. Les valeurs prises en charge sont *« minute », « hour », « day », « week »* ou *« month ».* |
| ***interval*** |La valeur *interval* est un entier positif et indique l’intervalle de la valeur *frequency* qui détermine la fréquence d’exécution du travail. Par exemple, si la valeur *interval* est définie sur 3 et la valeur *frequency* est définie sur « week » (semaine), le travail se répète toutes les 3 semaines. Azure Scheduler prend en charge un *intervalle* maximum de 18 mois pour la fréquence mensuelle, 78 semaines pour la fréquence hebdomadaire ou 548 jours pour la fréquence quotidienne. Pour la fréquence heure et minute, la plage prise en charge est 1 <= *interval* <= 1 000. |
| ***endTime*** |La chaîne *endTime* spécifie la dernière valeur date-heure après laquelle le travail ne doit pas s’exécuter. La chaîne *endTime* ne peut pas se situer dans le passé. Si aucune valeur *endTime* ou count n’est spécifiée, le travail s’exécute à l'infini. Vous ne pouvez pas inclure *endTime* et *count* dans le même travail. |
| ***count*** |<p>La valeur *count* est un entier positif (supérieur à zéro) qui spécifie le nombre de fois où ce travail doit s’exécuter avant la fin.</p><p>La valeur *count* représente le nombre de fois où le travail s’exécute avant d’être défini comme étant terminé. Par exemple, pour un travail qui est exécuté quotidiennement avec la valeur *count* égale à 5 et une date de début égale à lundi, le travail se termine après son exécution le vendredi. Si la date de début se situe dans le passé, la première exécution est calculée à partir de l'heure de création.</p><p>Si aucune valeur *endTime* ou *count* n’est spécifiée, le travail s’exécute à l’infini. Vous ne pouvez pas inclure *endTime* et *count* dans le même travail.</p> |
| ***schedule*** |Un travail avec une fréquence spécifiée modifie sa périodicité selon une planification périodique. Un objet *schedule* contient des modifications basées sur des minutes, heures, jours de la semaine, jour du mois et numéro de semaine. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Vue d'ensemble : Valeurs par défaut, limites et exemples du schéma de travail
Après cette introduction, examinons chacun de ces éléments en détail.

| **Nom JSON** | **Type de valeur** | **Obligatoire ?** | **Valeur par défaut** | **Valeurs valides** | **Exemple** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |Chaîne |Non  |Aucun |Dates-Heures ISO-8601 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |Object |Non  |Aucun |Objet de périodicité |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |Chaîne |Oui |Aucun |"minute", "hour", "day", "week", "month" |<code>"frequency" : "hour"</code> |
| ***interval*** |Number |Oui |Aucun |1 à 1 000. |<code>"interval":10</code> |
| ***endTime*** |Chaîne |Non  |Aucun |Valeur date-heure représentant une heure dans le futur |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Number |Non  |Aucun |>= 1 |<code>"count": 5</code> |
| ***schedule*** |Object |Non  |Aucun |Objet de planification |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Présentation approfondie : *startTime*
Le tableau suivant décrit comment *startTime* contrôle la manière dont un travail est exécuté.

| **valeur startTime** | **Aucune périodicité** | **Périodicité. Aucune planification** | **Périodicité avec planification** |
|:--- |:--- |:--- |:--- |
| **Aucune heure de début** |Exécuter une fois immédiatement |Exécuter une fois immédiatement. Les exécutions suivantes sont basées sur le calcul à partir de la dernière exécution |<p>Exécuter une fois immédiatement</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p> |
| **Heure de début dans le passé** |Exécuter une fois immédiatement |<p>Calculer la première exécution ultérieure après l’heure de début, puis l’exécuter à ce moment</p><p>Les exécutions suivantes sont basées sur le calcul à partir de la dernière exécution</p><p>Consultez l’exemple après ce tableau pour obtenir une explication supplémentaire</p> |<p>Le travail *ne démarre pas avant* l’heure de début spécifiée. La première occurrence est basée sur la planification calculée à partir de l'heure de début</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p> |
| **Heure de début dans le futur ou à l'heure actuelle** |Exécuter une fois à l'heure de début spécifiée |<p>Exécuter une fois à l'heure de début spécifiée</p><p>Les exécutions suivantes sont basées sur le calcul à partir de la dernière exécution</p> |<p>Le travail *ne démarre pas avant* l’heure de début spécifiée. La première occurrence est basée sur la planification calculée à partir de l'heure de début</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p> |

Examinons un exemple de ce qui se passe lorsque *startTime* se situe dans le passé, avec la valeur *recurrence* définie, mais pas la valeur *schedule*.  Supposons que la date et l’heure actuelle sont 2015-04-08 13:00, la valeur *startTime* est 2015-04-07 14:00 et la valeur *recurrence* est tous les 2 jours (définie avec *frequency* : day et *interval* : 2) Notez que la valeur *startTime* se situe dans le passé et se produit avant l’heure actuelle

Dans ces conditions, la *première exécution* aura lieu le 2015-04-09 à 14:00\. Le moteur d'Azure Scheduler calcule les occurrences de l'exécution à partir de l'heure de début.  Toutes les instances dans le passé sont ignorées. Le moteur utilise l'instance suivante qui se produit dans le futur.  Dans ce cas, *startTime* étant 2015-04-07 à 14:00, l’instance suivante aura lieu dans les 2 jours à partir de cette date, c’est-à-dire le 2015-04-09 à 14:00.

Notez que la première exécution serait la même, que la valeur startTime soit égale à 2015-04-05 14:00 ou à 2015-04-01 14:00\. Après la première exécution, les exécutions suivantes sont calculées à l'aide de la planification, afin qu'elles aient lieu le 2015-04-11 à 14:00, puis le 2015-04-13 à 14:00, puis le 2015-04-15 à 14:00, etc.

Enfin, lorsqu'un travail a une planification, si les heures et/ou les minutes ne sont pas définies dans la planification, elles prennent la valeur par défaut des heures et/ou minutes de la première exécution, respectivement.

## <a name="deep-dive-schedule"></a>Présentation approfondie : *schedule*
D’une part, une *planification* peut *limiter* le nombre d’exécutions du travail.  Par exemple, si un travail avec une fréquence « mois » a une *planification* qui s’exécute uniquement le 31, le travail s'exécute uniquement pendant les mois qui comptent un 31<sup>ème</sup> jour.

D’autre part, une *planification* peut également *développer* le nombre d’exécutions du travail. Par exemple, si un travail avec une fréquence « mois » a une *planification* qui s’exécute les jours 1 et 2 du mois, le travail s'exécute le 1<sup>er</sup> et le 2<sup>ème</sup> jours du mois au lieu d'une seule fois par mois.

Si plusieurs éléments de planification sont spécifiés, l'ordre d'évaluation va du plus grand au plus petit : numéro de semaine, jour du mois, jour de la semaine, heure et minute.

Le tableau suivant décrit les éléments *schedule* en détail.

| **Nom JSON** | **Description** | **Valeurs valides** |
|:--- |:--- |:--- |
| **minutes** |Minutes de l'heure auxquelles le travail sera exécuté |<ul><li>Tableau d’entiers</li></ul> |
| **hours** |Heures de la journée auxquelles le travail sera exécuté |<ul><li>Tableau d’entiers</li></ul> |
| **weekDays** |Jours de la semaine auxquels le travail sera exécuté Peut uniquement être spécifié avec une fréquence hebdomadaire. |<ul><li>N’importe laquelle des valeurs ci-dessous (taille max. de tableau 7)<ul><li>Lundi</li><li>« Tuesday »</li><li>« Wednesday »</li><li>« Thursday »</li><li>« Friday »</li><li>Samedi</li><li>« Sunday »</li></ul></li></ul>*Ne* respectant pas la casse |
| **monthlyOccurrences** |Détermine les jours du mois pour l'exécution du travail. Peut uniquement être spécifié avec une fréquence mensuelle. |<ul><li>Tableau d’objets monthlyOccurrence :</li></ul> <pre>{ "day": *day*,<br />  "occurrence":*occurrence*<br />}</pre><p> *day* est le jour de la semaine où la tâche sera exécutée, par exemple, {Sunday} correspond à tous les dimanches du mois. Requis.</p><p>Occurrence est *l’occurrence* du jour au cours du mois. Par exemple, {Sunday, -1} est le dernier dimanche du mois. facultatif.</p> |
| **monthDays** |Jour du mois auquel le travail sera exécuté. Peut uniquement être spécifié avec une fréquence mensuelle. |<ul><li>Un tableau des valeurs ci-dessous</li><ul><li>Toute valeur < = -1 et > = -31.</li><li>Toute valeur >= 1 et <= 31.</li></ul></ul> |

## <a name="examples-recurrence-schedules"></a>Exemples : planifications de périodicité
Voici divers exemples de planifications de périodicité, avec un accent sur l'objet de planification et ses sous-éléments.

Toutes les planifications ci-dessous supposent que *l’intervalle* est défini sur 1\. Vous devez également prendre en compte la fréquence qui est indiquée dans la *planification*. Par exemple, vous ne pouvez pas utiliser la fréquence « day » et effectuer une modification « monthDays » dans la planification. Ces restrictions sont décrites ci-dessus.

| **Exemple** | **Description** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Exécution à 5h tous les jours. Azure Scheduler fait correspondre chaque valeur sous « hours » avec chaque valeur sous « minutes », une par une, pour créer une liste de toutes les fois où le travail doit être exécuté. |
| <code>{"minutes":[15], "hours":[5]}</code> |Exécution à 5h15 tous les jours. |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Exécution à 5h15 et 17h15 tous les jours. |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Exécution à 5h15, 5h45, 17h15 et 17h45 tous les jours |
| <code>{"minutes":[0,15,30,45]}</code> |Exécution toutes les 15 minutes. |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Exécution toutes les heures. Ce travail s'exécute toutes les heures. La minute est contrôlée par la valeur *startTime*, si elle est spécifiée ou, dans le cas contraire, par l’heure de création. Par exemple, si l'heure de début ou l'heure de création (selon le cas) est 00h25, le travail sera exécuté à 00h25, 01h25, 02h25, ..., 23h25. La planification équivaut à un travail avec la valeur *frequency* définie sur « hour », la valeur *interval* égale à 1 et aucune *planification*. La différence est que cette planification peut être utilisée avec une *fréquence* et un *intervalle* différents pour créer d’autres travaux. Par exemple, si la valeur *frequency* est définie sur « month », la planification serait exécutée une seule fois par mois, au lieu de tous les jours si la valeur *frequency* était définie sur « day » |
| <code>{minutes:[0]}</code> |Exécution toutes les heures sur l'heure. Ce travail s'exécute également toutes les heures, mais sur l'heure (par exemple, minuit, 1h, 2h, etc.) Cela équivaut à un travail avec une fréquence définie sur « hour », une heure de début avec zéro minute et aucune planification si la fréquence était définie sur « day » ; mais si la fréquence était définie sur « week » ou « month », la planification ne s'exécuterait qu'un seul jour par semaine ou par mois, respectivement. |
| <code>{"minutes":[15]}</code> |Exécution 15 minutes après l'heure toutes les heures. Exécution toutes les heures, à partir de 00h15, 1h15, 2h15, etc. et se terminant à 22h15 et 23h15. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Exécution à 17h le samedi chaque semaine. |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Exécution à 17h le lundi, mercredi et vendredi chaque semaine. |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Exécution à 17h15 et 17h45 le lundi, mercredi et vendredi chaque semaine. |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Exécution à 5h et 17h le lundi, mercredi et vendredi chaque semaine. |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Exécution à 5h15, 5h45, 17h15 et 17h45 le lundi, mercredi et vendredi chaque semaine. |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Exécution toutes les 15 minutes les jours de semaine. |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Exécution toutes les 15 minutes les jours de semaine entre 9h et 16h45. |
| <code>{"weekDays":["sunday"]}</code> |Exécution le dimanche à l'heure de début. |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Exécution le mardi et le jeudi à l'heure de début. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Exécution à 6h le 28ème jour de chaque mois (en supposant une fréquence mensuelle). |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Exécution à 6h le dernier jour du mois. Si vous souhaitez exécuter un travail le dernier jour du mois, utilisez -1 au lieu de jour 28, 29, 30 ou 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Exécution à 6h le premier et le dernier jours de chaque mois. |
| <code>{monthDays":[1,-1]}</code> |Exécution le premier et le dernier jours de chaque mois à l'heure de début. |
| <code>{monthDays":[1,14]}</code> |Exécution le premier et le quatorzième jours de chaque mois à l'heure de début. |
| <code>{monthDays":[2]}</code> |Exécution le deuxième jour du mois à l'heure de début. |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Exécution le premier vendredi de chaque mois à 5h. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Exécution le premier vendredi de chaque mois à l'heure de début. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Exécution le troisième vendredi à partir de la fin du mois, chaque mois, à l'heure de début. |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Exécution le premier et le dernier vendredi de chaque mois à 5h15. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Exécution le premier et le dernier vendredi de chaque mois à l'heure de début. |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Exécution le cinquième vendredi de chaque mois à l'heure de début. S'il n'existe pas de cinquième vendredi dans un mois, le travail ne s'exécute pas, dans la mesure où il est planifié pour s'exécuter uniquement le cinquième vendredi. Vous pouvez envisager d'utiliser -1 au lieu de 5 pour l'occurrence si vous souhaitez exécuter le travail le dernier vendredi du mois. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Exécution toutes les 15 minutes le dernier vendredi du mois. |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Exécution à 5h15, 5h45, 17h15 et 17h45 le troisième mercredi de chaque mois. |

## <a name="see-also"></a>Voir aussi
 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

