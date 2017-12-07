---
title: "Qu’est-ce qui a changé dans Azure Log Analytics ? | Microsoft Docs"
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Qu’est-ce qui a changé dans Azure Log Analytics ?
En plus du langage de requête proprement dit, il y a plusieurs améliorations et modifications dont vous devez être informé quand votre espace de travail Log Analytics [passe au nouveau langage de requête](log-analytics-log-search-new.md).  Cet article décrit brièvement les différences entre un espace de travail hérité et un espace de travail mis à niveau, puis fournit des liens vers du contenu détaillé pour chacun. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Pour obtenir une description des problèmes connus avec la mise à niveau et des réponses aux questions courantes, consultez [Questions fréquentes (FAQ) sur la nouvelle recherche dans les journaux Log Analytics et problèmes connus](log-analytics-log-search-faq.md).  

## <a name="query-language"></a>Langage de requête
La principale modification apportée dans la mise à niveau de Log Analytics concerne le nouveau langage de requête, qui présente des améliorations significatives par rapport au langage précédent.  

Pour obtenir une comparaison directe des opérations courantes entre le langage hérité et le nouveau langage, consultez [Transition vers le nouveau langage de requête d’Azure Log Analytics](log-analytics-log-search-transition.md).  La documentation complète sur le nouveau langage est disponible à l’adresse [Langage de requête Azure Log Analytics](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Groupes d’ordinateurs
Le mode de création d’un groupe d’ordinateurs n’a pas changé, mais vous devez maintenant fournir un alias unique pour chaque groupe.  Les groupes d’ordinateurs basés sur des recherches dans les journaux doivent utiliser la syntaxe du nouveau langage.

Il existe une nouvelle syntaxe pour l’utilisation de groupes d’ordinateurs dans une recherche dans les journaux.  Au lieu d’utiliser la fonction $ComputerGroups, les groupes d’ordinateurs sont désormais tous implémentés en tant que fonction avec un alias unique.  Vous utilisez l’alias dans la recherche dans les journaux comme toute autre fonction.  

Vous trouverez des détails dans [Groupes d’ordinateurs dans les recherches dans les journaux Log Analytics](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portails de recherche dans les journaux
En plus du portail Recherche dans les journaux, qui vous permet de créer et d’exécuter des recherches dans les journaux, vous pouvez désormais utiliser le portail Analytique avancée, qui fournit des fonctionnalités d’édition considérablement améliorées.

Vous trouverez une brève description des deux portails dans [Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics](log-analytics-log-search-portals.md).  Vous pouvez effectuer un didacticiel sur le nouveau portail dans [Bien démarrer avec le portail Analytique](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Alertes
Le fonctionnement des alertes est inchangé dans les espaces de travail mis à niveau, mais la requête qu’ils s’exécutent doit être écrite dans le nouveau langage.  Toutes les règles d’alerte qui existaient avant la mise à niveau seront automatiquement converties dans le nouveau langage.  Pour plus d’informations, consultez [Présentation des alertes dans Log Analytics](log-analytics-alerts.md).

Le format de la charge utile pour les runbooks et les webhooks envoyés à partir d’alertes a changé.  Pour plus d’informations sur le nouveau format de charge utile, consultez [Ajouter des actions à des règles d’alerte dans Log Analytics](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Tableaux de bord
Les [tableaux de bord](log-analytics-dashboards.md) sont en cours de dépréciation.  Vous pouvez continuer d’utiliser les vignettes que vous avez ajoutées au tableau de bord avant la mise à niveau de votre espace de travail, mais vous ne pouvez ni les modifier ni en ajouter de nouvelles.  Utilisez le Concepteur de vues pour créer des vues personnalisées offrant un ensemble de fonctionnalités plus riches que les tableaux de bord.

Vous trouverez des détails sur le Concepteur de vues dans [Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
Le processus d’exportation de données Log Analytics vers Power BI a été changé pour les espaces de travail mis à niveau, et toutes les planifications existantes que vous avez créées avant la mise à niveau seront désactivées.  

Pour plus d’informations à ce sujet, consultez [Exportation de données Log Analytics vers Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
L’applet de commande Get-AzureRmOperationalInsightsSearchResults pour l’exécution de recherches dans les journaux à partir de PowerShell ne fonctionne pas avec un espace de travail mis à niveau.  Utilisez Invoke-LogAnalyticsQuery pour bénéficier de cette fonctionnalité avec un espace de travail mis à niveau.

Pour plus d’informations à ce sujet, consultez [Azure Log Analytics REST API - PowerShell Cmdlets](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>API Recherche de journal
L’API REST de recherche dans les journaux a changé, et les solutions qui utilisent l’ancienne version doivent être mises à jour pour utiliser la nouvelle version de l’API.   

Vous trouverez des détails sur la nouvelle version de l’API dans [API REST Azure Log Analytics](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une description des problèmes connus avec la mise à niveau et des réponses aux questions courantes, consultez [Questions fréquentes (FAQ) sur la nouvelle recherche dans les journaux Log Analytics et problèmes connus](log-analytics-log-search-faq.md).