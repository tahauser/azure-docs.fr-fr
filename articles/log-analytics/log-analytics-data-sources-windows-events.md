---
title: "Collecter et analyser les journaux des événements Windows dans Azure Log Analytics | Microsoft Docs"
description: "Les journaux d’événements Windows sont une des sources de données les plus couramment utilisées par Log Analytics.  Cet article décrit comment configurer la collecte des journaux d’événements Windows et les détails des enregistrements qu’ils créent dans l’espace de travail Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: bwren
ms.openlocfilehash: 7a7deb4d7a287b2e9613e6035a7ffd7bb6f14f9c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Sources de données de journal d’événements Windows dans Log Analytics
Les journaux d’événements Windows sont les [sources de données](log-analytics-data-sources.md) les plus communément utilisées pour collecter des données à l’aide d’agents Windows puisque de nombreuses applications écrivent dans le journal d’événements Windows.  Vous pouvez collecter des événements à partir de journaux standard tels que Système et Application, ou spécifier des journaux personnalisés créés par les applications que vous souhaitez surveiller.

![Événements Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configuration des journaux d’événements Windows
Configurez les journaux des événements Windows à partir du [menu Données dans Paramètres de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Log Analytics collecte uniquement les événements des journaux d’événements Windows spécifiés dans les paramètres.  Vous pouvez ajouter un journal d’événements en tapant le nom du journal puis en cliquant sur **+**.  Pour chaque journal, seuls les événements avec les niveaux de gravité sélectionnés sont collectés.  Vérifiez les niveaux de gravité du journal que vous souhaitez collecter.  Vous ne pouvez pas fournir d’autres critères supplémentaires pour filtrer les événements.

Lorsque vous saisissez le nom d’un journal d’événements, Log Analytics suggère des noms communs de journaux d’événements. Si le journal que vous voulez ajouter n’apparaît pas dans la liste, vous pouvez l’ajouter en saisissant le nom complet du journal. Vous trouverez le nom complet du journal à l’aide de l’Observateur d’événements. Dans l’Observateur d’événements, ouvrez la page *Propriétés* du journal et copiez la chaîne du champ *Nom complet*.

![Configurer les événements Windows](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>Collecte des données
Log Analytics collecte chaque événement correspondant à un niveau de gravité sélectionné à partir d'un journal d’événements surveillé à mesure que l'événement est créé.  L’agent enregistre sa position dans chaque journal des événements à partir duquel il collecte.  Si l’agent est mis hors connexion pendant un moment, Log Analytics collecte les événements à partir de là où il s’était arrêté, même si ces événements ont été créés lorsque l’agent était hors connexion.  Il est possible que ces événements ne soient pas collectés si des événements non collectés d’un journal sont écrasés pendant que l’agent est hors connexion.

>[!NOTE]
>Log Analytics ne collecte pas les événements d’audit créés par SQL Server à partir de la source *MSSQLSERVER* avec l’ID d’événement 18453 qui contient les mots clés - *Classic* ou *Audit Success* et le mot clé *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Propriétés des enregistrements d’événements Windows
Les enregistrements d'événements Windows sont de type **Événement** et possèdent les propriétés affichées dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| Ordinateur |Nom de l'ordinateur à partir duquel l'événement a été collecté. |
| EventCategory |Catégorie de l’événement. |
| EventData |Toutes les données d'événement au format brut. |
| EventID |Numéro de l’événement. |
| EventLevel |Gravité de l'événement au format numérique. |
| EventLevelName |Gravité de l'événement au format texte. |
| EventLog |Nom du journal des événements à partir duquel l'événement a été collecté. |
| ParameterXml |Valeurs des paramètres d'événement au format XML. |
| ManagementGroupName |Nom du groupe d’administration pour les agents System Center Operations Manager.  Pour les autres agents, cette valeur est AOI-<workspace ID> |
| RenderedDescription |Description de l'événement avec les valeurs de paramètres |
| Source |Source de l’événement. |
| SourceSystem |Type d’agent auprès duquel l’événement a été collecté. <br> Ops Manager – Agent Windows, connexion directe ou géré par Operations Manager <br> Linux – Tous les agents Linux  <br> AzureStorage – Diagnostics Azure |
| TimeGenerated |Date et heure de création de l’événement dans Windows. |
| Nom d’utilisateur |Nom d'utilisateur du compte qui a consigné l'événement. |

## <a name="log-searches-with-windows-events"></a>Recherches de journaux avec des événements Windows
Le tableau suivant fournit plusieurs exemples de recherches qui extraient des enregistrements d’événements Windows.

| Requête | Description |
|:---|:---|
| Événement |Tous les événements Windows. |
| Événement &#124; où valeur EventLevelName == « erreur » |Tous les événements Windows avec la gravité de l'erreur. |
| Événement &#124; résumer count() par source |Nombre d’événements Windows par source. |
| Événement &#124; où valeur EventLevelName == « erreur » &#124; résumer count() par source |Nombre d’événements d’erreur Windows par source. |


## <a name="next-steps"></a>Étapes suivantes
* Configurez Log Analytics pour collecter d’autres [sources de données](log-analytics-data-sources.md) à analyser.
* En savoir plus sur les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions.  
* Utilisez [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les enregistrements d'événements dans des champs individuels.
* Configurez la [collecte des compteurs de performances](log-analytics-data-sources-performance-counters.md) à partir de vos agents Windows.
