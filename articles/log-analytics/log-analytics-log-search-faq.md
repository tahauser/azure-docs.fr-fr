---
title: Forum aux questions pour la nouvelle recherche dans les journaux Log Analytics | Documents Microsoft
description: "Cet article fournit le forum aux questions relatives à la mise à niveau de Log Analytics au nouveau langage de requête."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Questions fréquentes (FAQ) sur la nouvelle recherche dans les journaux Log Analytics et problèmes connus

Cet article regroupe les questions fréquentes et les problèmes connus relatifs à la mise à niveau de [Log Analytics avec le nouveau langage de requête](log-analytics-log-search-upgrade.md).  Nous vous invitons à lire cet article dans son intégralité avant de vous décider à mettre à niveau votre espace de travail.


## <a name="alerts"></a>Alertes

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Question : J’ai un grand nombre de règles d’alerte. Dois-je les recréer dans la nouvelle langue après la mise à niveau ?  
Non, vos règles d’alerte sont automatiquement converties en nouveau langage de recherche pendant la mise à niveau.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Question : J’ai des règles d’alerte avec des actions de Webhook et de runbook. Fonctionneront-elles toujours après la mise à niveau ?

Non. Certaines modifications apportées aux actions de Webhook et de runbook risquent de vous obliger à revoir votre façon de gérer la charge utile. Nous avons apporté ces modifications afin de normaliser les différents formats de sortie et de réduire la taille de la charge utile. Pour plus d’informations sur ces formats, consultez la page [Ajouter des actions à des règles d’alerte dans Log Analytics](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Groupes d’ordinateurs

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Question : J’obtiens des erreurs quand j’essaie d’utiliser des groupes d’ordinateurs.  Leur syntaxe a-t-elle changé ?
Oui, la syntaxe utilisée pour les groupes d’ordinateurs change du moment que votre espace de travail est mis à niveau.  Pour plus d’informations, consultez [Groupes d’ordinateurs dans les recherches dans les journaux Log Analytics](log-analytics-computer-groups.md).


## <a name="dashboards"></a>Tableaux de bord

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Question : Puis-je toujours utiliser des tableaux de bord dans un espace de travail mis à niveau ?
Avec la mise à niveau, nous entamons un processus à l’issue duquel **Mon tableau de bord** sera déconseillé.  Vous pouvez continuer d’utiliser les vignettes que vous avez ajoutées au tableau de bord avant la mise à niveau de votre espace de travail, mais vous ne pouvez ni les modifier ni en ajouter de nouvelles.  Vous pouvez encore créer et modifier des affichages à l’aide du [Concepteur de vues](log-analytics-view-designer.md), qui comporte davantage de fonctionnalités, mais aussi créer des tableaux de bord sur le Portail Azure.


## <a name="log-searches"></a>Recherches dans les journaux

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Question : J’ai enregistré des recherches en dehors de mon espace de travail mis à niveau. Puis-je les convertir automatiquement en nouveau langage de recherche ?
Vous pouvez utiliser l’outil de conversion de langage dans la page de recherche de journal pour convertir chacune d’elles.  Il n’existe aucune méthode pour la conversion automatique de plusieurs recherches sans mise à niveau de l’espace de travail.

### <a name="question-why-are-my-query-results-not-sorted"></a>Question : Pourquoi mes résultats de requête ne sont pas triés ?
Par défaut, les résultats ne sont pas triés dans le nouveau langage de requête.  Utilisez l’[opérateur de tri](https://go.microsoft.com/fwlink/?linkid=856079) pour trier vos résultats selon une ou plusieurs propriétés.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Question : Où se trouve l’affichage des mesures après la mise à niveau ?
L’affichage des mesures offrait une représentation graphique des données de performances à partir d’une Recherche dans les journaux.  Cet affichage n’est plus disponible après la mise à niveau.  Vous pouvez utiliser [l’opérateur de rendu](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) pour mettre en forme la sortie d’une requête dans une frise chronologique.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Question : Où se trouve Minify après la mise à niveau ?
Minify est une fonctionnalité qui fournit une synthèse de vos résultats de recherche.  Une fois la mise à niveau effectuée, l’option Minify ne s’affiche plus dans le portail de recherche dans les journaux.  Vous pouvez obtenir des fonctionnalités similaires avec le nouveau langage de recherche en utilisant [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) ou [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API Recherche de journal

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Question : L’API Recherche dans les journaux est-elle mise à jour après la mise à niveau ?
[L’API Recherche dans les journaux](log-analytics-log-search-api.md) héritée ne fonctionnera plus après la mise à niveau de votre espace de travail.  Pour plus d’informations sur la nouvelle API, consultez [API REST Azure Log Analytics](https://dev.loganalytics.io/).


## <a name="portals"></a>Portails

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Question : Dois-je utiliser le nouveau portail Analytique avancée ou doit-je continuer à utiliser le portail Recherche dans les journaux ?
Vous pouvez consulter un comparatif des deux portails dans [Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics](log-analytics-log-search-portals.md).  Chacun présentant des avantages particuliers, vous pouvez choisir celui qui répond le mieux à vos besoins.  Il est courant d’écrire des requêtes dans le portail Analytique avancée et de les coller à d’autres endroits comme le Concepteur de vue.  Avant d’effectuer une telle opération, prenez connaissance des [problèmes potentiels](log-analytics-log-search-portals.md#advanced-analytics-portal).


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Question : Après la mise à niveau, je reçois une erreur quand je tente d’exécuter des requêtes et j’obtiens aussi des erreurs dans mes vues.

Votre navigateur doit pouvoir accéder aux adresses suivantes pour exécuter des requêtes Log Analytics après la mise à niveau.  Si votre navigateur accède au portail Azure par le biais d’un pare-feu, vous devez activer l’accès à ces adresses.

| Uri | IP | Ports |
|:---|:---|:---|
| portal.loganalytics.io | Dynamique | 80,443 |
| api.loganalytics.io    | Dynamique | 80,443 |
| docs.loganalytics.io   | Dynamique | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Question : Existe-t-il des changements suite à l’intégration de Power BI ?
Oui.  Une fois votre espace de travail mis à niveau, puis le processus d’exportation des données de Log Analytics à Power BI ne fonctionne plus.  Toutes les planifications existantes que vous avez créées avant la mise à niveau sont alors désactivées.  

Après la mise à niveau, Azure Log Analytics utilise la même plateforme qu’Application Insights et vous devez utiliser le même processus vous permettant d’exporter des requêtes de Log Analytics vers Power BI comme [processus d’exportation des requêtes d’Application Insights vers Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  L’exportation vers Power BI appelle désormais directement le point de terminaison d’API. Vous pouvez ainsi obtenir jusqu’à 500 000 lignes ou 64 000 000 octets de données, exporter des requêtes longues et personnaliser le délai d’expiration de la requête (le délai d’expiration par défaut est de 3 minutes et le délai d’expiration maximal est de 10 minutes).

## <a name="powershell-cmdlets"></a>Applets de commande PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Question : L’applet de commande PowerShell de recherche dans les journaux est-elle mise à jour après la mise à niveau ?
[Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) sera déprécié au terme de la mise à niveau de tous les espaces de travail.  Utilisez l’[applet de commande Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) pour effectuer des recherches dans les journaux dans les espaces de travail mis à niveau.




## <a name="resource-manager-templates"></a>Modèles Resource Manager

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Question : Puis-je créer un espace de travail mis à niveau avec un modèle Resource Manager ?
Oui.  Vous devez utiliser la version d’API 2017-03-15-preview et ajouter une section **features** dans votre modèle, comme dans l’exemple suivant.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>solutions

### <a name="question-will-my-solutions-continue-to-work"></a>Question : Mes solutions continueront-elles de fonctionner ?
Toutes les solutions continueront de fonctionner dans un espace de travail mis à niveau. Cependant, en les convertissant dans le nouveau langage de requête, vous bénéficierez de meilleures performances.  Certaines solutions existantes décrites dans cette section font face à des problèmes connus.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Problème connu : perspectives dans le connecteur Application Insights
Les perspectives dans la [solution Application Insights Connector](log-analytics-app-insights-connector.md) ne sont plus prises en charge dans la solution du connecteur Application Insights.  Vous pouvez utiliser le concepteur de vues pour créer des vues personnalisées avec des données d’Application Insights.

### <a name="known-issue-backup-solution"></a>Problème connu : solution Sauvegarde
La solution de sauvegarde peut ne pas collecter de données si elle a été installée avant la mise à niveau d’un espace de travail. Désinstallez la solution, puis installez sa version la plus récente.  Comme la nouvelle version de la solution ne prend pas en charge les coffres de sauvegarde classiques, vous devez également procéder à une mise à niveau vers les coffres Recovery Services pour continuer à utiliser la solution.

## <a name="upgrade-process"></a>Mise à niveau

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Question : Je dispose de plusieurs espaces de travail. Puis-je mettre à niveau tous mes espaces de travail simultanément ?  
Non.  La mise à niveau s’applique à un seul espace de travail à la fois. Actuellement, il n’existe aucun moyen de la mise à niveau de nombreux espaces de travail à la fois. Notez que les autres utilisateurs de l’espace de travail mis à niveau seront également affectés.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Question : Les données de journal collectées dans mon espace de travail seront-elles modifiées en cas de mise à niveau ?  
Non. Les données de journal disponibles pour vos recherches d’espace de travail ne sont pas affectées par la mise à niveau. Les recherches enregistrées, les alertes et les vues seront converties vers le nouveau langage de recherche automatiquement.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Question : Que se passe-t-il si je ne mets pas à niveau mon espace de travail ?  
La recherche de journal héritée sera déconseillée dans les prochains mois. Les espaces de travail qui ne sont pas mis à niveau à ce moment-là seront automatiquement mis à niveau.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Question : Puis-je revenir à la dernière version après la mise à niveau ?
Avant la disponibilité générale, vous pouviez revenir à la dernière version de votre espace de travail après la mise à niveau.  Le nouveau langage étant désormais en phase de disponibilité générale, cette fonctionnalité a été supprimée et la plateforme héritée va progressivement être mise hors service.


## <a name="views"></a>Views

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Question : Comment créer une vue avec le Concepteur de vues ?
Avant la mise à niveau, il était possible de créer une vue avec le Concepteur de vues à partir d’une vignette du tableau de bord principal dans le portail OMS.  Quand l’espace de travail est mis à niveau, cette vignette est supprimée.  Vous pouvez créer une vue à l’aide du Concepteur de vues dans le portail OMS en cliquant sur le bouton + vert dans le menu de gauche.  Vous continuez à créer une nouvelle vue avec le portail Azure en cliquant sur la vignette Concepteur de vues.


## <a name="next-steps"></a>Étapes suivantes

- Plus d’informations sur la [mise à niveau de votre espace de travail vers le nouveau langage de requête de Log Analytics](log-analytics-log-search-upgrade.md).
