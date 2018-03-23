---
title: Azure SQL Database - Réglage automatique | Microsoft Docs
description: Azure SQL Database analyse la requête SQL et s’adapte automatiquement aux charges de travail utilisateur.
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/09/2017
ms.author: jovanpop
ms.openlocfilehash: 917dd48d57b70b328f40f518d3fb472badfda08a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Réglage automatique dans Azure SQL Database

Le réglage automatique dans Azure SQL Database fournit une optimisation des performances et une stabilisation des charges de travail grâce à un réglage continu des performances qui s’appuie sur l’intelligence artificielle.

Le réglage automatique est un service entièrement géré qui utilise l’intelligence intégrée pour surveiller en continu les requêtes exécutées sur une base de données et qui améliore automatiquement leurs performances. Pour cela, il adapte de manière dynamique la base de données à l’évolution des charges de travail et applique des recommandations de réglage. Le réglage automatique apprend horizontalement de toutes les bases de données sur Azure grâce à l’intelligence artificielle et améliore ses actions de réglage de manière dynamique. Plus une base de données SQL Azure s’exécute avec le réglage automatique activée, meilleures sont ses performances.

Le réglage automatique dans SQL Azure Database est peut-être l’une des fonctionnalités les plus importantes que vous pouvez activer pour fournir des charges de travail stables avec des performances maximales.

## <a name="what-can-automatic-tuning-do-for-you"></a>En quoi peut vous aider le réglage automatique ?

- Réglage automatisé des performances des bases de données SQL Azure
- Vérification automatisée des gains de performances
- Restauration automatisée et correction automatique
- Journal d’historique de réglage
- Scripts T-SQL d’action de réglage pour les déploiements manuels
- Surveillance proactive des performances des charges de travail
- Capacité de montée en charge sur des centaines de milliers de bases de données
- Impact positif sur les ressources de DevOps et le coût total de possession

## <a name="safe-reliable-and-proven"></a>Sécurité et fiabilité

Les opérations de réglage appliquées aux bases de données SQL Azure sont entièrement sécurisées pour les performances de vos charges de travail les plus intenses. Le système a été conçu avec soin afin de ne pas interférer avec les charges de travail utilisateur. Les recommandations de réglage automatique sont appliquées uniquement pendant les périodes d’utilisation normale. Le système peut également désactiver temporairement les opérations de réglage automatique afin de protéger les performances des charges de travail. Dans ce cas, le message « Désactivé par le système » s’affiche dans le portail Azure. Le réglage automatique concerne les charges de travail avec la priorité de ressources la plus élevée.

Les mécanismes de réglage automatique sont matures et ont été éprouvés sur des centaines de milliers de bases de données exécutées sur Azure. Les opérations de réglage automatique appliquées sont vérifiées automatiquement afin de s’assurer qu’il y a une amélioration positive des performances des charges de travail. Les recommandations de performances ayant un impact négatif sont détectées de manière dynamique et inversées rapidement. Avec le journal d’historique de réglage, vous disposez d’une trace claire montrant les améliorations de réglage apportées à chaque base de données SQL Azure. 

![Fonctionnement du réglage automatique](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Le réglage automatique dans Azure SQL Database partage sa logique de base avec le moteur de réglage automatique de SQL Server. Pour obtenir des informations techniques supplémentaires sur le mécanisme d’intelligence intégré, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Utiliser le réglage automatique

Le réglage automatique doit être activé manuellement sur votre abonnement. Pour activer le réglage automatique à l’aide du portail Azure, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).

Le réglage automatique peut fonctionner de façon autonome par l’application automatique de recommandations de réglage, notamment une vérification automatisée des gains de performances. 

Pour plus de contrôle, vous pouvez désactiver l’application automatique des recommandations de réglage et appliquer manuellement les recommandations par le biais du portail Azure. Vous pouvez également utiliser la solution uniquement pour afficher les recommandations de réglage automatique et les appliquer manuellement par le biais de scripts et d’outils de votre choix. 

Pour obtenir une vue d’ensemble du fonctionnement du réglage automatique et des scénarios d’utilisation classiques, regardez la vidéo incorporée :


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Options de réglage automatique

Les options de réglage automatique disponibles dans Azure SQL Database sont les suivantes :
 1. **CREATE INDEX** identifie les index qui peuvent améliorer les performances de votre charge de travail, crée les index et vérifie qu’ils améliorent les performances des requêtes.
 2. **DROP INDEX** identifie les index en double et redondants, ainsi que ceux qui n’ont pas été utilisés depuis longtemps.
 3. **FORCE LAST GOOD PLAN** identifie les requêtes SQL utilisant le plan d’exécution qui sont plus lentes que le plan précédent, puis utilise le dernier plan correct connu au lieu du plan de régression.

Azure SQL Database identifie des recommandations**CREATE INDEX**, **DROP INDEX** et **FORCE LAST GOOD PLAN** qui peuvent optimiser votre base de données, puis les affiche dans le portail Azure. Pour plus d’informations sur l’identification des index à modifier, voir [Rechercher des recommandations d’index dans le portail Azure](sql-database-advisor-portal.md). Vous pouvez appliquer manuellement les recommandations à l’aide du portail ou vous pouvez laisser Azure SQL Database appliquer automatiquement les recommandations, surveiller la charge de travail après le changement et vérifier que la recommandation améliore les performances de votre charge de travail.

Vous pouvez activer ou désactiver les options de réglage automatique par base de données ou vous pouvez les configurer sur le serveur logique et les appliquer sur chaque base de données qui hérite des paramètres du serveur. La méthode recommandée consiste à configurer les options de réglage automatique sur le serveur et à hériter des paramètres sur les bases de données. Cette méthode simplifie la gestion des options de réglage automatique sur un grand nombre de bases de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database afin de gérer votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour consulter et appliquer manuellement des recommandations de réglage d’automatique, consultez [Rechercher et appliquer des recommandations en matière de performances](sql-database-advisor-portal.md).
- Pour en savoir plus sur la création de notifications par e-mail pour les recommandations de réglage automatique, consultez [Notifications par e-mail pour le réglage automatique](sql-database-automatic-tuning-email-notifications.md)
- Pour en savoir plus sur l’intelligence intégrée utilisée dans le réglage automatique, consultez [Artificial Intelligence tunes Azure SQL Databases (L’intelligence artificielle règle les bases de données SQL Azure)](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Pour en savoir plus sur le fonctionnement du réglage automatique dans Azure SQL Database et SQL Server 2017, consultez [Réglage automatique dans SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
