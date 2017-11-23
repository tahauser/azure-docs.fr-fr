---
title: Bonnes pratiques pour Azure SQL Data Sync | Microsoft Docs
description: "Découvrez les pratiques recommandées pour configurer et exécuter Azure SQL Data Sync."
services: sql-database
ms.date: 11/13/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d9529fc8acd9347b0505b1c578febc1c2219b37
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="best-practices-for-sql-data-sync-preview"></a>Bonnes pratiques pour SQL Data Sync (préversion) 

Cet article décrit les bonnes pratiques pour Azure SQL Data Sync (Préversion).

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](sql-database-sync-data.md).

## <a name="security-and-reliability"></a> Sécurité et fiabilité

### <a name="client-agent"></a>Agent client

-   Installez l’agent client à l’aide du compte doté des moindres privilèges ayant accès aux services réseau.

-   Il est préférable que l’agent client soit installé sur un ordinateur distinct de l’ordinateur SQL Server local.

-   N’inscrivez pas une base de données locale auprès de plusieurs agents,

-   même si vous synchronisez différentes tables pour différents groupes de synchronisation.

-   L’inscription d’une base de données locale auprès de plusieurs agents clients risque de poser des problèmes lors de la suppression de l’un des groupes de synchronisation.

### <a name="database-accounts-with-least-required-privilege"></a>Comptes de base de données avec le moins de privilèges nécessaires

-   **Pour la configuration de la synchronisation**. Créer/Modifier la table, Modifier la base de données, Créer une procédure, Sélectionner / Modifier le schéma, Créer un type défini par l’utilisateur.

-   **Pour la synchronisation continue**. Sélectionner / Insérer / Mettre à jour / Supprimer sur les tables sélectionnées pour la synchronisation et sur les métadonnées de synchronisation et les tables de suivi, autorisation Exécuter sur les procédures stockées créées par le service, autorisation Exécuter sur les types de tables définis par l’utilisateur.

-   **Pour l’annulation du provisionnement**. Modifier sur les tables qui font partie de la synchronisation, Sélectionner / Supprimer sur les tables de métadonnées de synchronisation, Contrôler sur les tables de suivi de synchronisation, les procédures stockées et les types définis par l’utilisateur.

**Comment faire pour utiliser ces informations alors qu’il n’existe qu’une seule information d’identification pour une base de données dans le groupe de synchronisation ?**

-   Changez les informations d’identification pour différentes phases (par exemple, *credential1* pour la configuration et *credential2* pour les opérations continues).

-   Modifiez l’autorisation des informations d’identification (autrement dit, modifiez l’autorisation après avoir configuré la synchronisation).

## <a name="setup"></a>Paramétrage

### <a name="database-considerations-and-constraints"></a> Considérations et contraintes relatives aux bases de données

#### <a name="sql-database-instance-size"></a>Taille de l’instance de SQL Database

Quand vous créez une instance de SQL Database, définissez la taille maximale afin qu’elle soit toujours supérieure à la base de données que vous déployez. Si vous n’affectez pas à la taille maximale une valeur supérieure à la base de données déployée, la synchronisation échoue. Comme la croissance n’est pas automatique, vous pouvez exécuter une instruction ALTER DATABASE pour augmenter la taille de la base de données après sa création. Veillez à rester dans les limites de taille d’instance SQL Database.

> [!IMPORTANT]
> SQL Data Sync stocke des métadonnées supplémentaires avec chaque base de données. Veillez à prendre en compte ces métadonnées quand vous calculez l’espace nécessaire. La quantité de surcharge ajoutée est régie par la largeur des tables (par exemple, des tables étroites nécessitent davantage de surcharge) et la quantité de trafic.

### <a name="table-considerations-and-constraints"></a> Considérations et contraintes relatives aux tables

#### <a name="selecting-tables"></a>Sélection de tables

Toutes les tables d’une base de données ne doivent pas obligatoirement être dans un groupe de synchronisation. La sélection des tables à inclure dans un groupe de synchronisation et de celles à exclure (ou à inclure dans un autre groupe de synchronisation) peut avoir un impact sur l’efficacité et les coûts. Incluez dans un groupe de synchronisation uniquement les tables dont l’inclusion est exigée par des besoins professionnels, ainsi que les tables sur lesquelles elles dépendent.

#### <a name="primary-keys"></a>Clés primaires

Chaque table dans un groupe de synchronisation doit avoir une clé primaire. Le service SQL Data Sync (Préversion) ne peut pas synchroniser une table qui n’a pas de clé primaire.

Avant le déploiement en production, testez les performances de synchronisation initiales et actuelles.

### <a name="provisioning-destination-databases"></a> Provisionnement des bases de données de destination

SQL Data Sync (Préversion) fournit un provisionnement automatique de base des bases de données.

