---
title: "Recommandations relatives aux performances - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database fournit des recommandations pour vos bases de données SQL afin d’améliorer le niveau de performance actuel des requêtes."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 09/20/2017
ms.author: sstein
ms.openlocfilehash: 84706837aeb416d13dab617f51a33d62a934c016
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="performance-recommendations"></a>Recommandations en matière de performances

Azure SQL Database apprend et s’adapte à votre application afin de vous fournir des recommandations personnalisées qui vous permettront d’optimiser les performances de vos bases de données SQL. Vos performances sont évaluées en permanence grâce à l’analyse de votre historique d’utilisation de SQL Database. Les recommandations fournies se fondent sur un modèle unique de charge de travail d’une base de données, et aident à améliorer les performances de la base de données.

> [!TIP]
> Le [Paramétrage automatique](sql-database-automatic-tuning.md) est la méthode recommandée pour le réglage des performances. [Intelligent Insights](sql-database-intelligent-insights.md) est la méthode recommandée pour l’analyse des performances. 
>

## <a name="create-index-recommendations"></a>Recommandations relatives à la création d’un index
Azure SQL Database surveille de manière continue les requêtes exécutées et identifie les index capables d’améliorer le niveau de performance. Dès que la base de données estime qu’un index manque, une recommandation **Créer un index** est créée. Azure SQL Database renforce le climat de confiance en estimant le gain de performances que l’index pourrait apporter au fil du temps. Selon le gain de performances estimé, les recommandations sont réparties en trois catégories : Élevée, Moyenne ou Faible. 

Les index créés à l’aide de recommandations portent toujours l’indicateur auto_created. Vous pouvez voir quels index possèdent l’indicateur auto_created en examinant la vue sys.indexes. Les index créés automatiquement ne bloquent pas les commandes ALTER/RENAME. Si vous essayez de supprimer la colonne qui possède un index créé automatiquement, la commande est transmise et l’index supprimé en même temps que la commande. Les index normaux peuvent bloquer les commandes ALTER/RENAME sur les colonnes indexées.

Une fois que la recommandation de création de l’index est appliquée, Azure SQL Database compare le niveau de performance des requêtes avec le référentiel de performances. Si le niveau de performance est amélioré grâce au nouvel index, la recommandation sera désignée comme réussie et un rapport d’impact sera généré. Si l’index n’apporte aucun avantage, il sera automatiquement restauré. De cette manière, Azure SQL Database garantit que l’utilisation des recommandations améliore uniquement le niveau de performance.

N’importe quelle recommandation **Créer un index** possède une stratégie d’abstention. Cette stratégie n’autorise pas l’application d’une recommandation si la base de données ou l’utilisation du DTU de pool atteint 80 % dans les dernières 20 minutes, ou si le stockage atteint un taux d’utilisation de plus de 90 %. Dans ce cas, la recommandation est reportée.

## <a name="drop-index-recommendations"></a>Recommandations relatives à la suppression d’index
En plus de détecter un index manquant, Azure SQL Database analyse continuellement le niveau de performance des index existants. Si l’index n’est pas utilisé, Azure SQL Database recommande sa suppression. Il est recommandé de supprimer un index dans deux cas :
* L’index est un doublon d’un autre index (même colonne, schéma de partition et filtres indexés et inclus)
* L’index n’est pas utilisé pendant une période prolongée (93 jours)

Les recommandations de suppression d’index passent également par la vérification, suite à l’implémentation. Si le niveau de performance est amélioré, le rapport d’impact est disponible. Si une dégradation du niveau de performance est détectée, la recommandation est restaurée.


## <a name="parameterize-queries-recommendations"></a>Recommandations de paramétrage de requêtes
Les recommandations liées au **paramétrage des requêtes** s’affichent lorsque vous disposez d’une ou plusieurs requêtes qui sont constamment recompilées, mais ont en fin de compte le même plan d’exécution de requête. Cela ouvre la possibilité d’appliquer un paramétrage forcé, ce qui permet de mettre en cache les plans de requête et de les réutiliser pour améliorer les performances et réduire l’utilisation des ressources. 

Chaque requête adressée initialement à SQL Server doit être compilée pour générer un plan d’exécution. Chaque plan généré est ajouté au cache du plan et les exécutions ultérieures de la même requête peuvent réutiliser ce plan à partir du cache, évitant ainsi toute compilation supplémentaire. 

