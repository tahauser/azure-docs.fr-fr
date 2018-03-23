---
title: Déploiement des mises à niveau d’applications - Azure SQL Database | Microsoft Docs
description: Découvrez comment utiliser la géo-réplication de la base de données SQL pour prendre en charge les mises à niveau en ligne de votre application cloud.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: 6efc14e5f8fab5baafff50e8eb6844f8decca1c9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Gestion des mises à niveau propagées d’applications cloud à l’aide d’une géoréplication active de SQL Database
> [!NOTE]
> La [géoréplication active](sql-database-geo-replication-overview.md) est désormais disponible pour toutes les bases de données de tous niveaux.
> 

Découvrez comment utiliser la [géoréplication](sql-database-geo-replication-overview.md) dans SQL Database pour permettre des mises à niveau propagées de votre application cloud. Une mise à niveau est une opération qui entraîne une interruption de service ; il est donc recommandé de l’intégrer à votre conception et à votre planification de la continuité des activités. Dans cet article, nous allons examiner deux méthodes différentes permettant d’orchestrer le processus de mise à niveau propagée, avant de présenter les avantages et inconvénients de chaque option. Dans le cadre de cet article, nous allons utiliser une application simple sous la forme d’un site web utilisant une base de données unique comme couche de données. Notre objectif est de mettre à niveau la version 1 de l’application vers la version 2 sans que cela ait une incidence significative sur l’expérience des utilisateurs finaux. 

Lorsque vous évaluez les options de mise à niveau, vous devez tenir compte des facteurs suivants :

* Impact sur la disponibilité de l’application au cours des mises à niveau. Durée pendant laquelle la fonction de l’application risque d’être limitée ou détériorée.
* Possibilité de restauration en cas d’échec de la mise à niveau.
* Vulnérabilité de l’application dans l’éventualité où un sinistre se produirait indépendamment pendant la mise à niveau.
* Coût total du processus,  notamment les coûts de redondance et les coûts incrémentiels des composants temporaires utilisés par le processus de mise à niveau. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Mise à niveau d’applications dont la récupération d’urgence repose sur des sauvegardes de base de données
Si votre application s’appuie sur des sauvegardes automatiques de la base de données et utilise la géo-restauration pour la récupération d’urgence, elle est généralement déployée dans une seule région Azure. Dans ce cas, la mise à niveau implique la création d’un déploiement de sauvegarde de tous les composants de l’application impliqués dans la mise à niveau. Afin de minimiser l’interruption pour l’utilisateur final, vous devez utiliser Azure Traffic Manager (WATM) avec le profil de basculement.  Le schéma suivant illustre l’environnement d’exploitation avant la mise à niveau. Le point de terminaison <i>contoso-1.azurewebsites.net</i> représente un emplacement de production de l’application qui doit être mise à niveau. Pour offrir la possibilité de restaurer la mise à niveau, vous devez créer un emplacement intermédiaire avec une copie entièrement synchronisée de l’application. Les étapes suivantes permettent de préparer l’application à la mise à niveau :

1. Création d’un emplacement intermédiaire pour la mise à niveau. Pour ce faire, créez une base de données secondaire (1) et déployez un site web identique dans la même région Azure. Surveillez la base de données secondaire afin de déterminer si le processus d’amorçage est terminé.
2. Création d’un profil de basculement dans WATM en utilisant <i>contoso-1.azurewebsites.net</i> comme point de terminaison en ligne et <i>contoso-2.azurewebsites.net</i> comme point de terminaison hors connexion. 

> [!NOTE]
> Notez que les étapes préparatoires n’auront aucune incidence sur l’application dans l’emplacement de production et que celle-ci pourra fonctionner en mode d’accès complet.
>  

