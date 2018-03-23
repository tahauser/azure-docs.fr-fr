---
title: Concevez votre première solution Azure SQL Database | Microsoft Docs
description: Apprenez à concevoir votre première base de données Azure SQL Database dans le portail Azure avec SQL Server Management Studio.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop databases
ms.topic: tutorial
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 12301d46b497b49032b1b8bdefe2056a7df143d1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="design-your-first-azure-sql-database"></a>Concevoir votre première base de données SQL Azure

Azure SQL Database est une solution DBaaS relationnelle gérée dans Microsoft Cloud (Azure). Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure et [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) pour : 

> [!div class="checklist"]
> * Créer une base de données dans le portail Azure
> * Configurer une règle de pare-feu au niveau du serveur dans le portail Azure
> * Connexion à la base de données avec SSMS
> * Créer des tables avec SSMS
> * Charger en masse des données avec BCP
> * Interroger ces données avec SSMS
> * Restaurer la base de données à un [point de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) antérieur dans le portail Azure

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, vérifiez que les éléments suivants sont installés :
- La dernière version de [SSMS](https://msdn.microsoft.com/library/ms174173.aspx) (SQL Server Management Studio).
- La dernière version de [BCP et SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Créer une base de données SQL vide

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md). 

Pour créer une base de données SQL vide, suivez la procédure suivante. 

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Dans la page **Nouveau**, sélectionnez **Bases de données**, puis **Créer** sous **SQL Database** dans **cette même** page.

   ![créer une base de données vide](./media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). | 
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

4. Cliquez sur **Serveur** pour créer et configurer un serveur pour votre nouvelle base de données. Remplissez le **formulaire de nouveau serveur** avec les informations suivantes : 

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
   | **Connexion d’administrateur du serveur** | Nom valide | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données).|
   | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
   | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

   ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU et la quantité de stockage. Explorez les options concernant le nombre de DTU et le stockage disponible pour chaque niveau de service. 

7. Pour ce tutoriel, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **100 DTU (S3)** et **400** Go de stockage.

   ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Acceptez les conditions d’utilisation de la préversion pour pouvoir utiliser l’option **Stockage de composants additionnels**. 

   > [!IMPORTANT]
   > \* Les tailles de stockage supérieures à la quantité de stockage inclue sont en version préliminaire et des coûts supplémentaires s’appliquent. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis – Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Après avoir sélectionné le niveau du serveur, le nombre de DTU et la quantité de stockage, cliquez sur **Appliquer**.  

