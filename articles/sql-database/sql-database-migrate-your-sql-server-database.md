---
title: "Migrer une base de données SQL Server vers Azure SQL Database | Microsoft Docs"
description: "Apprenez à migrer votre base de données SQL Server vers Azure SQL Database."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,migrate
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 01/29/2018
ms.author: carlrab
ms.openlocfilehash: acba1aff0af8d54fca44af62ab46e1fd1a9d607e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrer votre base de données SQL Server vers Azure SQL Database

Le déplacement de votre base de données SQL Server vers une base de données SQL Azure est simple. Il vous suffit de créer une base de données SQL vide dans Azure, puis d’utiliser l’outil [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Data Migration Assistant) pour importer la base de données dans Azure. Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données SQL Azure vide dans le portail Azure (à l’aide d’un serveur Azure SQL Database nouveau ou existant)
> * Créer une règle de pare-feu au niveau du serveur dans le portail Azure (si elle n’a pas déjà été créée)
> * Utiliser l’outil [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Data Migration Assistant) pour importer votre base de données SQL Server dans la base de données SQL Azure vide 
> * Utiliser l’outil [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) pour modifier les propriétés de la base de données

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vérifiez que les prérequis suivants sont remplis :

- La dernière version de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- La dernière version de l’outil [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Data Migration Assistant) est installée.
- Vous avez identifié une base de données à migrer, à laquelle vous avez accès. Ce didacticiel utilise la [base de données OLTP AdventureWorks SQL Server 2008 R2](https://msftdbprodsamples.codeplex.com/releases/view/59211) sur une instance de SQL Server 2008 R2 ou version ultérieure, mais vous pouvez utiliser une base de données de votre choix. Pour résoudre des problèmes de compatibilité, utilisez [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

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
   >\* Au niveau Premium, plus de 1 To de stockage est actuellement disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, Centre du Canada, Est du Canada, Centre des États-Unis, France-Centre, Centre de l’Allemagne, Est du Japon, Ouest du Japon, Corée Centre, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Royaume-Uni Sud, Royaume-Uni Ouest, Est des États-Unis 2, Ouest des États-Unis, Gouvernement des États-Unis - Virginie et Europe de l’Ouest. Consultez [Limitations actuelles P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
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

Vous pouvez maintenant vous connecter au serveur SQL Database et à ses bases de données à l’aide de SQL Server Management Studio, de Data Migration Assistant ou de tout autre outil de votre choix à partir de cette adresse IP, à l’aide du compte Administrateur de serveur créé dans la procédure précédente.

> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur SQL Azure à l’aide des outils clients, y compris Data Migration Assistant et SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

   ![informations de connexion](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Migrer votre base de données

Effectuez les étapes suivantes pour utiliser **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** en vue d’évaluer la disponibilité de votre base de données pour la migration vers Azure SQL Database, et d’effectuer cette migration.

1. Ouvrez **Data Migration Assistant**. Vous pouvez exécuter DMA sur n’importe quel ordinateur disposant d’une connectivité Internet et d’une connexion à l’instance SQL Server qui contient la base de données que vous projetez de migrer. Vous n’avez pas besoin de l’installer sur l’ordinateur qui héberge l’instance SQL Server que vous comptez migrer. La règle de pare-feu que vous avez créée précédemment doit être celle de l’ordinateur sur lequel vous exécutez Data Migration Assistant.

     ![ouvrir data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Dans le menu de gauche, cliquez sur **+ Nouveau** pour créer un projet **Évaluation**. Renseignez les valeurs demandées, puis cliquez sur **Créer** :

   | Paramètre      | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Type de projet | Migration | Vous devez choisir entre évaluer votre base de données pour la migration, ou évaluer la base de données et effectuer la migration dans le cadre d’un même flux de travail |
   |Nom du projet|Didacticiel sur la migration| Nom descriptif |
   |Type du serveur source| SQL Server | Il s’agit de la seule source actuellement prise en charge. |
   |Type du serveur cible| Base de données SQL Azure| Les options sont les suivantes : Azure SQL Database, SQL Server, SQL Server sur les machines virtuelles Azure |
   |Étendue de la migration| Schéma et données| Les options sont les suivantes : Schéma et données, Schéma uniquement, Données uniquement |
   
   ![nouveau projet de data migration assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  Dans la page **Sélectionner une source**, renseignez les valeurs demandées, puis cliquez sur **Connexion** :

    | Paramètre      | Valeur suggérée | Description | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nom du serveur | Le nom ou l’adresse IP de votre serveur | Le nom ou l’adresse IP de votre serveur |
    | Type d'authentification | Type d’authentification par défaut| Options : Authentification Windows, Authentification SQL Server, Authentification intégrée Active Directory, Authentification par mot de passe Active Directory |
    | Nom d’utilisateur | Votre identifiant de connexion | Votre connexion doit disposer de l’autorisation **CONTROL SERVER**. |
    | Mot de passe| Votre mot de passe | Votre mot de passe |
    | Propriétés de connexion| Sélectionnez **Chiffrer la connexion** et **Faire confiance au certificat du serveur** en fonction de votre environnement. | Choisissez les propriétés appropriées pour la connexion à votre serveur |

    ![nouvelle migration de données, sélectionner une source](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Sélectionnez la base de données à migrer de votre serveur source vers Azure SQL Database, puis cliquez sur **Suivant**. Pour ce didacticiel, il n’y a qu’une seule base de données.

6. Dans la page **Sélectionner une cible**, renseignez les valeurs demandées, puis cliquez sur **Connexion** :

    | Paramètre      | Valeur suggérée | Description | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nom du serveur | Nom complet de votre serveur Azure Database | Nom complet du serveur Azure Database de la procédure précédente |
    | Type d'authentification | l’authentification SQL Server | L’authentification SQL Server est la seule option disponible au moment de l’écriture de ce didacticiel. Toutefois, l’authentification intégrée Active Directory et l’authentification par mot de passe Active Directory sont également prises en charge par Azure SQL Database |
    | Nom d’utilisateur | Votre identifiant de connexion | Votre connexion doit disposer de l’autorisation **CONTROL DATABASE** pour la base de données source. |
    | Mot de passe| Votre mot de passe | Votre mot de passe |
    | Propriétés de connexion| Sélectionnez **Chiffrer la connexion** et **Faire confiance au certificat du serveur** en fonction de votre environnement. | Choisissez les propriétés appropriées pour la connexion à votre serveur |

    ![nouvelle migration de données, sélectionner une cible](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Sur le serveur cible, sélectionnez la base de données que vous avez créée dans une procédure précédente, puis cliquez sur **Suivant** pour démarrer le processus d’évaluation du schéma de la base de données source. Pour ce didacticiel, il n’y a qu’une seule base de données. Notez que le niveau de compatibilité pour cette base de données est défini sur 140, ce qui correspond au niveau de compatibilité par défaut de toutes les nouvelles bases de données dans Azure SQL Database.

   > [!IMPORTANT] 
   > Après avoir migré votre base de données vers Azure SQL Database, vous pouvez choisir d’utiliser la base de données à un niveau de compatibilité que vous spécifiez pour des raisons de compatibilité descendante. Pour plus d’informations sur les implications et les options du fonctionnement d’une base de données à un niveau de compatibilité spécifique, consultez [Niveau de compatibilité ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consultez également [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) pour plus d’informations sur des paramètres supplémentaires au niveau de la base de données relatifs aux niveaux de compatibilité.
   >

8. Dans la page **Sélectionner des objets**, une fois que le processus d’évaluation du schéma de la base de données source est terminé, passez en revue les objets sélectionnés pour la migration, ainsi que les objets qui contiennent des problèmes. Par exemple, examinez l’objet **dbo.uspSearchCandidateResumes** pour d’éventuels changements de comportement **SERVERPROPERTY('LCID')**, et l’objet **HumanResourcesJobCandidate** pour d’éventuels changements relatifs à la recherche en texte intégral. 

   > [!IMPORTANT] 
   > Selon la conception de la base de données et de l’application, lorsque vous migrez votre base de données source, vous devrez peut-être modifier la base de données ou l’application, ou les deux, après la migration (et, dans certains cas, avant la migration). Pour plus d’informations sur les différences Transact-SQL qui peuvent affecter la migration, consultez [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).

     ![évaluation de la nouvelle migration de données, sélection d’objets](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Cliquez sur **Générer un script SQL** pour générer un script à l’aide des objets de schéma de la base de données source. 
10. Examinez le script généré, puis cliquez sur **Problème suivant**, si nécessaire, pour passer en revue les problèmes d’évaluation identifiés et les recommandations fournies. Par exemple, pour la recherche en texte intégral, il est recommandé, lors d’une mise à niveau, de tester vos applications à l’aide des fonctionnalités de recherche en texte intégral. Vous pouvez enregistrer ou copier le script si vous le souhaitez.

     ![script généré pour la nouvelle migration de données](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Cliquez sur **Déployer le schéma** et surveillez le processus de migration du schéma.

     ![nouvelle migration de données, migration de schéma](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. Une fois la migration de schéma terminée, vérifiez qu’aucune erreur ne s’est produite et, en supposant que ce soit le cas, cliquez sur **Migrer les données**.
13. Dans la page **Sélectionner des tables**, examinez les tables sélectionnées pour la migration, puis cliquez sur **Démarrer la migration des données**.

     ![nouvelle migration de données, migration de données](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Surveillez le processus de migration.

     ![nouvelle migration de données, processus de migration des données](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

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

## <a name="change-database-properties"></a>Modifier les propriétés de base de données

Vous pouvez modifier le niveau de service, le niveau de performances et le niveau de compatibilité à l’aide de SQL Server Management Studio. Pendant la phase d’importation, nous recommandons d’importer dans une base de données de niveau supérieur pour bénéficier de performances optimales, mais que vous faites descendre en puissance une fois l’importation terminée afin d’économiser les coûts en attendant d’être prêt à utiliser activement la base de données importée. La modification du niveau de compatibilité peut offrir de meilleures performances et un accès aux fonctionnalités les plus récentes du service Azure SQL Database. Lorsque vous migrez une base de données ancienne, la compatibilité de celle-ci est maintenue au niveau pris en charge le plus bas compatible avec la base de données en cours d’importation. Pour plus d’informations, voir [Amélioration des performances des requêtes avec le niveau de compatibilité 130 dans Azure SQL Database](sql-database-compatibility-level-query-performance-130.md).

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête connectée à votre base de données s’ouvre.

2. Exécutez la commande suivante pour affecter au niveau de service la valeur **Standard** et au niveau de performances la valeur **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>étapes suivantes 
Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> * Créer une base de données SQL Azure vide dans le portail Azure 
> * Créer une règle de pare-feu au niveau du serveur dans le portail Azure 
> * Utiliser l’outil [DMA](https://www.microsoft.com/download/details.aspx?id=53595) (Data Migration Assistant) pour importer votre base de données SQL Server dans la base de données SQL Azure vide 
> * Utiliser l’outil [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio) pour modifier les propriétés de la base de données

Passez au didacticiel suivant pour apprendre à sécuriser votre base de données.

> [!div class="nextstepaction"]
> [Sécuriser votre base de données SQL Azure](sql-database-security-tutorial.md).


