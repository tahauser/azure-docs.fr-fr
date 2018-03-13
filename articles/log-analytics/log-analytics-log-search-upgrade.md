---
title: "Mise à niveau d’Azure Log Analytics pour la nouvelle fonctionnalité de recherche dans les journaux | Microsoft Docs"
description: "Le nouveau langage de requête Log Analytics sera bientôt disponible. En attendant, vous pouvez participer à la préversion publique.  Cet article décrit les avantages du nouveau langage et explique comment convertir votre espace de travail."
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
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 3bb54f7897876d656da6f1a4b349c9db202a142d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Mise à niveau Azure Log Analytics avec la nouvelle recherche dans les journaux

Le nouveau langage de requête Log Analytics est disponible, vous devez mettre à niveau votre espace de travail pour pouvoir l’utiliser.  Vous pouvez mettre à niveau votre espace de travail vous-même ou attendre qu’il soit automatiquement mis à niveau pendant la période de lancement (entre la fin octobre et la fin de l’année).  Cet article décrit les avantages du nouveau langage et explique comment convertir votre espace de travail.  

## <a name="why-the-new-language"></a>Pourquoi un nouveau langage ?
Nous sommes conscients que toute transition implique des efforts. Sachez, toutefois, que cette modification était nécessaire.  Cette modification présente de nombreux avantages pour les utilisateurs Log Analytics.