Cette section présente les limitations du provisionnement offert par SQL Data Sync (Préversion).

#### <a name="auto-provisioning-limitations"></a>Limitations du provisionnement automatique

Voici les limitations du provisionnement automatique offert par SQL Data Sync (Préversion).

-   Seules les colonnes sélectionnées sont créées dans la table de destination.
Par conséquent, si certaines colonnes ne font pas partie du groupe de synchronisation, elles ne sont pas provisionnées dans les tables de destination.

-   Des index sont créés uniquement pour les colonnes sélectionnées.
Si l’index de la table source a des colonnes qui ne font pas partie du groupe de synchronisation, ces index ne sont pas provisionnés dans les tables de destination.

-   Les index sur des colonnes de type XML ne sont pas provisionnés.

-   Les contraintes CHECK ne sont pas provisionnées.

-   Les déclencheurs existants sur les tables sources ne sont pas provisionnés.

-   Les vues et procédures stockées ne sont pas créées sur la base de données de destination.

#### <a name="recommendations"></a>Recommandations

-   Utilisez la fonctionnalité de provisionnement automatique uniquement pour tester le service.

-   Pour la production, vous devez provisionner le schéma de base de données.

### <a name="locate-hub"></a> Emplacement de la base de données Hub

#### <a name="enterprise-to-cloud-scenario"></a>Scénario entreprise-à-cloud

Pour réduire la latence, conservez la base de données Hub proche de la plus grande concentration du trafic de base de données du groupe de synchronisation.

#### <a name="cloud-to-cloud-scenario"></a>Scénario cloud-à-cloud

-   Quand toutes les bases de données d’un groupe de synchronisation sont dans un même centre de données, le hub doit se trouver dans le même centre de données. Cette configuration réduit la latence et le coût de transfert de données entre les centres de données.

-   Quand les bases de données d’un groupe de synchronisation se trouvent dans plusieurs centres de données, le hub doit se trouver dans le même centre de données que la plupart des bases de données et du trafic de base de données.

#### <a name="mixed-scenarios"></a>Scénarios mixtes

Appliquez les recommandations ci-dessus aux configurations de groupe de synchronisation plus complexes.

## <a name="sync"></a>Synchronisation

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> Éviter une synchronisation initiale lente et coûteuse

Cette section traite de la synchronisation initiale d’un groupe de synchronisation et de ce que vous pouvez faire pour éviter qu’elle ne dure plus longtemps que nécessaire et ne coûte plus qu’elle ne devrait.

#### <a name="how-initial-synchronization-works"></a>Fonctionnement de la synchronisation initiale

Quand vous créez un groupe de synchronisation, commencez avec des données d’une seule base de données. Si vous avez des données dans plusieurs bases de données, SQL Data Sync (Préversion) traite chaque ligne comme un conflit qui doit être résolu. Cette résolution de conflit ralentit la synchronisation initiale, qui peut prendre de plusieurs jours à plusieurs mois, en fonction de la taille de la base de données.

En outre, si les bases de données se trouvent dans différents centres de données, les coûts de la synchronisation initiale sont plus élevés que nécessaire, étant donné que chaque ligne doit être déplacée entre les différents centres de données.

#### <a name="recommendation"></a>Recommandation

Si possible, commencez avec les données d’une seule des bases de données du groupe de synchronisation.

### <a name="design-to-avoid-synchronization-loops"></a> Éviter les boucles de synchronisation

Une boucle de synchronisation se produit quand il existe des références circulaires dans un groupe de synchronisation. Dans ce cas, chaque modification apportée à une base de données est répliquée dans les bases de données du groupe de synchronisation de manière circulaire et indéfiniment. Vous devez éviter les boucles de synchronisation, car elles dégradent les performances et peuvent augmenter considérablement les coûts.

### <a name="handling-changes-that-fail-to-propagate"></a> Gestion des modifications dont la propagation échoue

#### <a name="reasons-that-changes-fail-to-propagate"></a>Raisons pour lesquelles la propagation des modifications échoue

La propagation des modifications peut échouer pour de nombreuses raisons, parmi lesquelles :

-   Incompatibilité de schéma/type de données

-   Tentative d’insertion de valeurs null dans des colonnes non nullables

-   Violation de contraintes de clé étrangère

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Que se passe-t-il quand la propagation des modifications échoue ?

-   Le groupe de synchronisation affiche un état d’avertissement.

-   Les détails sont disponibles dans la visionneuse du journal de l’interface utilisateur du portail.

-   Si le problème n’est pas résolu pendant 45 jours, la base de données devient obsolète.

> [!NOTE]
> Ces modifications ne sont jamais propagées. La seule façon de récupérer consiste à recréer le groupe de synchronisation.

#### <a name="recommendation"></a>Recommandation

