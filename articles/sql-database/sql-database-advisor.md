---
title: "Recommandations relatives aux performances - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database fournit des recommandations pour vos bases de données SQL afin d’améliorer le niveau de performance actuel des requêtes."
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: 
ms.openlocfilehash: b3cd5f2138f4d16a1a1590b025d020410ebcce3c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Recommandations relatives aux performances pour SQL Database

Le service Azure SQL Database apprend avec votre application et s’adapte à cette dernière. Il fournit des recommandations personnalisées qui vous permettent d’optimiser le niveau de performance de vos bases de données SQL. SQL Database évalue et analyse continuellement l’historique de l’utilisation de vos bases de données SQL. Les recommandations fournies reposent sur des modèles de charge de travail propres à une base de données, et contribuent à améliorer le niveau de performance.

> [!TIP]
> Le [paramétrage automatique](sql-database-automatic-tuning.md) constitue la méthode recommandée pour le réglage des performances. [Intelligent Insights](sql-database-intelligent-insights.md) est la méthode recommandée en matière d’analyse des performances. 
>

## <a name="create-index-recommendations"></a>Recommandations relatives à la création d’un index
SQL Database surveille continuellement les requêtes en cours d’exécution et identifie les index susceptibles d’améliorer le niveau de performance. Lorsque le service détermine avec certitude qu’un index spécifique est manquant, une recommandation **Créer un index** est créée.

 Azure SQL Database établit un climat de confiance en estimant le gain de performances que l’index pourrait offrir au fil du temps. Selon le gain estimé, les recommandations sont réparties en trois catégories : gain de performances élevé, moyen ou faible. 

Les index créés à l’aide de recommandations sont toujours indiqués comme ayant été créés automatiquement. Vous pouvez visualiser les index créés automatiquement en consultant la vue sys.indexes. Les index créés automatiquement ne bloquent pas les commandes ALTER/RENAME. 

Si vous tentez de supprimer la colonne qui comporte un index créé automatiquement, la commande aboutit. L’index créé automatiquement est également supprimé dans le cadre de l’exécution de la commande. Les index standard bloquent la commande ALTER/RENAME sur les colonnes indexées.

Une fois que la recommandation de création d’index a été appliquée, Azure SQL Database compare le niveau de performance des requêtes avec le niveau de performance de référence. Si le nouvel index a amélioré les performances, la recommandation est indiquée comme étant fructueuse, et le rapport d’impact est disponible. Si l’index n’a pas entraîné d’amélioration des performances, il est automatiquement annulé. SQL Database applique ce processus pour garantir le fait que les recommandations optimisent les performances de base de données.

Toutes les recommandations **Créer un index** comportent une stratégie d’abstention qui empêche la mise en œuvre de la recommandation si l’utilisation des ressources d’une base de données ou d’un pool est élevée. La stratégie d’abstention prend en compte l’UC, les E/S de données, les E/S de journal et le stockage disponible. 

Si l’UC, les E/S de données ou les E/S de journal dépassent 80 % au cours des 30 minutes qui précèdent, la recommandation de création d’un index est reportée. Si l’espace de stockage disponible est inférieur à 10 % une fois que l’index a été créé, la recommandation passe en état d’erreur. Si après deux jours, le paramétrage automatique estime toujours que cet index peut être avantageux, le processus redémarre. 

Ce processus est répété jusqu’à ce que le stockage disponible soit suffisant pour créer un index ou jusqu’à ce que l’index ne soit plus considéré comme bénéfique.

## <a name="drop-index-recommendations"></a>Recommandations relatives à la suppression d’index
Outre la détection des index manquants, SQL Database procède à l’analyse continue du niveau de performance des index existants. Si un index n’est pas utilisé, Azure SQL Database recommande sa suppression. Il est recommandé de supprimer un index dans deux cas :
* L’index est un doublon d’un autre index (mêmes colonne, schéma de partition et filtres indexés et inclus).
* L’index n’a pas été utilisé pendant une période prolongée (93 jours).

Les recommandations de suppression d’index passent également par la vérification, suite à l’implémentation. Si le niveau de performance s’améliore, le rapport d’impact est disponible. Si le niveau de performance se détériore, la recommandation est annulée.


## <a name="parameterize-queries-recommendations"></a>Recommandations de paramétrage de requêtes
Les recommandations liées au *paramétrage des requêtes* s’affichent lorsque vous disposez d’une ou plusieurs requêtes qui sont constamment recompilées, mais ont en fin de compte le même plan d’exécution de requête. Cette condition offre une opportunité de mise en œuvre d’un paramétrage forcé. Le paramétrage forcé autorise la mise en cache et la réutilisation ultérieure des plans de requête, ce qui améliore le niveau de performance et réduit l’utilisation des ressources. 

