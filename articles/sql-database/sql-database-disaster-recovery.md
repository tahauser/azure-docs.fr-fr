---
title: Récupération d’urgence de SQL Database | Microsoft Docs
description: Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géoréplication active et de géorestauration du service Azure SQL Database.
services: sql-database
author: anosov1960
manager: jhubbard
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 03/05/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: e9ec0a0a602965561b77619123588db57c59993c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurer une base de données SQL Azure ou basculer vers une base de données secondaire
Le service Base de données SQL Azure offre les fonctionnalités suivantes pour la récupération après une panne :

* [Géoréplication active et groupes de basculement](sql-database-geo-replication-overview.md)
* [Géorestauration](sql-database-recovery-using-backups.md#point-in-time-restore)
* [Bases de données redondantes dans une zone](sql-database-high-availability.md)

Pour en savoir plus sur les scénarios de continuité d’activité et les fonctionnalités prenant en charge ces scénarios, consultez [Continuité des activités](sql-database-business-continuity.md).

> [!NOTE]
> Si vous utilisez des bases de données ou des pools Premium redondants dans une zone, sachez que le processus de récupération est automatisé et que le reste de ce document ne vous sera d’aucune utilité. 

### <a name="prepare-for-the-event-of-an-outage"></a>Préparation à une panne
Pour parvenir à une récupération vers une autre région de données à l’aide des groupes de basculement ou des sauvegardes géoredondantes, vous devez préparer un serveur dans un autre centre de données. Celui-ci deviendra le nouveau serveur principal en cas de besoin. Vous devez également suivre des étapes précises, documentées et approuvées pour garantir une récupération optimale. Les étapes de préparation sont les suivantes :

* Identifiez le serveur logique d’une autre région qui deviendra le nouveau serveur principal. Pour la géorestauration, il s’agit généralement d’un serveur dans la [région jumelée](../best-practices-availability-paired-regions.md) de la région dans laquelle se trouve votre base de données. Cela permet d’éliminer le coût d’un trafic supplémentaire pendant les opérations de géorestauration.
* Identifiez, et éventuellement définissez, les règles de pare-feu nécessaires au niveau du serveur pour permettre aux utilisateurs d’accéder à la nouvelle base de données primaire.
* Déterminez la façon dont vous souhaitez rediriger les utilisateurs vers le nouveau serveur principal, par exemple en modifiant des chaînes de connexion ou des entrées DNS.
* Identifiez, et éventuellement créez, les connexions d’accès qui doivent être présentes dans la base de données master sur le nouveau serveur principal, puis vérifiez que ces connexions disposent des autorisations appropriées dans la base de données master, le cas échéant. Pour plus d’informations, consultez [Gestion de la sécurité de la base de données SQL après la récupération d’urgence](sql-database-geo-replication-security-config.md)
* Identifiez les règles d’alerte qui doivent être mises à jour pour le mappage à la nouvelle base de données primaire.
* Documentation de la configuration de l’audit sur la base de données primaire actuelle
* Effectuez une [simulation d’une récupération d'urgence](sql-database-disaster-recovery-drills.md). Pour simuler une panne de géorestauration, vous pouvez supprimer ou renommer la base de données source pour empêcher l’application de se connecter. Pour simuler une panne à l’aide des groupes de basculement, vous pouvez désactiver l’application web ou la machine virtuelle connectée à la base de données ou encore basculer la base de données pour empêcher l’application de se connecter.

## <a name="when-to-initiate-recovery"></a>Quand initier la récupération ?
L'opération de récupération a un impact sur l'application. Elle requiert la modification de la chaîne de connexion SQL et la redirection avec DNS, et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement quand la défaillance est susceptible de durer plus longtemps que l’objectif de délai de récupération de votre application. Lorsque l'application est déployée en production, vous devez effectuer une surveillance régulière de l'intégrité de l'application et vous assurer des points suivants pour déclarer que la récupération est garantie :

1. Échec permanent de la connectivité de la couche d'application à la base de données.
2. Le portail Azure affiche une alerte concernant un incident ayant un impact important dans la région.

> [!NOTE]
> Si vous utilisez des groupes de basculement et que vous avez choisi le basculement automatique, le processus de récupération est automatique et transparent pour l’application. 

Selon la tolérance de votre application aux temps d’arrêt et la mise en cause potentielle de la responsabilité de votre entreprise, vous pouvez envisager les options de récupération suivantes.

Utilisez [Obtenir une base de données récupérable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) pour obtenir le dernier point de restauration avec réplication géographique.

## <a name="wait-for-service-recovery"></a>Attendre le rétablissement du service
Les équipes Azure mettent tous les efforts en œuvre pour restaurer le service aussi rapidement que possible, mais cela peut prendre plusieurs heures ou plusieurs jours selon la cause principale du problème.  Si votre application peut tolérer des temps d’arrêt importants, vous pouvez simplement attendre que le service soit rétabli. Dans ce cas, aucune action n’est requise de votre part. Vous pouvez consulter l’état actuel du service dans notre [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Une fois le service rétabli dans la région, la disponibilité de votre application est restaurée.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Basculer vers le serveur secondaire géorépliqué dans le groupe de basculement
Si les temps d’arrêt de votre application mettent en cause la responsabilité de votre entreprise, vous devez utiliser des groupes de basculement. Cela permet de restaurer rapidement la disponibilité de l’application dans une autre région en cas de panne. En savoir plus sur la [configuration des groupes de basculement](sql-database-geo-replication-portal.md)

Pour restaurer la disponibilité des bases de données, vous devez lancer le basculement vers le serveur secondaire à l’aide de l’une des méthodes prises en charge.

Utilisez l’un des guides suivants pour effectuer le basculement vers une base de données secondaire géorépliquée :

* [Basculer vers un serveur secondaire géorépliqué à l’aide du portail Azure](sql-database-geo-replication-portal.md)
* [Basculer vers le serveur secondaire à l’aide de PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)

## <a name="recover-using-geo-restore"></a>Récupérer à l’aide de la géorestauration
Si le temps d’arrêt de votre application ne met pas en cause votre responsabilité professionnelle, vous pouvez utiliser la [géorestauration](sql-database-recovery-using-backups.md) pour récupérer les bases de données de votre application. Cela permet de créer une copie de la base de données à partir de la dernière sauvegarde géo-redondante.

## <a name="configure-your-database-after-recovery"></a>Configurer votre base de données après récupération
Si vous utilisez la géorestauration pour récupérer après une panne, vous devez vous assurer que la connectivité aux nouvelles bases de données est correctement configurée pour permettre à l’application de reprendre un fonctionnement normal. Voici une liste de contrôle de tâches pour vous aider à remettre votre base de données restaurée en service.

### <a name="update-connection-strings"></a>Mettre à jour les chaînes de connexion
Comme votre base de données restaurée se trouve sur un autre serveur, vous devez mettre à jour la chaîne de connexion de votre application de sorte qu’elle pointe vers celui-ci.

Pour plus d’informations sur la modification des chaînes de connexion, consultez le langage de développement approprié pour votre [bibliothèque de connectivité](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurer les règles de pare-feu
Vous devez vous assurer que les règles de pare-feu configurées sur le serveur et sur la base de données correspondent à celles du serveur principal et de la base de données primaire. Pour plus d’informations, voir [Procédure : configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurer les identifiants de connexion et les utilisateurs de la base de données
Vous devez vous assurer que tous les identifiants de connexion utilisés par votre application existent sur le serveur qui héberge votre base de données restaurée. Pour plus d’informations, voir [Configuration de la sécurité de la géoréplication](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Vous devez configurer et tester les règles et les connexions (et leurs autorisations) du pare-feu de votre serveur pendant un exercice de récupération d’urgence. Ces objets au niveau du serveur et leur configuration peuvent ne pas être disponibles pendant la panne.
> 
> 

### <a name="setup-telemetry-alerts"></a>Configurer les alertes de télémétrie
Vous devez vous assurer que vos paramètres de règles d’alerte existants sont mis à jour de manière à mapper à la base de données restaurée et à l’autre serveur.

Pour en savoir plus, voir [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) et [Suivi de l’intégrité du service](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Activer la fonction d’audit
Si la fonction d’audit doit accéder à votre base de données, vous devez l’activer après la restauration de la base de données. Pour plus d’informations, consultez [Audit de base de données](sql-database-auditing.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur la conception de la continuité des activités et les scénarios de récupération, consultez [Scénarios de continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)

