---
title: Alertes de journal dans Azure Monitor - Alerts (préversion) | Microsoft Docs
description: Déclenchez des e-mails et des notifications, appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions de requêtes complexes spécifiées sont remplies pour Azure Alerts (préversion).
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 0cee8bf77e0facc12159b823152b8859ce5cedd8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Alertes de journal dans Azure Monitor - Alerts (préversion)
Cet article décrit en détail le fonctionnement des règles d’alerte des requêtes Analytics dans Azure Alerts (préversion) et décrit les différences entre les divers types de règles d’alerte de journal. Pour plus d’informations sur les Alertes Métrique à l’aide des Journaux, reportez-vous à la rubrique [Alertes Métrique en quasi temps réel](monitoring-near-real-time-metric-alerts.md)

Actuellement, Azure Alerts (préversion) prend en charge les alertes de journal sur des requêtes provenant d’[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) et d’[Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> Actuellement, l’alerte de journal dans Azure Alerts (préversion) ne prend pas en charge les requêtes portant sur plusieurs espaces de travail ou applications.

Les utilisateurs peuvent également perfectionner leurs requêtes dans la plateforme Analytics de leur choix dans Azure, puis les *importer pour les utiliser dans Alerts (en version préliminaire) en enregistrant la requête*. Procédure à suivre :
- Pour Application Insights : accédez au portail Analytics, puis validez la requête et ses résultats. Enregistrez-la ensuite sous un nom unique dans *Requêtes partagées*.
- Pour Log Analytics : accédez à Recherche dans les journaux, puis validez la requête et ses résultats. Enregistrez-la ensuite sous un nom unique dans n’importe quelle catégorie.

Lorsque vous [créez une alerte de journal dans Alerts (version préliminaire)](monitor-alerts-unified-usage.md), la requête enregistrée est alors répertoriée en tant que type de signal **Journal (Requête enregistrée)**, comme illustré dans l’exemple ci-dessous : ![Requête enregistrée importée dans Alerts](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> L’utilisation de la commande **Journal (Requête enregistrée)** permet d’effectuer une importation dans Alerts. Par conséquent, les modifications effectuées par la suite dans Analytics ne seront pas reflétées dans les règles d’alerte enregistrés, et inversement.

## <a name="log-alert-rules"></a>Règles d'alerte de journal

Les alertes sont créées par Azure Alerts (préversion) pour exécuter automatiquement des requêtes de journal à intervalles réguliers.  Si les résultats de la requête de journal répondent à des critères particuliers, un enregistrement d’alerte est généré. La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus, comme l’envoi de données à une application externe à l’aide d’un [webhook json](monitor-alerts-unified-log-webhook.md), de façon proactive, en utilisant les [Groupes d’actions](monitoring-action-groups.md). Les différents types de règles d’alerte utilisent une logique différente pour effectuer cette analyse.

Les règles d’alerte sont définies par les détails suivants :

- **Requête de journal**.  La requête qui s’exécute chaque fois que la règle d’alerte est déclenchée.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée.
- **Fenêtre de temps**.  Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution.  La fenêtre de temps peut être toute valeur comprise entre 5 et 1 440 minutes ou 24 heures. Par exemple, si la fenêtre de temps est définie sur 60 minutes et que la requête est exécutée à 13h15, seuls les enregistrements créés entre 12h15 et 13h15 sont retournés.
- **Fréquence**.  Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être égale ou inférieure à la fenêtre de temps.  Si la valeur est supérieure à celle de la fenêtre de temps, vous risquez de manquer des enregistrements.<br>Par exemple, imaginons une fenêtre de temps de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h et 13 h 30 ne seront jamais analysés.
- **Seuil**.  Les résultats de la recherche dans les journaux sont évalués pour déterminer si une alerte doit être créée.  Le seuil est différent pour chaque type de règle d’alerte.

Chaque règle d’alerte Log Analytics appartient à l’un des deux types existants.  Chacun de ces types est décrit en détail dans les sections suivantes.

- **[Nombre de résultats](#number-of-results-alert-rules)**. Alerte unique créée lorsque le nombre d’enregistrements renvoyés par la recherche dans les journaux dépasse un nombre spécifié.
- **[Mesure métrique](#metric-measurement-alert-rules)**.  Alerte créée pour chaque objet des résultats de la recherche dans les journaux dont les valeurs dépassent le seuil spécifié.

Les différences entre les types de règles d’alerte sont présentées ci-dessous.

- La règle d’alerte **Nombre de résultats crée toujours une alerte unique, tandis que la règle d’alerte **Mesure métrique** crée une alerte pour chaque objet dépassant le seuil.
- Les règles d’alerte **Nombre de résultats** créent une alerte quand le seuil est dépassé une seule fois. Les règles d’alerte **Mesure métrique** peuvent créer une alerte lorsque le seuil est dépassé un certain nombre de fois au cours d’un intervalle de temps spécifique.

## <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats
Les règles d’alerte **Nombre de résultats** créent une alerte unique lorsque le nombre d’enregistrement renvoyés par la requête de recherche dépasse le seuil spécifié. Ce type de règle d’alerte est idéal pour la gestion des événements, par exemple les journaux des événements Windows, les journaux Syslog, la réponse WebApp et les journaux personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés dans une fenêtre de temps spécifique.

**Seuils** : le seuil pour une règle d’alerte **Nombre de résultats est supérieur ou inférieur à une valeur particulière.  Si le nombre d’enregistrements renvoyés par cette recherche dans les journaux satisfait à ce critère, une alerte est créée.

Pour déclencher une alerte sur un événement unique, définissez le nombre de résultats sur une valeur supérieure à 0 et vérifiez si un événement spécifique a été créé depuis la dernière exécution de la requête. Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés dans une fenêtre de temps spécifique.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne pas un de ces événements dans une fenêtre de temps spécifique, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur une valeur **inférieure à 1**.

### <a name="example"></a>Exemples
Imaginez que vous cherchiez à savoir à quel moment votre application web renvoie aux utilisateurs une réponse avec le code 500, autrement dit une erreur interne au serveur. Il conviendrait de créer une règle d’alerte paramétrée comme suit :  
**Requête :** requests | where resultCode == "500"<br>
**Fenêtre de temps :**  30 minutes<br>
**Fréquence des alertes :** 5 minutes<br>
**Valeur de seuil :** supérieure à 0<br>

L’alerte exécute alors la requête toutes les 5 minutes, avec 30 minutes de données, pour rechercher tous les enregistrements associés à un code de résultat 500. Si un seul enregistrement est trouvé, elle déclenche l’alerte et lance l’action configurée.

## <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

Les règles d’alerte **Mesure métrique** créent une alerte pour chaque objet d’une requête dont la valeur dépasse un seuil spécifié.  Elles se distinguent des règles d’alerte **Nombre de résultats** comme suit.

**Fonction d'agrégation** : détermine le calcul à effectuer et, éventuellement, un champ numérique à agréger.  Par exemple, **count()** renvoie le nombre d’enregistrements dans la requête, et **avg(CounterValue)** renvoie la moyenne du champ CounterValue sur l’intervalle.

> [!NOTE]

> La fonction d’agrégation dans une requête doit être nommée/appelée AggregatedValue et fournir une valeur numérique. 


**Champ de groupe** : un enregistrement avec une valeur agrégée est créé pour chaque instance de ce champ, et une alerte peut être générée pour chacun.  Par exemple, si vous souhaitez générer une alerte pour chaque ordinateur, vous pourriez utiliser **by Computer**.   

> [!NOTE]

> Pour la mesure de métriques, les règles d’alerte basées sur Application Insights, vous pouvez spécifier le champ de regroupement des données. Pour ce faire, utilisez l’option **Agréger sur** dans la définition de règle.   

**Intervalle** : définit l’intervalle de temps pendant lequel les données sont agrégées.  Par exemple, si vous avez spécifié **5 minutes**, un enregistrement est créé pour chaque instance du champ de groupe agrégé à intervalles de 5 minutes sur la fenêtre de temps spécifiée pour l’alerte.
> [!NOTE]
> Une fonction Bin doit être utilisée dans la requête. Étant donné que bin() peut entraîner des intervalles inégaux, l’alerte utilisera plutôt la fonction bin_at avec l’heure appropriée lors de l’exécution, pour garantir des résultats avec un point fixe

**Seuil** : le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

#### <a name="example"></a>Exemples
Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit :  

**Requête :** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**Fenêtre de temps :**  30 minutes<br>
**Fréquence des alertes :** 5 minutes<br>
**Valeur d’agrégation :** supérieure à 90<br>
**Déclencheur d’alerte :** nombre total de violations supérieur à 5<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes.  Des exemples de données sont indiqués ci-dessous pour trois ordinateurs.

![Résultats de l’exemple de requête](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Dans cet exemple, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé 3 fois au cours de la fenêtre de temps.  Si le **déclencheur d’alerte** était remplacé par **Consécutif**, une alerte serait créée uniquement pour srv03, car il a dépassé le seuil sur 3 échantillons consécutifs.


## <a name="next-steps"></a>Étapes suivantes
* Comprendre les [actions Webhook pour les alertes de journal](monitor-alerts-unified-log-webhook.md)
* [Consulter une présentation d’Azure Alerts (préversion)](monitoring-overview-unified-alerts.md)
* En savoir plus sur l’[utilisation d’Azure Alerts (préversion)](monitor-alerts-unified-usage.md)
* En savoir plus sur [Application Insights](../application-insights/app-insights-analytics.md)
* En savoir plus sur [Log Analytics](../log-analytics/log-analytics-overview.md).    