Les applications qui envoient des requêtes incluant des valeurs non paramétrées peuvent entraîner une baisse des performances, car le plan d’exécution est recompilé pour chaque requête de ce type avec des valeurs de paramètres différentes. Dans de nombreux cas, les mêmes requêtes avec différentes valeurs de paramètres génèrent les mêmes plans d’exécution, mais ces plans sont toujours ajoutés séparément au cache du plan. La recompilation des plans d’exécution utilise des ressources de base de données, augmente la durée la requête et dépasse la capacité du cache du plan, entraînant la suppression des plans de ce cache. Ce comportement de SQL Server peut être modifié en définissant l’option de paramétrage forcé au niveau de la base de données. 

Pour vous aider à évaluer l’impact de cette recommandation, nous vous fournissons une comparaison entre l’utilisation réelle et l’utilisation projetée du processeur (comme si la recommandation avait été appliquée). Outre le fait que le processeur est moins sollicité, la durée de la requête dans la compilation diminue. La surcharge sur le cache du plan sera également beaucoup moins importante, permettant ainsi à la majorité des plans de rester dans le cache et d’être réutilisés. Vous pouvez appliquer cette recommandation rapidement et facilement en cliquant sur la commande **Appliquer**. 

Une fois appliquée, cette recommandation active en quelques minutes le paramétrage forcé sur votre base de données et démarre le processus de surveillance qui dure environ 24 heures. Après cette période, vous pourrez consulter le rapport de validation qui indique l’utilisation du processeur de votre base de données 24 heures avant et après l’application de la recommandation. SQL Database Advisor intègre un mécanisme de sécurité qui annule automatiquement la recommandation appliquée si une baisse des performances est détectée.

## <a name="fix-schema-issues-recommendations-preview"></a>Recommandations liées à la résolution des problèmes de schéma (préversion)

> [!IMPORTANT]
> Microsoft est sur le point de déconseiller les recommandations liées à la « résolution des problèmes de schéma ». En effet, il est préférable d’utiliser [Intelligent Insights](sql-database-intelligent-insights.md) pour le monitoring automatique des problèmes de performances de base de données, lesquels incluent les problèmes de schéma auparavant couverts par les recommandations liées à la « résolution des problèmes de schéma ».
> 

Les recommandations liées à la **résolution des problèmes de schéma** s’affichent lorsque le service SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent dans Azure SQL Database. Cette recommandation s’affiche généralement lorsque votre base de données rencontre plusieurs erreurs liées au schéma (nom de colonne non valide, nom d’objet non valide, etc.) dans la même heure.

Les « problèmes de schéma » représentent une catégorie d’erreurs de syntaxe dans SQL Server, qui se produisent lorsque la définition de la requête SQL et la définition du schéma de la base de données ne sont pas alignées. Par exemple, une des colonnes attendues par la requête est manquante dans la table cible, ou inversement. 

Les recommandations « Résoudre les problèmes de schéma » s’affichent lorsque le service Azure SQL Database détecte une anomalie dans le nombre d’erreurs SQL liées au schéma qui se produisent dans Azure SQL Database. Le tableau suivant répertorie les erreurs liées à des problèmes de schéma :

| Code d'erreur SQL | Message |
| --- | --- |
| 201 |La procédure ou fonction '*’ attend le paramètre ’*', qui n’a pas été fourni. |
| 207 |Nom de colonne non valide '*'. |
| 208 |Nom d'objet non valide ’*’. |
| 213 |Le nom de la colonne ou le nombre de valeurs fournies ne correspond pas à la définition de la table. |
| 2812 |Impossible de trouver la procédure stockée '*'. |
| 8144 |La procédure ou la fonction * a trop d’arguments spécifiés. |

## <a name="next-steps"></a>Étapes suivantes
Surveillez vos recommandations et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. SQL Database Advisor continue à surveiller et à fournir des recommandations pouvant potentiellement améliorer les performances de votre base de données. 

* Consultez [Réglage automatique de Microsoft Azure SQL Database](sql-database-automatic-tuning.md) pour le réglage automatique des index de base de données et les plans d’exécution de requêtes.
* Consultez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour la surveillance automatique des performances de base de données avec des diagnostics automatiques et l’analyse des causes racine des problèmes de performance.
* Consultez la page [Performance recommendations in the Azure portal](sql-database-advisor-portal.md) (Recommandations en matière de performances dans le portail Azure) afin d’obtenir des instructions sur l’utilisation des recommandations relatives aux performances dans le portail Azure.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md) (Analyse des performances des requêtes).


