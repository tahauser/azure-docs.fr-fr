---
title: Stocker les sauvegardes Azure SQL Database pendant 10 ans | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge le stockage de sauvegardes pendant 10 ans.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 12/22/2016
ms.author: sashan
ms.openlocfilehash: 2f31e89fce2746e57d6a670aef949d0d534af4c1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Stocker les sauvegardes Azure SQL Database pendant 10 ans
De nombreuses applications sont dédiées à la réglementation, à la conformité ou à d’autres fins professionnelles qui vous obligent à conserver des sauvegardes de données au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) Azure SQL Database. Avec la fonctionnalité de rétention de sauvegarde à long terme, vous pouvez stocker vos sauvegardes SQL Database dans un coffre Azure Recovery Services pendant 10 ans. Vous pouvez stocker jusqu’à 1 000 bases de données par coffre. Vous pouvez ensuite sélectionner n’importe quelle sauvegarde dans le coffre pour la restaurer en tant que nouvelle base de données.

> [!IMPORTANT]
> La rétention de sauvegarde à long terme est en préversion et disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, États-Unis du Centre, Asie de l’Est, États-Unis de l’Est, États-Unis de l’Est 2, Inde-Centre, Sud de l’Inde, Japon de l’Est, Japon de l’Ouest, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Europe de l’Ouest et États-Unis de l’Ouest.
>

> [!NOTE]
> Vous pouvez activer jusqu'à 200 bases de données par coffre pendant une période de 24 heures. Nous vous recommandons d’utiliser un coffre distinct pour chaque serveur afin de réduire l’impact de cette limite. 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>Comment fonctionne la rétention de sauvegarde SQL Database à long terme

Avec la rétention à long terme des sauvegardes, vous pouvez associer un serveur de base de données SQL à un coffre Azure Recovery Services. 

* Vous devez créer l’archivage dans l’abonnement Azure qui a créé le serveur SQL, dans la même région géographique et dans le même groupe de ressources. 
* Vous configurez ensuite une stratégie de rétention pour une base de données quelconque. La stratégie met en œuvre une copie hebdomadaire des sauvegardes complètes des bases de données dans le coffre Recovery Services et leur conservation pendant la période de rétention spécifiée (jusqu’à 10 ans). 
* Vous pouvez ensuite restaurer la base de données à partir d’une de ces sauvegardes dans une nouvelle base de données dans n’importe quel serveur de l’abonnement. Le stockage Azure crée une copie à partir de sauvegardes existantes, qui n’a aucun impact sur le niveau de performance de la base de données existante.

