---
title: Migrer votre entrepôt de données Azure vers le stockage Premium | Microsoft Docs
description: Instructions de migration d’un entrepôt de données vers le stockage Premium
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: ''
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 03/15/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 3b43bc17b7f9cf80a9520c5c573be3a48d82e4e7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrer votre entrepôt de données vers un stockage Premium
Azure SQL Data Warehouse propose depuis peu le [stockage Premium pour améliorer la prévisibilité des performances][premium storage for greater performance predictability]. Nous sommes maintenant prêts à migrer des entrepôts de données situés sur le stockage standard vers le stockage Premium. Vous pouvez utiliser la migration automatique ou, si vous préférez contrôler le déclenchement de la migration (ce qui implique un temps d’inactivité), effectuer la migration vous-même.

Si vous avez plusieurs entrepôts de données, suivez les instructions de la section [Planification de la migration automatique][automatic migration schedule] ci-dessous pour déterminer à quel moment ils doivent également être migrés.

## <a name="determine-storage-type"></a>Déterminer le type de stockage
Si vous avez créé un entrepôt de données avant les dates ci-dessous, vous utilisez actuellement le stockage standard.

| **Région** | **Entrepôt de données créé avant cette date** |
|:--- |:--- |
| Est de l’Australie |1er janvier 2018 |
| Chine orientale |1er novembre 2016 |
| Chine du Nord |1er novembre 2016 |
| Centre de l’Allemagne |1er novembre 2016 |
| Nord-Est de l’Allemagne |1er novembre 2016 |
| Inde-Ouest |1er février 2018 |
| Ouest du Japon |1er février 2018 |
| Centre-Nord des États-Unis |10 novembre 2016 |

## <a name="automatic-migration-details"></a>Détails sur la migration automatique
Par défaut, nous allons migrer votre base de données pour vous entre 18h00 et 6h00 (heure locale) selon les instructions de la section [Planification de la migration automatique][automatic migration schedule]. Votre entrepôt de données sera inutilisable pendant la migration. L’opération prendra environ une heure par To de stockage pour chaque entrepôt de données. La migration automatique ne donne lieu à aucune facturation.

> [!NOTE]
> Une fois la migration terminée, votre entrepôt de données sera remis en ligne et utilisable.
>
>

Microsoft effectue les opérations suivantes pour finaliser la migration (aucune intervention requise de votre part). Dans cet exemple, imaginez que votre entrepôt de données sur le stockage standard s’appelle « MyDW ».

1. Microsoft le renomme en « MyDW_DO_NOT_USE_[Horodatage] ».
2. Microsoft interrompt « MyDW_ DO_NOT_USE_ [Horodatage] ». Une sauvegarde est effectuée pendant ce temps. En cas de problème, il se peut que le processus s’interrompe et se relance plusieurs fois.
3. Microsoft crée un entrepôt de données nommé « MyDW » sur le stockage Premium à partir de la sauvegarde effectuée à l’étape 2. L’entrepôt « MyDW » n’apparaît que lorsque la restauration est terminée.
4. Une fois la restauration terminée, « MyDW » reprend les mêmes unités d’entrepôt de données (DWU) et le même état (actif ou en pause) qu’avant la migration.
5. Une fois la migration terminée, Microsoft supprime « MyDW_DO_NOT_USE_[Horodatage] ».

> [!NOTE]
> Les paramètres suivants ne sont pas conservés dans le cadre de la migration :
>
> * L’audit au niveau de la base de données doit être réactivé.
> * Les règles de pare-feu au niveau de la base de données doivent être rajoutées. Les règles de pare-feu au niveau du serveur ne sont pas affectées.
>
>

### <a name="automatic-migration-schedule"></a>Planification de la migration automatique
Les migrations automatiques s’effectuent entre 18h00 et 06h00 (heure locale) pendant le temps d’arrêt suivant.