10. Sélectionnez un **classement** pour la base de données vide (pour ce didacticiel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Maintenant que vous avez rempli le formulaire SQL Database, cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend quelques minutes. 

12. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.
    
     ![notification](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu au niveau du serveur qui empêche les applications et les outils externes de se connecter au serveur ou à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour l’adresse IP de votre client afin de permettre la connectivité externe via le pare-feu de base de données SQL pour votre adresse IP uniquement. 

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, elle affiche le nom de serveur complet (tel que **mynewserver-20170824.database.windows.net**) et fournit des options pour poursuivre la configuration. 

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants. 

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png) 

3. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

   ![règle de pare-feu de serveur](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio ou de tout autre outil de votre choix à partir de cette adresse IP à l’aide du compte Administrateur de serveur créé au préalable.

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

   ![informations de connexion](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Connexion à la base de données avec SSMS

Utilisez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) pour établir une connexion à votre serveur Azure SQL Database.

1. Ouvrez SQL Server Management Studio.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :

   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Type de serveur | Moteur de base de données | Cette valeur est obligatoire |
   | Nom du serveur | Nom complet du serveur | Le nom doit être similaire à ce qui suit : **mon_nouveau_serveur20170824.database.windows.net**. |
   | Authentification | l’authentification SQL Server | L’authentification SQL est le seul type d’authentification que nous avons configuré dans ce didacticiel. |
   | Connexion | Compte d’administrateur de serveur | Il s’agit du compte que vous avez spécifié lorsque vous avez créé le serveur. |
   | Mot de passe | Mot de passe de votre compte d’administrateur de serveur | Il s’agit du mot de passe que vous avez spécifié lorsque vous avez créé le serveur. |

   ![connect to server](./media/sql-database-connect-query-ssms/connect.png)

3. Cliquez sur **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans la section **Se connecter à la base de données**, entrez **mySampleDatabase** pour vous connecter à cette base de données.

   ![connexion à la base de données sur le serveur](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre dans SSMS. 

5. Dans l’Explorateur d’objets, développez **Bases de données**, puis **mySampleDatabase** pour afficher les objets dans la base de données exemple.

   ![objets de base de données](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Créer des tables dans la base de données 

Créez un schéma de base de données avec quatre tables qui modélisent un système de gestion des étudiants pour les universités à l’aide de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) :

- Personne
- Cours
- Étudiant
- Attribuez à ce modèle un système de gestion des étudiants pour les universités

Le diagramme suivant montre comment ces tables sont liées entre elles. Certaines de ces tables référencent des colonnes d’autres tables. Par exemple, la table Étudiant référence la colonne **PersonId** de la table **Personne**. Étudiez le diagramme pour comprendre comment les tables présentées dans ce didacticiel sont liées entre elles. Pour découvrir comment créer des tables de base de données efficaces, consultez [Créer des tables de base de données efficaces](https://msdn.microsoft.com/library/cc505842.aspx). Pour plus d’informations sur le choix des types de données, consultez [Types de données](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Vous pouvez également utiliser le [concepteur de tables dans SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) pour créer et concevoir vos tables. 

![Relations entre les tables](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.

2. Dans la fenêtre de requête, exécutez la requête suivante pour créer quatre tables dans votre base de données : 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![créez des tables](./media/sql-database-design-first-database/create-tables.png)

3. Développez le nœud « tables » dans l’Explorateur d’objets SQL Server Management Studio pour afficher les tables que vous avez créées.

   ![tables ssms-créées](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables

1. Créez un dossier nommé **SampleTableData** dans le dossier Téléchargements pour y stocker les exemples de données pour votre base de données. 

2. Cliquez sur les liens suivants et enregistrez-les dans le dossier **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Ouvrez une fenêtre d’invite de commandes et accédez au dossier SampleTableData.

4. Exécutez les commande suivantes pour insérer des exemples de données dans les tables, en remplaçant les valeurs de **ServerName**, **DatabaseName**, **UserName** et **Password** avec les valeurs pour votre environnement.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Vous avez maintenant chargé des exemples de données dans les tables que vous avez créées précédemment.

## <a name="query-data"></a>Données de requête

Exécutez les requêtes suivantes pour récupérer des informations à partir des tables de base de données. Consultez [Écriture des requêtes SQL](https://technet.microsoft.com/library/bb264565.aspx) pour en savoir plus sur l’écriture des requêtes SQL. La première requête réunit les quatre tables pour rechercher tous les étudiants inscrits au cours de « Dominick Pope » pour lequel ils ont une note supérieure à 75 %. La deuxième requête réunit les quatre tables et recherche tous les cours que « Noe Coleman » a déjà suivis.

1. Dans une fenêtre de requête SQL Server Management Studio, exécutez la requête suivante :

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. Dans une fenêtre de requête SQL Server Management Studio, exécutez la requête suivante :

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurer une version antérieure d’une base de données

Imaginez que vous avez supprimé une table par inadvertance. Il s’agit de quelque chose que vous ne pouvez pas récupérer facilement. Azure SQL Database vous permet de revenir à n’importe quel moment des 35 derniers jours et de restaurer ce moment pour obtenir une nouvelle base de données. Vous pouvez utiliser cette base de données pour récupérer vos données supprimées. Les étapes suivantes restaurent la base de données à un point situé avant l’ajout des tables.

1. Sur la page SQL Database de votre base de données, cliquez sur **Restaurer** dans la barre d’outils. La page **Restauration** s’ouvre.

   ![restauration](./media/sql-database-design-first-database/restore.png)

2. Remplissez le formulaire **Restaurer** avec les informations requises :
    * Nom de la base de données : entrez un nom pour la base de données 
    * Point dans le temps : Sélectionnez l’onglet **Point dans le temps** dans le formulaire Restauration 
    * Point de restauration : Sélectionnez une heure avant la modification de la base de données
    * Serveur cible : Vous ne pouvez pas modifier cette valeur lors de la restauration d’une base de données 
    * Pool de base de données élastique : sélectionnez **Aucun**  
    * Niveau tarifaire : sélectionnez **20 DTU** et **40 Go** de stockage.

   ![point de restauration](./media/sql-database-design-first-database/restore-point.png)

3. Cliquez sur **OK** pour restaurer la base de données [à un point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) avant l’ajout des tables. La restauration d’une base de données vers un autre point dans le temps crée une base de données en double sur le même serveur que la base de données d’origine en date du point dans le temps que vous spécifiez, pour autant qu’il s’inscrive dans la période de rétention pour votre [niveau de service](sql-database-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes 
Dans ce didacticiel, vous avez appris à exécuter des tâches de base de données classiques telles que la création d’une base de données et de tables, le chargement et l’interrogation de données, ainsi que la restauration de la base de données à un point antérieur dans le temps. Vous avez appris à effectuer les actions suivantes :
> [!div class="checklist"]
> * Créer une base de données
> * Configurer une règle de pare-feu
> * Vous connecter à la base de données avec [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * créez des tables
> * Charger des données en bloc
> * Interroger ces données
> * Restaurer la base de données à un point antérieur dans le temps à l’aide des fonctionnalités de [restauration à un point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) de SQL Database

Passez au didacticiel suivant pour en savoir plus sur la conception d’une base de données à l’aide de Visual Studio et C#.

> [!div class="nextstepaction"]
>[Concevoir une base de données SQL Azure et se connecter avec C# et ADO.NET](sql-database-design-first-database-csharp.md)
