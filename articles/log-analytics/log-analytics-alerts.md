---
title: "Présentation des alertes dans Azure Log Analytics | Microsoft Docs"
description: "Les alertes dans Log Analytics identifient des informations importantes dans votre référentiel OMS et peuvent de façon proactive vous informer sur des problèmes ou appeler des actions pour tenter de les corriger.  Cet article décrit les différents types de règles d’alerte et comment elles sont définies."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2018
ms.author: bwren
ms.openlocfilehash: 07e8312d5e113eeb9016dcc832b1cf66f8001c5f
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>Comprendre les alertes dans Log Analytics

Les alertes identifient des informations importantes dans votre référentiel Log Analytics.  Cet article traite de la conception et de certaines des décisions à prendre en la matière. Plusieurs éléments entrent en ligne de compte, notamment la fréquence de collecte des données recherchées, les retards aléatoires d’ingestion des données en raison de la latence du réseau ou de la capacité de traitement et la validation des données dans le référentiel Log Analytics.  Il détaille le fonctionnement des règles d’alerte dans Log Analytics et décrit les différences entre les types de règles d’alerte.

Pour plus d’informations sur le processus de création de règles d’alerte, consultez les articles suivants :

- Créer des règles d’alerte avec le [portail Azure](log-analytics-alerts-creating.md)
- Créer des règles d’alerte avec le [modèle Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Créer des règles d’alerte avec l’[API REST](log-analytics-api-alerts.md)

## <a name="considerations"></a>Considérations

Pour des informations détaillées sur la fréquence de collecte des données selon les solutions et les types de données, consultez la section [Détails sur la collecte des données](log-analytics-add-solutions.md#data-collection-details) de l’article sur la vue d’ensemble des solutions. Comme indiqué dans cet article, la collecte peut avoir lieu aussi bien une fois tous les sept jours que *lors de la notification*. Il est important de comprendre et de prendre en compte la fréquence de collecte des données avant de configurer une alerte. 

- La fréquence de collecte détermine la fréquence à laquelle l’agent OMS installé sur les machines envoie des données à Log Analytics. Par exemple, si la fréquence de collecte est de 10 minutes et qu’aucun autre retard ne se produit dans le système, l’horodatage des données transmises peut avoir lieu à n’importe quel moment entre 0 et 10 minutes, avant que les données ne soient ajoutées au référentiel et ne puissent faire l’objet de recherches dans Log Analytics.

- Avant de pouvoir déclencher une alerte, les données doivent être écrites dans le référentiel, ce qui les rend disponibles pour recherche. En raison de la latence décrite ci-dessus, la fréquence de collecte diffère du moment où les données sont disponibles pour répondre aux demandes. Par exemple, si les données peuvent être collectées avec précision toutes les 10 minutes, elles ne seront disponibles dans le référentiel qu’à intervalles irréguliers. En théorie, les données collectées toutes les 0, 10 et 20 minutes peuvent faire l’objet d’une recherche respectivement toutes les 25, 28 et 35 minutes, ou selon un intervalle dont la régularité dépend de la latence d’ingestion. Le pire cas pour ces délais est documenté dans le [Contrat de niveau de service de Log Analytics](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1). Cependant, il n’inclut pas les délais introduits par la fréquence de collecte ou la latence du réseau entre l’ordinateur et le service Log Analytics.


## <a name="alert-rules"></a>Règles d'alerte

Les alertes sont créées par des règles dédiées qui exécutent automatiquement des recherches dans les journaux à intervalles réguliers.  Si les résultats de la recherche répondent à des critères particuliers, un enregistrement d’alerte est généré.  La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive.  Les différents types de règles d’alerte utilisent une logique différente pour effectuer cette analyse.

![Alertes Log Analytics](media/log-analytics-alerts/overview.png)

En raison de la latence constatée dans l’ingestion des données du journal, le temps absolu qui s’écoule entre l’indexation des données et le moment où elles sont disponibles et peuvent faire l’objet d’une recherche n’est pas toujours prévisible.  Ainsi, au moment de définir les règles d’alerte, vous devez prendre en compte la disponibilité en quasi temps réel des données collectées.    

Un compromis est nécessaire entre la fiabilité des alertes et leur réactivité. Vous pouvez choisir de configurer les paramètres d’alerte pour limiter les fausses alertes et les alertes manquantes. Vous pouvez aussi choisir les paramètres d’alerte pour répondre rapidement aux conditions en cours d’analyse, ce qui aura parfois pour effet de générer de fausses alertes ou des alertes manquantes.

Les règles d’alerte sont définies par les détails suivants :

- **Recherche dans les journaux**.  La requête qui s’exécute chaque fois que la règle d’alerte est déclenchée.  Les enregistrements retournés par cette requête permettent de déterminer si une alerte a été créée.
- **Fenêtre de temps**.  Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution.  Il peut s’agir de toute valeur comprise entre 5 minutes et 24 heures. Cette plage doit être assez étendue pour tenir compte des délais raisonnables de l’ingestion. La fenêtre de temps doit avoir une durée deux fois supérieure au délai le plus long que vous souhaitez gérer.<br> Par exemple, si vous voulez recevoir des alertes fiables pour des délais de 30 minutes, la plage doit être d’une heure.  

    Si cette plage est trop limitée, les symptômes rencontrés sont de deux types.

    - **Alertes manquantes**. Supposons que le délai d’ingestion est de 15 minutes dans la plupart des cas, et de 60 minutes à certaines occasions.  Si la fenêtre de temps est définie sur 30 minutes, une alerte manque dans les cas où le délai est de 60 minutes. En effet, les données ne sont pas disponibles et ne peuvent pas faire l’objet d’une recherche au moment de l’exécution de la requête d’alerte. 
   
        >[!NOTE]
        >Il est impossible de diagnostiquer la raison pour laquelle l’alerte est manquante. Par exemple, dans le cas ci-dessus, les données sont écrites dans le référentiel 60 minutes après l’exécution de la requête d’alerte. Le jour suivant, si vous constatez qu’une alerte était manquante et si la requête s’exécute alors sur le bon intervalle de temps, les critères de recherche du journal devraient correspondre au résultat. Il semble que l’alerte aurait dû être déclenchée. En réalité, l’alerte n’a pas été déclenchée, car les données n’étaient pas encore disponibles au moment où la requête d’alerte a été exécutée. 
        >
 
    - **Fausses alertes**. Parfois, les requêtes d’alerte sont conçues pour identifier l’absence d’événements. C’est par exemple le cas lorsque vous recherchez des pulsations manquantes au moment où une machine virtuelle est mise hors ligne. Comme nous l’avons dit plus haut, si la pulsation ne peut pas faire l’objet d’une recherche dans la fenêtre de temps définie pour l’alerte, une alerte est alors générée. En effet, les données de pulsation n’étaient pas disponibles pour recherche. Entendez par là qu’elles étaient absentes. Cela équivaut à une machine virtuelle mise hors ligne de manière légitime. Elle ne génère aucune donnée de pulsation. L’exécution de la requête le jour suivant dans la fenêtre de temps correspondante indique qu’il y avait bien des pulsations, mais que le système d’alerte a échoué. En réalité, les pulsations ne pouvaient pas encore faire l’objet d’une recherche, car la fenêtre de temps pour l’alerte était trop limitée.  

- **Fréquence**.  Spécifie la fréquence d’exécution et d’utilisation de la requête pour rendre les alertes plus réactives dans le cas d’une utilisation normale. Cette valeur peut être comprise entre 5 minutes et 24 heures et doit être inférieure ou égale à la fenêtre de temps définie pour l’alerte.  Si la valeur est supérieure à celle de la fenêtre de temps, vous risquez de manquer des enregistrements.<br>Si l’objectif est la fiabilité pour des délais atteignant 30 minutes et que le délai d’attente normal est de 10 minutes, la fenêtre de temps doit être d’une heure et la fréquence de 10 minutes. Cela déclenche une alerte concernant les données dont le délai d’ingestion est de 10 minutes, de 10 à 20 minutes après le moment où les données d’alerte sont générées.<br>Pour éviter de créer plusieurs alertes pour les mêmes données, la fenêtre de temps étant trop étendue, vous pouvez utiliser l’option [Supprimer les alertes](log-analytics-tutorial-response.md#create-alerts). Elle vous permet de supprimer des alertes au moins pendant la fenêtre de temps définie.
  
- **Seuil**.  Les résultats de la recherche dans les journaux sont évalués pour déterminer si une alerte doit être créée.  Le seuil est différent pour chaque type de règle d’alerte.

Chaque règle d’alerte Log Analytics appartient à l’un des deux types existants.  Chacun de ces types est décrit en détail dans les sections suivantes.

- **[Nombre de résultats](#number-of-results-alert-rules)**. Alerte unique créée lorsque le nombre d’enregistrements renvoyés par la recherche dans les journaux dépasse un nombre spécifié.
- **[Mesure métrique](#metric-measurement-alert-rules)**.  Alerte créée pour chaque objet des résultats de la recherche dans les journaux dont les valeurs dépassent le seuil spécifié.

Les différences entre les types de règles d’alerte sont présentées ci-dessous.

- La règle d’alerte **Nombre de résultats** crée toujours une alerte unique, tandis que la règle d’alerte **Mesure métrique** crée une alerte pour chaque objet dépassant le seuil.
- Les règles d’alerte **Nombre de résultats** créent une alerte quand le seuil est dépassé une seule fois. Les règles d’alerte **Mesure métrique** peuvent créer une alerte lorsque le seuil est dépassé un certain nombre de fois au cours d’un intervalle de temps spécifique.

## <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats
Les règles d’alerte **Nombre de résultats** créent une alerte unique lorsque le nombre d’enregistrement renvoyés par la requête de recherche dépasse le seuil spécifié.

### <a name="threshold"></a>Seuil
Le seuil pour une règle d’alerte **Nombre de résultats** est simplement supérieur ou inférieur à une valeur particulière.  Si le nombre d’enregistrements renvoyés par cette recherche dans les journaux satisfait à ce critère, une alerte est créée.

### <a name="scenarios"></a>Scénarios

#### <a name="events"></a>Événements
Ce type de règle d’alerte est idéal pour la gestion des événements, par exemple les journaux des événements Windows, les journaux Syslog et les journaux personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés dans une fenêtre de temps spécifique.

Pour associer une alerte à un seul événement, définissez le nombre de résultats sur une valeur supérieure à 0, et la fréquence et la fenêtre de temps sur 5 minutes.  Cette configuration exécute la requête toutes les cinq minutes et vérifie si un événement spécifique a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut rallonger le temps qui s’écoule entre l’événement collecté et l’alerte créée.

Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés dans une fenêtre de temps spécifique.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne pas un de ces événements dans une fenêtre de temps spécifique, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur **Inférieur à 1**.

#### <a name="performance-alerts"></a>Alertes de performances
[données de performances](log-analytics-data-sources-performance-counters.md) sont stockées sous la forme d’enregistrements dans le référentiel OMS.  Si vous souhaitez être averti quand un compteur de performances dépasse un seuil spécifique, ce seuil doit être inclus dans la requête.

Par exemple, pour être averti quand le processeur s’exécute à plus de 90 % de ses capacités, utilisez une requête comme la suivante et définissez le seuil de la règle d’alerte sur **Supérieur à 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Pour être averti lorsque la moyenne d’exécution du processeur dépasse 90 % de ses capacités pendant dans une fenêtre de temps spécifique, utilisez une requête comme la suivante avec la [commande measure](log-analytics-search-reference.md#commands) et définissez le seuil de la règle d’alerte sur **Supérieur à 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête Log Analytics](log-analytics-log-search-upgrade.md), les requêtes ci-dessus sont remplacées par les requêtes ci-dessous : `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

>[!NOTE]
> Les règles d’alerte relatives aux mesures de métrique sont actuellement en préversion publique.

Les règles d’alerte **Mesure métrique** créent une alerte pour chaque objet d’une requête dont la valeur dépasse un seuil spécifié.  Elles se distinguent des règles d’alerte **Nombre de résultats** comme suit.

#### <a name="log-search"></a>Recherche dans les journaux
Vous pouvez utiliser n’importe quelle requête pour une règle d’alerte **Nombre de résultats**, mais il existe des exigences de requête spécifiques pour la règle d’alerte Mesure métrique.  Elle doit inclure une [commande Measure](log-analytics-search-reference.md#commands) pour regrouper les résultats sur un champ spécifique. Cette commande doit inclure les éléments suivants :

- **Fonction d’agrégation**.  Détermine le calcul à effectuer et, éventuellement, un champ numérique à agréger.  Par exemple, **count()** renvoie le nombre d’enregistrements dans la requête, **avg(CounterValue)** renverra la moyenne du champ CounterValue sur l’intervalle.
- **Champ de groupe**.  Un enregistrement avec une valeur agrégée est créé pour chaque instance de ce champ, et une alerte peut être générée pour chacun.  Par exemple, si vous souhaitez générer une alerte pour chaque ordinateur, vous pourriez utiliser **by Computer**.   
- **Intervalle**.  Définit l’intervalle de temps pendant lequel les données sont agrégées.  Par exemple, si vous avez spécifié **5 minutes**, un enregistrement est créé toutes les 5 minutes pour chaque instance du champ de groupe agrégé, d’après la fenêtre de temps indiquée pour l’alerte.

#### <a name="threshold"></a>Seuil
Le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

#### <a name="example"></a>Exemple
Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit.  

**Requête :** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Fenêtre de temps :**  30 minutes<br>
**Fréquence des alertes :** 5 minutes<br>
**Valeur agrégée :** supérieure à 90<br>
**Déclencher une alerte en fonction de :** nombre total de violations supérieur à 5<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes.  Des exemples de données sont indiqués ci-dessous pour trois ordinateurs.

![Résultats de l’exemple de requête](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Dans cet exemple, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé 3 fois au cours de la fenêtre de temps.  Si l’**Alerte Déclencheur basée sur :** a été modifiée sur **Consécutive**, alors une alerte serait créée uniquement pour srv03, car il a dépassé le seuil sur 3 exemples consécutifs.

## <a name="alert-records"></a>Enregistrements d’alerte
Les enregistrements d’alerte créés par des règles d’alerte dans Log Analytics ont pour **Type** **Alert** et pour **SourceSystem** **OMS**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| type |*Alert* |
| SourceSystem |*OMS* |
| *Object*  | Les [alertes Mesure métrique](#metric-measurement-alert-rules) auront une propriété pour le champ de groupe.  Par exemple, si la recherche dans les journaux effectue un groupement sur Ordinateur, l’enregistrement de l’alerte disposera d’un champ Ordinateur avec comme valeur le nom de l’ordinateur.
| AlertName |Nom de l’alerte. |
| AlertSeverity |Niveau de gravité de l’alerte. |
| LinkToSearchResults |Lien vers la recherche dans les journaux Log Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| Requête |Texte de la requête qui a été exécutée. |
| QueryExecutionEndTime |Fin de l’intervalle de temps pour la requête. |
| QueryExecutionStartTime |Début de l’intervalle de temps pour la requête. |
| ThresholdOperator | Opérateur utilisé par la règle d’alerte. |
| ThresholdValue | Valeur utilisée par la règle d’alerte. |
| TimeGenerated |Date et heure de la création de l’alerte. |

Il existe d’autres genres d’enregistrements d’alerte créés par la [solution de gestion des alertes](log-analytics-solution-alert-management.md) et par les [exportations Power BI](log-analytics-powerbi.md).  Ces enregistrements ont tous pour **Type** **Alert**, mais se distinguent par leur propriété **SourceSystem**.


## <a name="next-steps"></a>Étapes suivantes
* Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans Log Analytics, ainsi que les alertes collectées à partir de System Center Operations Manager.
* Approfondissez vos connaissances sur les [recherches dans les journaux](log-analytics-log-searches.md) pouvant générer des alertes.
* Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
* Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.