![Configuration de la géoréplication d’une base de données SQL. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Une fois les étapes de préparation terminées, l’application est prête pour la mise à niveau. Le schéma suivant illustre les étapes impliquées dans le processus de mise à niveau. 

1. Configuration de la base de données primaire dans l’emplacement de production en mode lecture seule (3). Ceci garantit que l’instance de production de l’application (V1) restera en lecture seule au cours de la mise à niveau, ce afin d’éviter une divergence des données entre les instances de base de données V1 et V2.  
2. Déconnexion de la base de données secondaire à l’aide du mode d’arrêt planifié (4). Cette étape permet de créer une copie indépendante entièrement synchronisée de la base de données primaire. Cette base de données est alors mise à niveau.
3. Configuration de la base de données primaire en mode lecture-écriture et exécution du script de mise à niveau dans l’emplacement intermédiaire (5).     

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Si la mise à niveau s’est correctement déroulée, vous êtes maintenant prêt à basculer les utilisateurs finaux sur la copie intermédiaire de l’application, qui deviendra alors l’emplacement de production de l’application.  Cette opération implique quelques étapes supplémentaires, comme l’illustre le schéma suivant.

1. Basculez le point de terminaison en ligne du profil WATM sur <i>contoso-2.azurewebsites.net</i>, qui pointe vers la version V2 du site web (6). Celui-ci devient à présent l’emplacement de production doté de l’application V2 vers lequel est dirigé le trafic de l’utilisateur final.  
2. Si vous n’avez plus besoin des composants de l’application V1, vous pouvez les supprimer en toute sécurité (7).   

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Si la mise à niveau échoue, par exemple en raison d’une erreur dans le script de mise à niveau, l’emplacement intermédiaire doit être considéré comme compromis. Pour restaurer l’application telle qu’elle se trouvait avant la mise à niveau, il vous suffit de restaurer l’accès complet à l’application dans l’emplacement de production. Les étapes sont indiquées sur le schéma suivant.    

1. Définition de la copie de base de données en mode lecture-écriture (8). Cette opération restaure fonctionnellement la V1 complète dans l’emplacement de production.
2. Exécution d’une analyse des causes premières et suppression des composants compromis dans l’emplacement intermédiaire (9). 

À ce stade, l’application est entièrement fonctionnelle et les étapes de la mise à niveau peuvent être répétées.

> [!NOTE]
> La restauration ne nécessite aucune modification du profil WATM, car celui-ci utilise déjà <i>contoso-1.azurewebsites.net</i> comme point de terminaison actif.
> 
> 

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Le principal **avantage** de cette option est qu’elle vous permet de mettre à niveau une application dans une seule région grâce à une série d’étapes simples. Le coût de la mise à niveau est relativement faible. L’ **inconvénient** est que, si une défaillance irrémédiable se produit pendant la mise à niveau, vous devrez redéployer l’application dans une autre région et restaurer la base de données à partir de la sauvegarde à l’aide de la géo-restauration pour pouvoir rétablir l’application telle qu’elle se trouvait avant la mise à niveau. Ce processus entraîne des interruptions de service importantes.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Mise à niveau d’applications dont la récupération d’urgence repose sur la géoréplication de la base de données
Si votre application s’appuie sur la géoréplication pour garantir la continuité d’activité, elle est déployée dans au moins deux régions différentes, avec un déploiement actif dans la région primaire et un déploiement de secours dans la région de sauvegarde. Outre les facteurs mentionnés précédemment, le processus de mise à niveau doit garantir que :

* l’application demeure constamment à l’abri des sinistres pendant le processus de mise à niveau ;
* les composants géo-redondants de l’application sont mis à niveau parallèlement aux composants actifs.

Pour atteindre ces objectifs, vous allez utiliser Azure Traffic Manager (WATM) à l’aide du profil de basculement avec un point de terminaison actif et trois points de terminaison de sauvegarde.  Le schéma suivant illustre l’environnement d’exploitation avant la mise à niveau. Les sites web <i>contoso-1.azurewebsites.net</i> et <i>dr.azurewebsites.net de contoso</i> représentent un emplacement de production de l’application avec redondance géographique complète. Pour offrir la possibilité de restaurer la mise à niveau, vous devez créer un emplacement intermédiaire avec une copie entièrement synchronisée de l’application. Pour avoir la garantie que l’application sera capable de récupérer rapidement en cas de défaillance irrémédiable pendant le processus de mise à niveau, l’emplacement intermédiaire doit également être géoredondant. Les étapes suivantes permettent de préparer l’application à la mise à niveau :

1. Création d’un emplacement intermédiaire pour la mise à niveau. Pour cela, vous devez créer une base de données secondaire (1) et déployer une copie identique du site web dans la même région Azure. Surveillez la base de données secondaire afin de déterminer si le processus d’amorçage est terminé.
2. Création d’une base de données secondaire géo-redondante dans l’emplacement intermédiaire en géo-répliquant la base de données secondaire dans la région de sauvegarde (on parle alors de « géo-réplication chaînée »). Surveillez la base de données de sauvegarde afin de déterminer si le processus d’amorçage est terminé (3).
3. Création d’une copie de secours du site web dans la région de sauvegarde et liaison de cette copie à la base secondaire géo-redondante (4).  
4. Ajout des points de terminaison supplémentaires <i>contoso-2.azurewebsites.net</i> et <i>contoso-3.azurewebsites.net</i> au profil de basculement dans WATM en tant que points de terminaison hors connexion (5). 

> [!NOTE]
> Notez que les étapes préparatoires n’auront aucune incidence sur l’application dans l’emplacement de production et que celle-ci pourra fonctionner en mode d’accès complet.
> 
> 

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Une fois les étapes de préparation terminées, l’emplacement intermédiaire est prêt pour la mise à niveau. Le schéma suivant illustre les étapes de la mise à niveau.

1. Configuration de la base de données primaire dans l’emplacement de production en mode lecture seule (6). Ceci garantit que l’instance de production de l’application (V1) restera en lecture seule au cours de la mise à niveau, ce afin d’éviter une divergence des données entre les instances de base de données V1 et V2.  
2. Déconnexion de la base de données secondaire se trouvant dans la même région à l’aide du mode d’arrêt planifié (7). Cette opération crée une copie indépendante entièrement synchronisée de la base de données primaire, qui deviendra automatiquement la base de données primaire à la fin du processus. Cette base de données est alors mise à niveau.
3. Configuration de la base de données primaire se trouvant dans l’emplacement intermédiaire en mode lecture-écriture et exécution du script de mise à niveau (8).    

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Si la mise à niveau s’est correctement déroulée, vous êtes maintenant prêt à basculer les utilisateurs finaux sur la version V2 de l’application. Le schéma suivant illustre les étapes impliquées dans ce processus.

1. Basculement du point de terminaison actif du profil WATM sur <i>contoso-2.azurewebsites.net</i>, qui pointe désormais vers la version V2 du site web (9). Il devient alors un emplacement de production comprenant l’application V2 et vers lequel est dirigé le trafic utilisateur. 
2. Si vous n’avez plus besoin de l’application V1, vous pouvez la supprimer en toute sécurité (10 et 11).  

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Si la mise à niveau échoue, par exemple en raison d’une erreur dans le script de mise à niveau, l’emplacement intermédiaire doit être considéré comme compromis. Pour restaurer l’application telle qu’elle se trouvait avant la mise à niveau, il vous suffit de rétablir l’utilisation de l’application dans l’emplacement de production avec un accès complet. Les étapes sont indiquées sur le schéma suivant.    

1. Configuration de la copie de la base de données primaire dans l’emplacement de production en mode lecture-écriture (12). Cette opération restaure fonctionnellement la V1 complète dans l’emplacement de production.
2. Exécution d’une analyse des causes premières et suppression des composants compromis dans l’emplacement intermédiaire (13 et 14). 

À ce stade, l’application est entièrement fonctionnelle et les étapes de la mise à niveau peuvent être répétées.

> [!NOTE]
> La restauration ne nécessite aucune modification du profil WATM, car celui-ci utilise déjà <i>contoso-1.azurewebsites.net</i> comme point de terminaison actif.
> 
> 

![Configuration de la géoréplication de SQL Database. Récupération d’urgence cloud.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Le principal **avantage** de cette option est qu’elle vous permet de mettre à niveau l’application et sa copie géo-redondant en parallèle sans compromettre votre continuité d’activité lors de la mise à niveau. L’ **inconvénient** est qu’elle implique une double redondance de chaque composant de l’application, ce qui augmente le coût total de l’opération. Elle implique également un flux de travail plus complexe. 

## <a name="summary"></a>Résumé
Les deux méthodes de mise à niveau décrites dans cet article présentent certaines différences en termes de complexité et de coût, mais les deux visent à réduire la durée pendant laquelle l’utilisateur final est limité aux opérations en lecture seule. Cette durée dépend directement de la durée du script de mise à niveau. Elle ne dépend pas la taille de la base de données, du niveau de service que vous avez choisi, de la configuration du site web ou d’autres facteurs que vous ne pouvez pas facilement contrôler. En effet, toutes les étapes de préparation sont dissociées de la procédure de mise à niveau et peuvent être effectuées sans incidence sur l’application de production. L’efficacité du script de mise à niveau est essentiel pour déterminer l’expérience utilisateur au cours des mises à niveau. La meilleure façon de l’améliorer consiste donc à concentrer vos efforts sur la création d’un script de mise à niveau aussi efficace que possible.  

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [Restaurer une base de données à partir de sauvegardes automatisées](sql-database-recovery-using-backups.md).
* Pour en savoir plus sur les options de récupération plus rapides, voir [Géoréplication active](sql-database-geo-replication-overview.md).


