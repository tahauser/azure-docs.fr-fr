---
title: Configurer Azure SQL Data Sync (Préversion) | Microsoft Docs
description: Ce didacticiel vous montre comment configurer Azure SQL Data Sync (Préversion).
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 237a34c955f8ef36e25c30a6b13787f6a9296612
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-sql-data-sync-preview"></a>Configurer SQL Data Sync (Préversion)
Dans ce didacticiel, vous allez apprendre à configurer Azure SQL Data Sync en créant un groupe de synchronisation hybride contenant des instances SQL Database et SQL Server. Ce nouveau groupe de synchronisation est entièrement configuré et synchronise sur la planification définie.

Ce didacticiel suppose que vous ayez déjà utilisé SQL Database et SQL Server. 

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync (Préversion)](sql-database-sync-data.md).

Pour obtenir des exemples PowerShell complets qui montrent comment configurer SQL Data Sync, consultez les articles suivants :
-   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Étape 1 : créer un groupe de synchronisation

### <a name="locate-the-data-sync-settings"></a>Localiser les paramètres de synchronisation des données

1.  Dans votre navigateur, accédez au portail Azure.

2.  Dans le portail, localisez vos bases de données SQL à partir de votre tableau de bord ou de l’icône des bases de données SQL dans la barre d’outils.

    ![Liste des bases de données SQL Azure](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Dans la page **Bases de données SQL**, sélectionnez la base de données SQL que vous souhaitez utiliser comme base de données Hub pour Data Sync. La page Base de données SQL s’ouvre.

4.  Dans la page Base de données SQL de la base de données choisie, sélectionnez **Synchroniser avec les autres bases de données**. La page Synchronisation des données s’ouvre.

    ![Option Synchroniser avec les autres bases de données](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Créer un groupe de synchronisation

1.  Dans la page Synchronisation des données, sélectionnez **Nouveau groupe de synchronisation**. La page **Nouveau groupe de synchronisation** s’ouvre à l’étape 1 **Créer un groupe de synchronisation**. La page **Créer un groupe de synchronisation de données** s’ouvre également.

2.  Dans la page **Créer un groupe de synchronisation de données**, effectuez les étapes suivantes :

    1.  Dans le champ **Nom du groupe de synchronisation**, entrez le nom du nouveau groupe de synchronisation.

    2.  Dans la section **Synchroniser la base de données de métadonnées**, choisissez entre créer une base de données (recommandé) ou utiliser une base de données existante.

        > [!NOTE]
        > Microsoft recommande de créer une base de données vide à utiliser comme base de métadonnées de synchronisation. SQL Data Sync crée les tables dans cette base de données et exécute une charge de travail fréquente. Cette base de données est automatiquement partagée comme base des métadonnées de synchronisation pour l’ensemble de vos groupes de synchronisation dans la région sélectionnée. Vous ne pouvez pas modifier la base des métadonnées de synchronisation ou son nom sans la supprimer.

        Si vous avez choisi **Nouvelle base de données**, sélectionnez **Créer une nouvelle base de données.** La page **Base de données SQL** s’ouvre. Dans la page **Base de données SQL**, nommez et configurez la nouvelle base de données. Sélectionnez ensuite **OK**.

        Si vous avez choisi **Utiliser une base de données existante**, sélectionnez la base de données dans la liste.

    3.  Dans la section **Synchronisation automatique**, sélectionnez d’abord **On (Activé)** ou **Off (Désactivé)**.

        Si vous avez choisi **On (Activé)**, dans la section **Fréquence de synchronisation**, entrez un nombre et sélectionnez les secondes, les minutes, les heures ou les jours.

        ![Spécifier la fréquence de synchronisation](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  Dans la section **Résolution des conflits**, sélectionnez « Priorité au hub » ou « Member wins (Priorité au membre ».

        ![Spécifier le mode de résolution des conflits](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Sélectionnez **OK** et attendez que le nouveau groupe de synchronisation soit créé et déployé.

## <a name="step-2---add-sync-members"></a>Étape 2 : ajouter des membres de synchronisation

Une fois le nouveau groupe de synchronisation créé et déployé, l’étape 2 **Ajouter des membres de synchronisation** apparaît en surbrillance dans la page **Nouveau groupe de synchronisation**.

Dans la section **Base de données Hub**, entrez les informations d’identification pour le serveur SQL Database qui héberge la base de données Hub. N’entrez pas de *nouvelles* informations d’identification dans cette section.

![Base de données Hub ajoutée au groupe de synchronisation](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Ajouter une base de données SQL Azure Database

Dans la section **Base de données membre**, ajoutez éventuellement une base de données Azure SQL Database au groupe de synchronisation en sélectionnant **Ajouter une base de données Azure**. La page **Configurer une base de données Azure** s’ouvre.

Dans la page **Configurer une base de données Azure**, effectuez les étapes suivantes :

1.  Dans le champ **Nom du membre de synchronisation**, indiquez le nom du nouveau membre de synchronisation. Ce nom est différent du nom de la base de données.

2.  Dans le champ **Abonnement**, sélectionnez l’abonnement Azure associé en vue de la facturation.

3.  Dans le champ **Azure SQL Server**, sélectionnez le serveur de base de données SQL.

4.  Dans le champ **Azure SQL Database**, sélectionnez la base de données SQL.

5.  Dans le champ **Sens de la synchronisation**, sélectionnez Synchronisation bidirectionnelle, To the hub (Vers le hub) ou From the hub (À partir du hub).

    ![Ajout d’un nouveau membre de synchronisation SQL Database](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  Dans les champs **Nom d’utilisateur** et **Mot de passe**, entrez les informations d’identification du serveur SQL Database qui héberge la base de données membre. N’entrez pas de *nouvelles* informations d’identification dans cette section.

7.  Sélectionnez **OK** et attendez que le nouveau membre de synchronisation soit créé et déployé.

    ![Nouveau membre de synchronisation SQL Database ajouté](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Ajouter une base de données SQL Server locale

Dans la section **Base de données membre**, ajoutez éventuellement un serveur SQL local au groupe de synchronisation en sélectionnant **Ajouter une base de données locale**. La page **Configurer localement** s’ouvre.

Dans la page **Configurer localement**, effectuez les étapes suivantes :

1.  Sélectionnez **Choisir la passerelle de l’agent de synchronisation**. La page **Sélectionner l’agent de synchronisation** s’ouvre.

    ![Choisir la passerelle de l’agent de synchronisation](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Dans la page **Choisir la passerelle de l’agent de synchronisation**, faites votre choix entre utiliser un agent existant ou en créer un.

    Si vous avez choisi **Agents existants**, sélectionnez un agent dans la liste.

    Si vous avez choisi **Créer un agent**, procédez comme suit :

    1.  Téléchargez le logiciel de l’agent de synchronisation de client à l’aide du lien fourni et installez-le sur l’ordinateur qui héberge le serveur SQL Server.
 
        > [!IMPORTANT]
        > Vous devez ouvrir le port TCP sortant 1433 dans le pare-feu pour permettre à l’agent du client de communiquer avec le serveur.


    2.  Entrez le nom de l’agent.

    3.  Sélectionnez **Créer et générer une clé**.

    4.  Copiez la clé de l’agent dans le Presse-papiers.
        
        ![Création d’un agent de synchronisation](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Sélectionnez **OK** pour fermer la page **Sélectionner l’agent de synchronisation**.

    6.  Sur l’ordinateur du serveur SQL Server, recherchez et exécutez l’application de l’agent de synchronisation du client.

        ![Application de l’agent de synchronisation du client](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  Dans l’application de l’agent de synchronisation, sélectionnez **Submit Agent Key (Envoyer la clé de l’agent)**. La boîte de dialogue **Sync Metadata Database Configuration (Configuration de la base des métadonnées de synchronisation)** s’ouvre.

    8.  Dans la boîte de dialogue **Sync Metadata Database Configuration (Configuration de la base des métadonnées de synchronisation)**, collez la clé de l’agent copiée à partir du portail Azure. Indiquez également les informations d’identification du serveur Azure SQL Database qui héberge la base de données des métadonnées. (Si vous avez créé une base de métadonnées, celle-ci réside sur le même serveur que la base de données Hub.) Sélectionnez **OK** et attendez la fin de la configuration.

        ![Entrer la clé de l’agent et les informations d’identification du serveur](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Si vous recevez une erreur de pare-feu à ce stade, vous devez créer une règle de pare-feu sur Windows Azure pour autoriser le trafic entrant en provenance de l’ordinateur qui héberge le serveur SQL Server. Vous pouvez créer cette règle manuellement dans le portail, mais il peut être plus facile de la créer dans SQL Server Management Studio (SSMS). Dans SSMS, essayez de vous connecter à la base de données Hub sur Azure. Entrez son nom sous la forme \<nom_base_données_hub\>.database.windows.net. Pour configurer la règle de pare-feu Azure, suivez les étapes décrites dans la boîte de dialogue. Revenez ensuite dans l’application de l’agent de synchronisation du client.

    9.  Dans l’application de l’agent de synchronisation du client, cliquez sur **Inscrire** pour inscrire une base de données SQL Server dans l’agent. La boîte de dialogue **Configuration de SQL Server** s’ouvre.

        ![Ajouter et configurer une base de données SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. Dans la boîte de dialogue **Configuration de SQL Server**, choisissez entre l’authentification SQL Server et l’authentification Windows. Si vous avez choisi l’authentification SQL Server, entrez les informations d’identification. Indiquez le nom du serveur SQL Server et le nom de la base de données que vous voulez synchroniser. Sélectionnez **Tester la connexion** pour tester vos paramètres. Ensuite, sélectionnez **Enregistrer**. La base de données inscrite apparaît dans la liste.

        ![La base de données SQL Server est maintenant inscrite.](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Vous pouvez fermer l’application de l’agent de synchronisation du client.

    12. Dans le portail, dans la apge **Configurer localement**, choisissez **Sélectionner la base de données**. La page **Sélectionner la base de données** s’ouvre.

    13. Dans la page **Sélectionner la base de données**, indiquez le nom du nouveau membre de synchronisation dans le champ **Nom du membre de synchronisation**. Ce nom est différent du nom de la base de données. Sélectionnez la base de données dans la liste. Dans le champ **Sens de la synchronisation**, sélectionnez Synchronisation bidirectionnelle, To the hub (Vers le hub) ou From the hub (À partir du hub).

        ![Sélectionner la base de données locale](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Sélectionnez **OK** pour fermer la page **Sélectionner la base de données**. Ensuite, sélectionnez **OK** pour fermer la page **Configurer localement** et attendez que le nouveau membre de synchronisation soit créé et déployé. Pour finir, cliquez sur **OK** pour fermer la page **Sélectionner les membres de synchronisation**.

        ![Base de données locale ajoutée au groupe de synchronisation](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Pour vous connecter à SQL Data Sync et à l’agent local, ajoutez votre nom d’utilisateur au rôle `DataSync_Executor`. Data Sync crée ce rôle sur l’instance SQL Server.

## <a name="step-3---configure-sync-group"></a>Étape 3 : configurer le groupe de synchronisation

Une fois les nouveaux membres du groupe de synchronisation créés et déployés, l’étape 3 **Configurer le groupe de synchronisation** apparaît en surbrillance dans la page **Nouveau groupe de synchronisation**.

1.  Dans la page **Tables**, sélectionnez une base de données dans la liste des membres du groupe de synchronisation, puis sélectionnez **Actualiser le schéma**.

2.  Dans la liste des tables disponibles, sélectionnez celles que vous voulez synchroniser.

    ![Sélectionner les tables à synchroniser](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Par défaut, toutes les colonnes de la table sont sélectionnées. Si vous ne souhaitez pas synchroniser toutes les colonnes, désactivez la case à cocher de celles à exclure de la synchronisation. Vérifiez que la colonne de la clé primaire est sélectionnée.

    ![Sélectionner les champs à synchroniser](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Enfin, sélectionnez **Enregistrer**.

## <a name="faq-about-setup-and-configuration"></a>FAQ sur l’installation et la configuration

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>À quelle fréquence Data Sync synchronise-t-il mes données ? 
Il effectue une synchronisation au minimum toutes les cinq minutes.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL Data Sync gère-t-il intégralement la création et le provisionnement des tables ?

Si les tables de schéma de synchronisation ne sont pas déjà créées dans la base de données de destination, SQL Data Sync (Préversion) les crée avec les colonnes que vous avez sélectionnées. Toutefois, ce comportement ne permet pas la création d’un schéma haute fidélité, pour les raisons suivantes :

-   Seules les colonnes que vous avez sélectionnées sont créées dans la table de destination. Si certaines colonnes dans les tables source ne font pas partie du groupe de synchronisation, elles ne sont pas provisionnées dans les tables de destination.

-   Des index sont créés uniquement pour les colonnes sélectionnées. Si l’index de la table source a des colonnes qui ne font pas partie du groupe de synchronisation, ces index ne sont pas provisionnés dans les tables de destination.

-   Les index sur des colonnes de type XML ne sont pas provisionnés.

-   Les contraintes CHECK ne sont pas provisionnées.

-   Les déclencheurs existants sur les tables sources ne sont pas provisionnés.

-   Les vues et procédures stockées ne sont pas créées sur la base de données de destination.

En raison de ces limitations, nous vous recommandons les opérations suivantes :
-   Pour les environnements de production, provisionnez vous-même le schéma haute fidélité.
-   Pour tester le service, la fonctionnalité de provisionnement automatique de SQL Data Sync (Préversion) fonctionne bien.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Pourquoi puis-je voir des tables que je n’ai pas créées ?  
Data Sync crée des tables latérales dans votre base de données utilisateur pour le suivi des modifications. Ne les supprimez pas, sinon Data Sync cessera de fonctionner.

### <a name="is-my-data-convergent-after-a-sync"></a>Mes données convergent-elles après une synchronisation ?

Pas nécessairement. Dans un groupe de synchronisation comprenant un concentrateur et trois rayons (A, B et C), les synchronisations sont les suivantes : Concentrateur vers A, Concentrateur vers B et Concentrateur vers C. Si une modification est apportée à la base de données A *après* la synchronisation Concentrateur vers A, cette modification sera prise en compte dans la base de données B ou C après la prochaine tâche de synchronisation.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Comment obtenir des modifications de schéma dans un groupe de synchronisation ?

Vous devez effectuer les modifications de schéma manuellement.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Comment puis-je exporter et importer une base de données avec Data Sync ?
Après avoir exporté une base de données sous forme de fichier `.bacpac` et avoir importé le fichier pour créer une base de données, vous devez effectuer les deux tâches suivantes pour utiliser Data Sync dans la nouvelle base de données :
1.  Nettoyer les objets Data Sync et les tables annexes de la **nouvelle base de données** à l’aide de [ce script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Ce script supprime tous les objets Data Sync requis de la base de données.
2.  Recréer le groupe de synchronisation avec la nouvelle base de données. Si vous n’avez plus besoin de l’ancien groupe de synchronisation, supprimez-le.

## <a name="faq-about-the-client-agent"></a>FAQ concernant l’agent client

### <a name="why-do-i-need-a-client-agent"></a>Pourquoi ai-je besoin d’un agent client ?

Le service SQL Data Sync (Préversion) communique avec les bases de données SQL Server via l’agent client. Cette fonctionnalité de sécurité empêche la communication directe avec les bases de données derrière un pare-feu. Lorsque le service SQL Data Sync (Préversion) communique avec l’agent, il utilise des connexions chiffrées et un jeton unique ou *clé d’agent*. Les bases de données SQL Server authentifient l’agent à l’aide de la chaîne de connexion et de la clé d’agent. Cette méthode assure un haut niveau de sécurité pour vos données.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Combien d’instances de l’interface utilisateur de l’agent local peuvent être exécutées ?

Une seule instance de l’interface utilisateur peut être exécutée.

### <a name="how-can-i-change-my-service-account"></a>Comment faire pour modifier mon compte de service ?

Après avoir installé un agent client, la seule façon de modifier le compte de service consiste à le désinstaller et à installer un nouvel agent client avec le nouveau compte de service.

### <a name="how-do-i-change-my-agent-key"></a>Comment faire pour modifier ma clé d’agent ?

Une clé d’agent ne peut être utilisée qu’une seule fois par un agent. Elle ne peut pas être réutilisée lorsque vous supprimez puis réinstallez un nouvel agent, et ne peut pas non plus être utilisée par plusieurs agents. Si vous avez besoin de créer une nouvelle clé pour un agent existant, vous devez vous assurer que la même clé est enregistrée auprès de l’agent client et du service SQL Data Sync (Préversion).

### <a name="how-do-i-retire-a-client-agent"></a>Comment mettre hors service un agent client ?

Pour invalider ou mettre hors service immédiatement un agent, régénérez sa clé dans le portail, mais ne l’envoyez pas dans l’interface utilisateur de l’agent. La régénération d’une clé invalide la clé précédente, peu importe que l’agent correspondant soit en ligne ou hors connexion.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Comment déplacer un agent client vers un autre ordinateur ?

Si vous souhaitez exécuter l’agent local à partir d’un autre ordinateur, procédez comme suit :

1. Installez l’agent sur l’ordinateur souhaité.

2. Connectez-vous au portail SQL Data Sync (Préversion) et régénérez une clé d’agent pour le nouvel agent.

3. Utilisez l’interface utilisateur du nouvel agent pour envoyer la nouvelle clé d’agent.

4. Patientez pendant que l’agent client télécharge la liste des bases de données locales qui ont été enregistrées précédemment.

5. Indiquez les informations d’identification de base de données pour toutes les bases de données apparaissant comme inaccessibles. Ces bases de données doivent être accessibles depuis le nouvel ordinateur sur lequel l’agent est installé.

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Vous avez créé un groupe de synchronisation incluant une instance de base de données SQL et une base de données SQL Server.

Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md)
-   [Bonnes pratiques pour Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Surveiller Azure SQL Data Sync avec OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Résoudre les problèmes liés à Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