Chaque requête initialement adressée à SQL Server doit être compilée pour générer un plan d’exécution. Chaque plan généré est ajouté au cache du plan. Les exécutions ultérieures de la même requête peuvent réutiliser ce plan à partir du cache, évitant ainsi toute compilation supplémentaire. 

Les requêtes incluant des valeurs non paramétrées peuvent entraîner une détérioration des performances, car le plan d’exécution est recompilé chaque fois que les valeurs non paramétrées diffèrent. Dans de nombreux cas, des requêtes identiques comportant différentes valeurs de paramètre génèrent les mêmes plans d’exécution. Toutefois, ces plans sont toujours ajoutés séparément au cache du plan. 

Le processus de recompilation des plans d’exécution utilise des ressources de base de données, augmente la durée des requêtes et dépasse la capacité du cache du plan. Ces événements entraînent à leur tour la suppression des plans de ce cache. Il est possible de modifier ce comportement de SQL Server en définissant l’option de paramétrage forcé au niveau de la base de données. 

Pour vous aider à évaluer l’impact de cette recommandation, nous vous fournissons une comparaison entre l’utilisation réelle de l’UC et son utilisation projetée (comme si la recommandation avait été appliquée). Cette recommandation peut vous aider à économiser les ressources d’UC. Elle peut également contribuer à réduire la durée des requêtes et les surcharges de cache du plan, permettant ainsi la conservation dans le cache et la réutilisation d’un plus grand nombre de plans. Vous pouvez appliquer cette recommandation rapidement en sélectionnant la commande **Appliquer**. 

Une fois cette recommandation appliquée, le paramétrage forcé est activé en l’espace de quelques minutes dans votre base de données. Il démarre le processus d’analyse, qui dure environ 24 heures. Au terme de cette période, vous pouvez consulter le rapport de validation. Ce rapport présente l’utilisation de l’UC par votre base de données 24 heures avant et après l’application de la recommandation. SQL Database Advisor intègre un mécanisme de sécurité qui annule automatiquement la recommandation appliquée si une baisse des performances est détectée.

## <a name="fix-schema-issues-recommendations-preview"></a>Recommandations liées à la résolution des problèmes de schéma (préversion)

> [!IMPORTANT]
> Microsoft déconseille actuellement la recommandation « Résoudre les problèmes de schéma ». Nous vous recommandons d’utiliser [Intelligent Insights](sql-database-intelligent-insights.md) pour analyser vos problèmes de performances de base de données, y compris les problèmes de schéma précédemment couverts par la recommandation de résolution de ce type de problèmes.
> 

La recommandation **Résoudre les problèmes de schéma** s’affiche lorsque le service SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent sur votre base de données SQL. Cette recommandation apparaît généralement lorsque votre base de données rencontre plusieurs erreurs liées au schéma (nom de colonne non valide, nom d’objet incorrect, etc.) en l’espace d’une heure.

Les « problèmes de schéma » désignent une classe d’erreurs de syntaxe dans SQL Server. Ils surviennent lorsque la définition de la requête SQL et la définition du schéma de base de données ne sont pas alignées. Par exemple, il est possible que l’une des colonnes attendues par la requête soit absente de la table cible, ou inversement. 

La recommandation « Résoudre les problèmes de schéma » s’affiche lorsque le service Azure SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent dans votre base de données SQL. Le tableau suivant répertorie les erreurs liées à des problèmes de schéma :

| Code d’erreur SQL | Message |
| --- | --- |
| 201 |La procédure ou fonction '*’ attend le paramètre ’*', qui n’a pas été fourni. |
| 207 |Nom de colonne non valide '*'. |
| 208 |Nom d'objet non valide ’*’. |
| 213 |Le nom de la colonne ou le nombre de valeurs fournies ne correspond pas à la définition de la table. |
| 2812 |Impossible de trouver la procédure stockée '*'. |
| 8144 |La procédure ou la fonction * a trop d’arguments spécifiés. |

## <a name="next-steps"></a>Étapes suivantes
Surveillez vos recommandations et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. SQL Database Advisor analyse en continu le niveau de performance de votre base de données et fournit des recommandations susceptibles d’améliorer ce niveau. 

* Pour plus d’informations sur le paramétrage automatique des index de base de données et des plans d’exécution de requêtes, consultez l’article [Réglage automatique dans Azure SQL Database](sql-database-automatic-tuning.md).
* Pour plus d’informations sur l’analyse automatique des performances de base de données à l’aide des diagnostics automatisés et de l’analyse de la cause racine des problèmes de performances, consultez l’article [Intelligent Insights](sql-database-intelligent-insights.md).
*  Pour plus d’informations sur la procédure d’utilisation des recommandations relatives aux performances dans le portail Azure, consultez l’article [Rechercher et appliquer les recommandations en matière de performances](sql-database-advisor-portal.md).
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md) (Analyse des performances des requêtes).