- **Simple, mais puissant.** Le nouveau langage est plus facile à comprendre et similaire au langage SQL, avec des constructions plus proches du langage naturel que l’ancien langage.
- **Des fonctionnalités de redirection complètes.**  Le nouveau langage comprend des fonctionnalités de redirection plus complètes que celles de l’ancien langage.  Quasiment tous les résultats peuvent être redirigés vers une autre commande pour créer des requêtes plus complexes qu’avec l’ancien langage.
- **Extraction des champs au moment de la recherche.**  Le nouveau langage prend en charge des champs calculés au moment du runtime plus avancés que ceux de l’ancien langage.  Vous pouvez utiliser des calculs complexes pour les champs étendus, puis utiliser les champs calculés dans des commandes supplémentaires telles que des jointures et des agrégations.
- **Jointures avancées.**  Le nouveau langage fournit des jointures plus avancées que celles de l’ancien langage. Il permet notamment de joindre les tables de plusieurs champs, d’utiliser des jointures internes et externes, et de joindre des champs étendus.
- **Fonctions de date et d’heure.**  Le nouveau langage comprend davantage de fonctions de date/heure que l’ancien langage.
- **Analyses intelligentes.**  Le nouveau langage comprend des algorithmes avancés permettant d’évaluer les modèles de jeux de données et de comparer des jeux de données.
- **Portail Analytique avancée.**  Le portail Analytique avancée comprend des fonctionnalités d’analyse qui ne sont pas disponibles dans le portail Log Analytics, notamment l’édition multiligne de requêtes, des visualisations supplémentaires et des diagnostics avancés.
- **Cohérence avec les autres applications.**  Le nouveau langage et le portail Analytique avancée sont déjà utilisés pour les analyses dans Application Insights.  L’implémentation du nouveau langage pour Log Analytics permet une cohérence entre les services Azure.
- **Meilleure intégration avec Power BI.** Les requêtes dans le nouveau langage peuvent être exportées vers Power BI Desktop : vous pouvez donc utiliser ses riches fonctionnalités de transformation des données.
- **Et bien plus encore...** Pour obtenir des informations détaillées et des didacticiels sur le nouveau langage, consultez le site [Langage de requête Azure Log Analytics](https://docs.loganalytics.io).


## <a name="when-can-i-upgrade"></a>À quel moment puis-je effectuer la mise à niveau ?
La mise à niveau va être déployée dans toutes les régions Azure, et peut être disponible dans certaines régions avant d’autres.  Vous savez que votre espace de travail est prêt à être mis à niveau quand vous voyez une bannière en haut de votre espace de travail vous invitant à effectuer la mise à niveau.

![Mise à niveau 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Si votre espace de travail est automatiquement mis à niveau, une bannière s’affiche pour confirmer la mise à niveau et signaler les éventuels problèmes rencontrés.

 ![Mise à jour automatique](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Que se passe-t-il après la mise à niveau ?
Les changements suivants sont apportés à votre espace de travail quand il est converti :

- Toutes les recherches enregistrées, les règles d’alerte et les vues que vous avez créées avec le Concepteur de vues sont automatiquement converties dans le nouveau langage.  Les recherches incluses dans les solutions de recherche ne sont pas automatiquement converties. Elles le sont, cependant, lorsque vous les ouvrez.  
- La fonctionnalité [Mon tableau de bord](log-analytics-dashboards.md) est dépréciée. Elle va être remplacée par le [Concepteur de vues](log-analytics-view-designer.md) et les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md).  Les vignettes que vous avez ajoutées à Mon tableau de bord sont toujours disponibles, mais elles sont en lecture seule.
- La fonctionnalité [Intégration Power BI](log-analytics-powerbi.md) est remplacée par un nouveau processus.  Toutes les planifications de Power BI existantes que vous avez créées sont désactivées, et vous devez les remplacer par le nouveau processus.
- Les réponses des [actions d’alerte](log-analytics-alerts-actions.md) utilisant des webhooks et des runbooks ont un nouveau format qui va peut-être vous obliger à mettre à jour vos règles d’alerte.
- Consultez les [questions fréquentes (FAQ) sur la recherche dans les journaux](log-analytics-log-search-faq.md) pour voir les questions couramment posées sur la mise à niveau.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Comment savoir si des problèmes se sont produits pendant la mise à niveau ?
Une fois la mise à niveau terminée, une section **Résumé de la mise à niveau** apparaît dans les paramètres de l’espace de travail.  Consultez cette section pour obtenir des informations sur votre mise à niveau et afficher les éventuels problèmes rencontrés.

 ![Résumé de la mise à niveau](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Comment effectuer manuellement la mise à niveau ?
Quand vous voyez la bannière en haut du portail, vous pouvez mettre à niveau votre espace de travail.  

1.  Pour lancer le processus de mise à niveau, cliquez sur la bannière intitulée **En savoir plus et mise à niveau**.

    ![Mise à niveau 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Pour plus d’informations sur la mise à niveau, consultez la page d’informations sur la mise à niveau.

    ![Mise à niveau 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Pour démarrer la mise à niveau, cliquez sur **Mettre à niveau maintenant**.

    ![Mise à niveau 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>L’état est affiché dans la zone de notification située dans le coin supérieur droit.
    
    ![Mise à niveau 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  Et voilà !  Passez en revue la page Recherche dans les journaux pour voir à quoi ressemble votre nouvel espace de travail.

    ![Mise à jour 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Si vous rencontrez un problème qui provoque l’échec de la mise à niveau, vous pouvez accéder au [Forum de discussion](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) et envoyer votre question. Vous pouvez également [créer une demande de support](../azure-supportability/how-to-create-azure-support-request.md) dans le portail Azure.

## <a name="how-do-i-learn-the-new-language"></a>Comment en savoir plus sur le nouveau langage ?
Dans la mesure où il est utilisé par plusieurs services, nous avons créé un [site externe pour héberger la documentation](https://docs.loganalytics.io/) relative au nouveau langage.  Cette documentation inclut des didacticiels, des exemples, ainsi qu’une référence complète sur le langage, afin de vous aider à améliorer vos connaissances. Pour suivre un didacticiel sur le nouveau langage, consultez le [Guide pratique sur les requêtes](https://go.microsoft.com/fwlink/?linkid=856078). Pour accéder à la référence du langage, consultez [Langage de requête Log Analytics](https://go.microsoft.com/fwlink/?linkid=856079).  

Si vous souhaitez essayer le nouveau langage dans un environnement de démonstration comprenant un ensemble d’exemples de données, visitez l’[environnement de test](https://portal.loganalytics.io/demo#/discover/home).

Si vous êtes déjà familiarisé avec l’ancien langage de requête Log Analytics, vous pouvez utiliser le convertisseur de langage qui est ajouté à votre espace de travail dans le cadre de la mise à niveau.  Il vous suffit de taper votre requête dans l’ancien langage, puis de cliquer sur **Convertir** pour afficher la version traduite en nouveau langage.  Vous pouvez ensuite cliquer sur le bouton de recherche pour exécuter la recherche, ou copier-coller la requête convertie pour l’utiliser ailleurs (par exemple, dans une règle d’alerte).  Vous pouvez également examiner notre [aide-mémoire](log-analytics-log-search-transition.md) qui compare directement les requêtes courantes du langage hérité.

![Convertisseur de langage](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Étapes suivantes
- Consultez le [didacticiel sur le nouveau langage](https://go.microsoft.com/fwlink/?linkid=856078).
- Suivez le [didacticiel sur l’utilisation du portail Recherche dans les journaux](log-analytics-log-search-log-search-portal.md) avec le nouveau langage de requête.
- Lisez la présentation du nouveau [portail Advanced Analytics](https://go.microsoft.com/fwlink/?linkid=856587).