| **Région** | **Date de début estimée** | **Date de fin estimée** |
|:--- |:--- |:--- |
| Est de l’Australie |19 mars 2018 |20 mars 2018 |
| Chine orientale |Déjà migrées |Déjà migrées |
| Chine du Nord |Déjà migrées |Déjà migrées |
| Centre de l’Allemagne |Déjà migrées |Déjà migrées |
| Nord-Est de l’Allemagne |Déjà migrées |Déjà migrées |
| Inde-Ouest |19 mars 2018 |20 mars 2018 |
| Ouest du Japon |19 mars 2018 |20 mars 2018 |
| Centre-Nord des États-Unis |Déjà migrées |Déjà migrées |

## <a name="self-migration-to-premium-storage"></a>Migration ponctuelle vers le stockage Premium
Si vous souhaitez déterminer à quel moment le temps d’arrêt doit se produire, suivez la procédure ci-après qui permet de migrer un entrepôt de données du stockage standard vers le stockage Premium. Si vous choisissez cette option, vous devez effectuer la migration ponctuelle avant le début de la migration automatique dans cette région. Vous évitez ainsi tout risque de conflit dû à la migration automatique (reportez-vous à la [planification de la migration automatique][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Instructions relatives à la migration ponctuelle
Pour migrer votre entrepôt de données vous-même, utilisez les fonctionnalités de sauvegarde et de restauration. La partie restauration de la migration dure environ une heure par To de stockage pour chaque entrepôt de données. Si vous souhaitez conserver le même nom une fois la migration terminée, suivez cette [procédure de changement de nom pendant la migration][steps to rename during migration].

1. [Suspendez][Pause] votre entrepôt de données. 
2. [Effectuez une restauration][Restore] à partir de votre instantané le plus récent.
3. Supprimez votre entrepôt de données sur le stockage standard. **Si vous n’effectuez pas cette opération, vous serez facturé pour les deux entrepôts de données.**

> [!NOTE]
>
> Lorsque vous restaurez votre entrepôt de données, vérifiez que le point de restauration le plus récent disponible est ultérieur à la pause de cet entrepôt de données.
>
> Les paramètres suivants ne sont pas conservés dans le cadre de la migration :
>
> * L’audit au niveau de la base de données doit être réactivé.
> * Les règles de pare-feu au niveau de la base de données doivent être rajoutées. Les règles de pare-feu au niveau du serveur ne sont pas affectées.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Renommer l’entrepôt de données pendant la migration (facultatif)
Deux bases de données situées sur un même serveur logique ne peuvent pas présenter le même nom. SQL Data Warehouse permet désormais de renommer un entrepôt de données.

Dans cet exemple, imaginez que votre entrepôt de données sur le stockage standard s’appelle « MyDW ».

1. Renommez « MyDW » à l’aide de la commande ALTER DATABASE suivante. (Dans cet exemple, nous allons le renommer « MyDW_BeforeMigration ».) Cette commande arrête toutes les transactions en cours et doit s’exécuter dans la base de données pour aboutir.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Suspendez][Pause] « MyDW_BeforeMigration ». 
3. À partir de votre dernier instantané, [restaurez][Restore] une nouvelle base de données avec l’ancien nom (par exemple « MyDW »).
4. Supprimez « MyDW_BeforeMigration ». **Si vous n’effectuez pas cette opération, vous serez facturé pour les deux entrepôts de données.**


## <a name="next-steps"></a>Étapes suivantes
Le stockage Premium augmente le nombre de fichiers blob de base de données dans l’architecture sous-jacente de votre entrepôt de données. Pour optimiser les avantages de ce changement en termes de performances, recréez vos index columnstore clustérisés à l’aide du script suivant. Ce script force le déplacement de certaines de vos données vers les objets blob supplémentaires. Si vous ne faites rien, les données sont naturellement redistribuées au fil du temps, lorsque vous en chargez d’autres dans vos tables.

Si vous rencontrez des problèmes avec votre entrepôt de données, [créez un ticket de support][create a support ticket] et indiquez « Migration vers le stockage Premium » comme cause possible.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
