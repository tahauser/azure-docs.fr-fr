---
title: "Exécuter des requêtes sur différents espaces de travail Azure Log Analytics | Microsoft Docs"
description: "Cet article explique comment interroger plusieurs espaces de travail et une application App Insights spécifique dans votre abonnement."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>Guide pratique pour exécuter des requêtes sur différents espaces de travail Log Analytics

Avant, avec Azure Log Analytics, vous pouviez analyser les données uniquement dans l’espace de travail actif, ce qui limitait la capacité à interroger plusieurs espaces de travail définis dans l’abonnement.  

Maintenant, vous pouvez interroger non seulement plusieurs espaces de travail Log Analytics, mais également des données d’une application Application Insights spécifique dans le même groupe de ressources, un autre groupe de ressources ou un autre abonnement. Cela vous donne une vue de vos données à l’échelle du système.  Ce type de requête n’est possible que sur le [Portail avancé](log-analytics-log-search-portals.md#advanced-analytics-portal), et non sur le Portail Azure.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Interrogation de plusieurs espaces de travail Log Analytics à partir d’Application Insights
Pour référencer un autre espace de travail dans votre requête, utilisez l’identificateur [*workspace*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()). Pour une application Application Insights, utilisez l’identificateur [*app*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

Par exemple, la première requête retourne les nombres synthétiques de mises à jour requises par leur classement dans la table Update à partir de l’espace de travail actif et d’un autre espace de travail nommé *contosoretail-it*.  Le deuxième exemple de requête retourne le nombre de demandes effectuées par rapport à une application nommée *fabrikamapp* dans Application Insights. 

### <a name="identifying-workspace-resources"></a>Identification des ressources d’espace de travail
Il est possible d’identifier un espace de travail de plusieurs manières :

* Nom de la ressource : il s’agit d’un nom convivial de l’espace de travail, parfois appelé *nom du composant*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >L’identification d’un espace de travail par son nom suppose que celui-ci soit unique parmi tous les abonnements accessibles. Si plusieurs applications portent le nom spécifié, la requête échoue en raison de l’ambiguïté. Dans ce cas, vous devez utiliser l’un des autres identificateurs.

* Nom qualifié : il s’agit du « nom complet » de l’espace de travail, composé du nom de l’abonnement, du groupe de ressources et du nom du composant au format suivant : *nomAbonnement/groupeRessources/nomComposant*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Étant donné que les noms des abonnements Azure ne sont pas uniques, cet identificateur peut être ambigu. 
    >

* ID de l’espace de travail : il s’agit de l’identificateur unique et immuable affecté à chaque espace de travail, représenté sous la forme d’un identificateur global unique (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* ID de ressource Azure : c’est l’identité unique de l’espace de travail, définie par Azure. Vous utilisez l’ID de ressource quand le nom de la ressource est ambigu.  Pour les espaces de travail, le format est : */subscriptions/idAbonnement/resourcegroups/groupeRessources/providers/microsoft.OperationalInsights/workspaces/nomComposant*.  

    Par exemple : 
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Identification d’une application

Vous pouvez identifier une application dans Application Insights avec l’expression *app(Identifier)*.  L’argument *Identifier* spécifie l’application à l’aide de l’un éléments suivants :

* Nom de la ressource : il s’agit d’un nom convivial de l’application, parfois appelé *nom du composant*.  

    `app("fabrikamapp")`

* Nom qualifié : il s’agit du « nom complet » de l’application, composé du nom de l’abonnement, du groupe de ressources et du nom du composant au format suivant : *nomAbonnement/groupeRessources/nomComposant*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Étant donné que les noms des abonnements Azure ne sont pas uniques, cet identificateur peut être ambigu. 
    >

* ID : GUID de l’application.

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* ID de ressource Azure : identité unique de l’application, définie par Azure. Vous utilisez l’ID de ressource quand le nom de la ressource est ambigu. Le format est le suivant : */subscriptions/ID_abonnement/resourcegroups/Groupe_Ressources/providers/microsoft. OperationalInsights/components/Nom_Composant*.  

    Par exemple : 
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>étapes suivantes

Pour connaître toutes les options de syntaxe des requêtes disponibles dans Log Analytics, consultez les [Informations de référence sur la Recherche dans les journaux avec Log Analytics](https://docs.loganalytics.io/docs/Language-Reference).    