Surveillez l’intégrité du groupe de synchronisation et de la base de données régulièrement par le biais de l’interface du portail et du journal.


## <a name="maintenance"></a>Maintenance 

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> Éviter les bases de données et les groupes de synchronisation obsolètes

Un groupe de synchronisation ou une base de données d’un groupe de synchronisation peut devenir obsolète. Quand l’état d’un groupe de synchronisation est « obsolète », il cesse de fonctionner. Quand l’état d’une base de données est « obsolète », il y a un risque de perte de données. Il est préférable d’éviter ces situations plutôt que d’avoir à les corriger.

#### <a name="avoid-out-of-date-databases"></a>Éviter les bases de données obsolètes

Une base de données bascule à l’état obsolète quand elle est hors connexion depuis 45 jours ou plus. Pour éviter qu’une base de données ne bascule à l’état obsolète, veillez à ce qu’aucune des bases de données ne soit hors connexion pendant 45 jours ou plus.

#### <a name="avoid-out-of-date-sync-groups"></a>Éviter les groupes de synchronisation obsolètes

Un groupe de synchronisation bascule à l’état obsolète en cas d’échec de propagation d’une modification dans le groupe de synchronisation vers le reste du groupe de synchronisation pendant 45 jours ou plus. Pour éviter qu’un groupe de synchronisation ne bascule à l’état obsolète, vérifiez régulièrement le journal d’historique du groupe de synchronisation. Assurez-vous que tous les conflits sont résolus et que les modifications sont propagées dans les bases de données du groupe de synchronisation.

Voici quelques-unes des raisons pour lesquelles l’application d’une modification dans un groupe de synchronisation peut échouer :

-   Incompatibilité de schéma entre des tables

-   Incompatibilité de données entre des tables

-   Insertion d’une ligne avec une valeur null dans une colonne qui n’autorise pas les valeurs null

-   Mise à jour d’une ligne avec une valeur qui enfreint une contrainte de clé étrangère

Vous pouvez empêcher qu’un groupe de synchronisation ne devienne obsolète en :

-   Mettant à jour le schéma pour autoriser les valeurs contenues dans les lignes ayant provoqué l’échec.

-   Mettant à jour les valeurs de clé étrangères pour inclure les valeurs contenues dans les lignes ayant provoqué l’échec.

-   Mettant à jour les valeurs de données dans la ligne ayant provoqué l’échec pour qu’elles soient compatibles avec le schéma ou les clés étrangères dans la base de données cible.

### <a name="avoid-deprovisioning-issues"></a> Éviter les problèmes d’annulation du provisionnement

Dans certaines circonstances, la désinscription d’une base de données auprès d’un agent client peut entraîner l’échec des synchronisations.

#### <a name="scenario"></a>Scénario

1. Le groupe de synchronisation A a été créé avec une instance de SQL Database et une base de données SQL Server locale, qui est associée à l’agent local 1.

2. La même base de données locale est inscrite auprès de l’agent local 2 (cet agent n’est associé à aucun groupe de synchronisation).

3. Si vous annulez l’inscription de la base de données locale auprès de l’agent local 2, cela supprime les tables de suivi/métadonnées pour le groupe de synchronisation A pour la base de données locale.

4. Désormais, les opérations du groupe de synchronisation A échouent avec l’erreur suivante : « L’opération en cours a échoué, car la base de données n’est pas configurée pour la synchronisation ou vous ne disposez pas des autorisations nécessaires sur les tables de configuration de synchronisation. »

#### <a name="solution"></a>Solution

Évitez que ces situations ne se produisent en n’inscrivant jamais une base de données auprès de plusieurs agents.

Pour résoudre ce problème

1. Supprimez la base de données de chaque groupe de synchronisation auquel elle appartient.

2. Rajoutez la base de données dans chaque groupe de synchronisation où vous venez de la retirer.

3. Déployez chaque groupe de synchronisation concerné (ce qui provisionne la base de données).

### <a name="modifying-your-sync-group"></a> Modification d’un groupe de synchronisation

N’essayez pas de supprimer une base de données d’un groupe de synchronisation et de modifier ensuite le groupe de synchronisation sans avoir déployé au préalable l’une des modifications.

Tout d’abord, supprimez une base de données d’un groupe de synchronisation. Ensuite, déployez la modification et attendez que l’annulation du provisionnement soit terminée. Une fois cette opération terminée, vous pouvez modifier le groupe de synchronisation et déployer les modifications.

Si vous tentez de supprimer une base de données et de modifier un groupe de synchronisation sans avoir déployé au préalable l’une des modifications, l’une ou l’autre opération échoue et l’interface du portail peut basculer dans un état incohérent. Dans ce cas, vous pouvez actualiser la page pour restaurer l’état correct.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md)
-   [Configurer Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Surveiller Azure SQL Data Sync avec OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Résoudre les problèmes liés à Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