> [!TIP]
> Pour obtenir une procédure, consultez [Configurer et restaurer à partir de la rétention des sauvegardes à long terme Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="enable-long-term-backup-retention"></a>Activer la rétention des sauvegardes à long terme

Pour configurer la rétention de sauvegarde d’une base de données à long terme :

1. Créez un coffre Azure Recovery Services dans la même région, le même abonnement et le même groupe de ressources que votre serveur de base de données SQL. 
2. Inscrivez le serveur dans le coffre.
3. Créez une stratégie de protection Azure Recovery Services.
4. Appliquez la stratégie de protection aux bases de données nécessitant une rétention à long terme de la sauvegarde.

Pour configurer, gérer et restaurer une base de données depuis la rétention de sauvegarde à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services, effectuez l’une des opérations suivantes :

* À l’aide du portail Azure : cliquez sur **Rétention des sauvegardes à long terme**, sélectionnez une base de données, puis cliquez sur **Configurer**. 

   ![Sélectionner une base de données pour la rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* À l’aide de PowerShell : accédez à [Configurer et restaurer à partir de la rétention des sauvegardes à long terme Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>Restaurer une base de données stockée avec la fonctionnalité de rétention de sauvegarde à long terme

Pour restaurer à partir d’une sauvegarde conservée à long terme :

1. Répertoriez le coffre dans lequel la sauvegarde est stockée.
2. Répertoriez le conteneur mappé sur votre serveur logique.
3. Répertoriez la source de données au sein du coffre mappé sur votre base de données.
4. Répertoriez les points de récupération qui sont disponibles pour la restauration.
5. Restaurez la base de données à partir du point de récupération sur le serveur cible dans votre abonnement.

Pour configurer, gérer et restaurer une base de données depuis la rétention de sauvegarde à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services, effectuez l’une des opérations suivantes :

* À l’aide du portail Azure : accédez à [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](sql-database-long-term-backup-retention-configure.md). 

* À l’aide de PowerShell : accédez à [Gérer la rétention des sauvegardes à long terme à l’aide de PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="get-pricing-for-long-term-backup-retention"></a>Obtenir les prix de la rétention des sauvegardes à long terme

La rétention de sauvegarde à long terme d’une base de données SQL est facturée selon les [tarifs des services de sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

Une fois que le serveur de base de données SQL est inscrit dans le coffre, le stockage total utilisé par les sauvegardes hebdomadaires stockées dans le coffre vous est facturé.

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>Afficher les sauvegardes disponibles qui sont stockées avec rétention à long terme

Pour configurer, gérer et restaurer une base de données depuis la rétention de sauvegarde à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services à l’aide du portail Azure, effectuez l’une des opérations suivantes :

* À l’aide du portail Azure : accédez à [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](sql-database-long-term-backup-retention-configure.md). 

* À l’aide de PowerShell : accédez à [Gérer la rétention des sauvegardes à long terme à l’aide de PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disable-long-term-retention"></a>Désactiver la rétention à long terme

Le service de récupération gère automatiquement le nettoyage des sauvegardes en fonction de la stratégie de rétention fournie. 

Pour arrêter l’envoi des sauvegardes d’une base de données spécifique dans le coffre, supprimez la stratégie de rétention pour cette base de données.
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> Les sauvegardes qui se trouvent déjà dans le coffre ne sont pas affectées. Elles sont automatiquement supprimées par le service de récupération après l’expiration de leur période de rétention.

## <a name="long-term-backup-retention-faq"></a>Questions fréquentes (FAQ) sur la rétention des sauvegardes à long terme

**Puis-je supprimer manuellement des sauvegardes spécifiques du coffre ?**

Pas actuellement. Le coffre nettoie automatiquement les sauvegardes une fois la période de rétention expirée.

**Puis-je enregistrer mon serveur pour stocker des sauvegardes dans plus d’un coffre ?**

Non. Vous ne pouvez stocker des sauvegardes que dans un coffre à la fois.

**Puis-je avoir un serveur et un coffre dans différents abonnements ?**

Non. Le coffre et le serveur doivent se trouver dans le même abonnement et dans le même groupe de ressources.

**Puis-je utiliser un coffre que j’ai créé dans une région qui est différente de celle de mon serveur ?**

Non, le coffre et le serveur doivent se trouver dans la même région afin de réduire les délais de copie et d’éviter des frais de trafic.

**Combien de bases de données puis-je stocker dans un coffre ?**

Nous prenons en charge jusqu’à 1 000 bases de données par coffre. 

**Combien de coffres puis-je créer par abonnement ?**

Vous pouvez créer 25 coffres par abonnement.

**Combien de bases de données puis-je configurer par jour par coffre ?**

Vous pouvez configurer jusqu’à 200 bases de données par jour par coffre.

**La rétention de sauvegarde à long terme fonctionne-t-elle avec des pools élastiques ?**

Oui. Une base de données dans le pool peut être configurée avec la stratégie de rétention.

**Puis-je choisir l’heure de création de la sauvegarde ?**

Non, SQL Database contrôle la planification des sauvegardes afin de réduire l’impact sur le niveau de performance de vos bases de données.

**Transparent Data Encryption est activé pour ma base de données. Puis-je l’utiliser avec le coffre ?** 

Oui, Transparent Data Encryption est pris en charge. Même si la base de données d’origine n’existe plus, vous pouvez restaurer la base de données depuis le coffre.

**Que se passe-t-il avec les sauvegardes dans le coffre si mon abonnement est suspendu ?** 

Si votre abonnement est suspendu, nous conservons les bases de données existantes et les sauvegardes. Les nouvelles sauvegardes ne sont pas copiées dans le coffre. Une fois l’abonnement réactivé, le service copie à nouveau les sauvegardes dans le coffre. Vous pouvez effectuer des restaurations à partir de votre coffre à l’aide de sauvegardes copiées avant la suspension de l’abonnement. 

**Puis-je obtenir un accès aux fichiers de sauvegarde de base de données SQL afin de les télécharger ou restaurer sur le serveur SQL ?**

Non, pas actuellement.

**Est-il possible d’avoir plusieurs planifications (quotidienne, hebdomadaire, mensuelle, annuelle) au sein d’une stratégie de rétention SQL ?**

Non. Les planifications multiples sont prises en charge uniquement pour les sauvegardes de machines virtuelles.

**Que se passe-t-il si nous configurons la rétention à long terme de la sauvegarde sur une base de données qui est une base de données de géoréplication active secondaire ?**

Comme nous ne prenons pas en charge les sauvegardes sur des réplicas, il n’existe aucune option de rétention de sauvegarde à long terme sur les bases de données secondaires. Toutefois, il est important pour les utilisateurs de configurer la rétention à long terme de la sauvegarde sur une base de données de géoréplication active secondaire pour les raisons suivantes :
* Quand un basculement a lieu et que la base de données devient une base de données primaire, nous procédons à une sauvegarde complète, qui est chargée dans un coffre.
* La configuration de cette rétention de sauvegarde à long terme n’entraîne aucun coût supplémentaire pour le client sur une base de données secondaire.

## <a name="next-steps"></a>Étapes suivantes
Dans la mesure où les sauvegardes de base de données protègent les données des corruptions et des suppressions accidentelles, elles sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence. Pour en savoir plus sur les autres solutions de continuité des activités SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
