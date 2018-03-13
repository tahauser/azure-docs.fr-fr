---
title: "Exercices de récupération d’urgence de SQL Database | Microsoft Docs"
description: "Découvrez les conseils et meilleures pratiques pour l’utilisation d’Azure SQL Database pour effectuer des exercices de récupération d’urgence."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 10/20/2016
ms.workload: Inactive
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 73c2cbe978c980cbe1269b34cdb9f5ff86113e61
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="performing-disaster-recovery-drill"></a>Exécution d'un exercice de récupération d'urgence
Nous recommandons de valider régulièrement la préparation des applications à la récupération. La vérification du comportement de l'application et des implications en matière de pertes de données et/ou d'interruptions en cas basculement constitue une bonne pratique. Il s'agit également d'une exigence figurant dans la plupart des normes industrielles dans le cadre d'une certification de la continuité des activités.

L'exécution d'un exercice de récupération d'urgence comprend :

* la simulation d'une défaillance des couches de données
* la récupération
* la validation de l'intégrité des applications après la récupération

Le flux de travail à exécuter peut varier en fonction de la [conception de votre application pour la continuité des activités](sql-database-business-continuity.md). Cet article décrit les bonnes pratiques pour l’exécution d’un exercice de récupération d’urgence dans le contexte d’Azure SQL Database.

## <a name="geo-restore"></a>Géo-restauration
Afin d’éviter la perte potentielle de données lors d’un exercice de récupération d’urgence, effectuez l’exercice dans un environnement de test en créant une copie de l’environnement de production et en l’utilisant pour vérifier le flux de travail de basculement de l’application.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez renommer la base de données source. Cela entraîne des échecs de connexion de l’application.

#### <a name="recovery"></a>Récupérer
* Effectuez la géorestauration de la base de données sur un autre serveur, comme décrit [ici](sql-database-disaster-recovery.md).
* Modifiez la configuration de l’application pour établir une connexion aux bases de données récupérées, puis suivez le guide [Configurer une base de données après récupération](sql-database-disaster-recovery.md) pour achever la récupération.

#### <a name="validation"></a>Validation
* Terminez l’exercice en vérifiant l’intégrité de l’application après la récupération (notamment les chaînes de connexion, les connexions, le test des fonctionnalités de base ou d’autres validations faisant partie des procédures d’approbation d’application standard).

## <a name="failover-groups"></a>Groupes de basculement
Pour une base de données protégée à l’aide de groupes de basculement, l’exercice implique un basculement planifié sur le serveur secondaire. Le basculement planifié garantit que les bases de données primaire et secondaire dans le groupe de basculement restent synchronisées quand les rôles sont permutés. À la différence du basculement non planifié, cette opération n’entraîne pas de perte de données. L’exercice peut donc être exécuté dans l’environnement de production.

#### <a name="outage-simulation"></a>Simulation d'une défaillance
Pour simuler la défaillance, vous pouvez désactiver l’application web ou une machine virtuelle connectée à la base de données. Cela entraîne des échecs de connectivité des clients web.

#### <a name="recovery"></a>Récupérer
* Vérifiez que la configuration de l’application dans la région de récupération d’urgence pointe vers l’ancienne base de données secondaire qui devient la nouvelle base de données primaire entièrement accessible.
* Lancez le [basculement planifié](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) du groupe de basculement sur le serveur secondaire.
* Suivez le guide [Configure a database after recovery](sql-database-disaster-recovery.md) pour effectuer la restauration.

#### <a name="validation"></a>Validation
Terminez l’exercice en vérifiant l’intégrité de l’application après la récupération (notamment la connectivité, le test des fonctionnalités de base ou d’autres validations nécessaire pour les autorisations de l’exercice).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les scénarios de continuité d’activité, consultez [Scénarios de continuité](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
* Pour plus d’informations sur les options de récupération plus rapides, consultez [Géoréplication active et groupes de basculement](sql-database-geo-replication-overview.md).  
