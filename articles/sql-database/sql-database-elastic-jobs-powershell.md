---
title: Création et gestion de tâches élastiques à l’aide de PowerShell | Microsoft Docs
description: Gérer la base de données SQL Azure avec PowerShell
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 17e4176129da747925596c66ca9df936a3828c2d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Création et gestion de tâches de bases de données SQL élastiques à l’aide de PowerShell (version préliminaire)

Les API PowerShell pour les **tâches de bases de données élastiques** (en version préliminaire) vous permettent de définir quels scripts seront exécutés pour quel groupe de bases de données. Cet article vous montre comment créer et gérer des **tâches de bases de données élastiques** à l’aide de PowerShell. Voir [Vue d’ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Pour obtenir un essai gratuit, voir [Version d'évaluation d'un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un ensemble de bases de données créé avec les outils de bases de données élastiques. Voir [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **tâches de bases de données élastiques** : voir [Installing tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Sélectionner votre abonnement Azure
Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement (**-SubscriptionId**) ou de votre nom d’abonnement (**-SubscriptionName**). Si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats. Une fois que vous disposez de vos informations d’abonnement, exécutez l’applet de commande pour définir l’abonnement en tant qu’abonnement par défaut, à savoir, la cible pour créer et gérer des tâches :

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

Il est recommandé d’utiliser [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) pour développer et exécuter des scripts PowerShell sur les tâches de bases de données élastiques.

## <a name="elastic-database-jobs-objects"></a>Objets des tâches de bases de données élastiques
Le tableau suivant répertorie les types d’objets des **tâches de bases de données élastiques** ainsi que leur description et les API PowerShell correspondantes.

<table style="width:100%">
  <tr>
    <th>Type d'objet</th>
    <th>Description</th>
    <th>API PowerShell correspondantes</th>
  </tr>
  <tr>
    <td>Informations d'identification</td>
    <td>Nom d'utilisateur et mot de passe à utiliser lors de la connexion aux bases de données pour l'exécution de scripts ou l'application d’un DACPAC. <p>Le mot de passe est chiffré avant l’envoi et le stockage dans la base de données des tâches de bases de données élastiques.  Le mot de passe est déchiffré par le service des tâches de bases de données élastiques via les informations d’identification créées et téléchargées à partir du script d’installation.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL à utiliser pour l’exécution dans les bases de données.  Le script doit être créé pour être idempotent puisque le service réessaiera l’exécution du script en cas de défaillances.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Package d’application de la couche Données </a> à appliquer dans l’ensemble des bases de données.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Base de données cible</td>
    <td>Nom de la base de données et du serveur pointant vers une base de données Azure SQL.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Carte de partitions cible</td>
    <td>Combinaison d’une cible de base de données et d’une information d’identification à utiliser pour déterminer les informations stockées dans une carte de partitions de base de données élastique.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cible de la collection personnalisée</td>
    <td>Groupe défini de bases de données à utiliser ensemble pour l’exécution.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cible enfant de la collection personnalisée</td>
    <td>Cible de la base de données qui est référencée à partir d'une collection personnalisée.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Travail</td>
    <td>
    <p>Définition des paramètres d'une tâche qui peut être utilisée pour déclencher l'exécution ou pour répondre à une planification.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Exécution des tâches</td>
    <td>
    <p>Conteneur de tâches nécessaire pour pouvoir exécuter un script ou appliquer un DACPAC à une cible à l’aide des informations d’identification pour les connexions de bases de données avec des défaillances gérées suivant une stratégie d’exécution.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Exécution d’une tâche</td>
    <td>
    <p>Unité de travail unique pour effectuer une tâche.</p>
    <p>Si une tâche ne parvient pas à s’exécuter correctement, le message d’exception qui en résulte sera journalisé et une nouvelle tâche correspondante sera créée et exécutée suivant la stratégie d’exécution indiquée.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Stratégie d’exécution des tâches</td>
    <td>
    <p>Contrôle les délais d’expiration d’une tâche, les limites du nombre de tentatives et les intervalles entre les tentatives.</p>
    <p>Les tâches de bases de données incluent une stratégie d’exécution de tâche par défaut qui entraîne essentiellement un nombre infini de nouvelles tentatives de défaillances de tâches avec une interruption exponentielle des intervalles entre chaque tentative.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Planification</td>
    <td>
    <p>Indication temporelle pour que l’exécution ait lieu à un intervalle récurrent ou à un moment précis.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Déclencheur de tâches</td>
    <td>
    <p>Un mappage entre une tâche et une planification pour déclencher l’exécution de la tâche suivant la planification.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Types de groupe des tâches de bases de données élastiques prises en charge
La tâche exécute les scripts Transact-SQL (T-SQL) ou l’application de DACPAC sur un groupe de bases de données. Lorsqu’une tâche est soumise pour être exécutée sur un groupe de bases de données, la tâche « se développe » dans des tâches enfants dans lesquelles chaque tâche effectue l’exécution requise sur une seule base de données du groupe. 

Vous pouvez créer deux types de groupes : 

* [Carte de partitions](sql-database-elastic-scale-shard-map-management.md) : lorsqu’une tâche est soumise pour cibler une carte de partitions, la tâche interroge d’abord la carte de partitions afin de déterminer l’ensemble de ses partitions actuelles, puis crée les tâches enfants pour chaque partition de la carte de partitions.
* Groupe Collection personnalisée : un ensemble défini de bases de données personnalisées. Lorsqu’une tâche cible une collection personnalisée, elle crée des tâches enfants pour chaque base de données actuellement présente dans la collection personnalisée.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Définir la connexion des tâches de bases de données élastiques
Une connexion doit être définie pour la *base de données de contrôle* des tâches avant d’utiliser les API des tâches. L’exécution de cet applet de commande affiche une fenêtre d’informations d’identification demandant le nom d’utilisateur et le mot de passe créés lors de l’installation des tâches de bases de données élastiques. Tous les exemples fournis dans cette rubrique partent du principe que cette première étape a déjà été effectuée.

Ouvrez une connexion des tâches de bases de données élastiques :

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Informations d'identification chiffrées dans les tâches de bases de données élastiques
Les informations d’identification de la base de données peuvent être insérées dans la *base de données de contrôle* des tâches avec un mot de passe chiffré. Il est nécessaire de stocker les informations d’identification pour activer des tâches à exécuter ultérieurement (à l’aide de planifications de tâches).

Le chiffrement utilise un certificat créé dans le cadre du script d'installation. Le script d'installation crée et télécharge le certificat dans le Azure Cloud Service pour le déchiffrement des mots de passe chiffrés stockés. Azure Cloud Service stocke ultérieurement la clé publique dans la *base de données de contrôle* des tâches de bases de données, ce qui permet à l’interface de l’API PowerShell ou du portail Azure de chiffrer un mot de passe fourni sans que l’installation locale du certificat ne soit requise.

Les mots de passe des informations d’identification sont chiffrés et sécurisés auprès des utilisateurs ayant un accès en lecture seule aux objets des tâches de bases de données élastiques. Mais un utilisateur malveillant bénéficiant d’un accès en lecture-écriture aux objets de bases de données élastiques aura toujours la possibilité d’extraire un mot de passe. Les informations d’identification sont conçues pour être réutilisées lors des exécutions de tâches. Les informations d'identification sont transmises aux bases de données cibles lors de l'établissement des connexions. Il n’y a actuellement aucune restriction sur les bases de données cibles utilisées pour chaque information d’identification ; un utilisateur malveillant pourrait donc ajouter une cible de base de données pour une base de données dont il a le contrôle. L’utilisateur pourrait ensuite démarrer une tâche ciblant cette base de données afin d’obtenir le mot de passe associé aux informations d’identification.

Voici quelques bonnes pratiques de sécurité pour les tâches de bases de données élastiques :

* Limitez l'utilisation des API aux personnes de confiance.
* Les informations d'identification doivent avoir les privilèges minimaux requis pour effectuer la tâche.  Pour plus d’informations, consultez l’article MSDN [Autorisations](https://msdn.microsoft.com/library/bb669084.aspx) sur SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Créer des informations d’identification chiffrées pour l’exécution d’une tâche dans des bases de données
Pour créer de nouvelles informations d’identification chiffrées, [**l’applet de commande Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) demande un nom d’utilisateur et un mot de passe qui peuvent être transmis à [**l’applet de commande New-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Mettre à jour les informations d’identification
En cas de modification des mots de passe, utilisez [**l’applet de commande Set-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) et définissez le paramètre **CredentialName**.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Définir une cible de carte de partitions de base de données élastique
Pour exécuter une tâche sur toutes les bases de données dans un ensemble de partitions (créé à l’aide de la [bibliothèque cliente de la base de données élastique](sql-database-elastic-database-client-library.md)), utilisez une carte de partitions en tant que cible de base de données. Cet exemple requiert une application partitionnée créée à l’aide de la bibliothèque cliente de la base de données élastique. Voir [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

La base de données du gestionnaire de cartes de partitions doit être définie en tant que base de données cible et la carte de partitions spécifique doit être spécifiée en tant que cible.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créez un script T-SQL pour l’exécuter sur des bases de données
Lorsque vous créez des scripts T-SQL pour l’exécution, il est fortement recommandé de les créer pour garantir une [idempotence](https://en.wikipedia.org/wiki/Idempotence) et une résistance aux défaillances. Les tâches de bases de données élastiques vont tenter à nouveau l’exécution d’un script chaque fois que l’exécution rencontre une défaillance, quel que soit le type de défaillance.

Utilisez [**l’applet de commande New-AzureSqlJobContent**](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) pour créer et enregistrer un script pour l’exécution, puis définissez les paramètres **-ContentName** et **-CommandText**.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Créer un script à partir d'un fichier
Si le script T-SQL est défini dans un fichier, utilisez ce qui suit pour importer le script :

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Mettre à jour un script T-SQL en vue d’une exécution sur des bases de données
Ce script PowerShell met à jour le texte de commande T-SQL d’un script existant.

Définissez les variables suivantes pour refléter la définition de script souhaitée à définir :

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Mettre à jour la définition d’un script existant
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Créer une tâche pour exécuter un script sur une carte de partitions
Ce script PowerShell démarre une tâche pour l’exécution d’un script sur chaque partition dans la carte de partitions d’une infrastructure élastique.

Définissez les variables suivantes pour refléter le script et la cible souhaités :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Exécuter une tâche
Ce script PowerShell exécute une tâche existante :

Mettez à jour la variable suivante pour refléter le nom de la tâche souhaitée à exécuter :

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Récupérer l’état de l’exécution d’une tâche unique
Utilisez [**l’applet de commande Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) et définissez le paramètre **JobExecutionId** pour afficher l’état de l’exécution de la tâche.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilisez la même applet de commande **Get-AzureSqlJobExecution** avec le paramètre **IncludeChildren** pour afficher l’état de l’exécution des tâches enfants, à savoir l’état spécifique à chaque exécution de tâche sur chaque base de données ciblée par la tâche.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Afficher l’état sur plusieurs exécutions de tâches
L’applet de commande [**Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/new-azuresqljob) dispose de plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de tâches, filtrées selon les paramètres fournis. L'exemple suivant présente certaines façons d'utiliser Get-AzureSqlJobExecution :

Récupérez toutes les exécutions de tâches de niveau supérieur actives :

    Get-AzureSqlJobExecution

Récupérez toutes les exécutions de tâches de niveau supérieur, y compris les exécutions de tâches inactives :

    Get-AzureSqlJobExecution -IncludeInactive

Récupérez toutes les exécutions de tâches enfants d'un ID d'exécution de tâches fourni, y compris les exécutions de tâches inactives :

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Récupérez toutes les exécutions de tâches créées à l'aide d'une planification/combinaison de tâches, y compris les tâches inactives :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Récupérez toutes les tâches ciblant une carte de partitions spécifiée, y compris les tâches inactives :

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Récupérez toutes les tâches ciblant une collecte personnalisée spécifiée, y compris les tâches inactives :

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Récupérez la liste des exécutions de tâches de travail dans l'exécution d'une tâche spécifique :

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Récupérez les détails d’exécution de tâches de travail :

Le script PowerShell suivant peut être utilisé pour afficher les détails d'une exécution de tâches de travail, qui est particulièrement utile lors du débogage des échecs d'exécution.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Récupérer des échecs dans les exécutions de tâches de travail
L’ **objet JobTaskExecution** inclut une propriété pour le cycle de vie de la tâche, ainsi qu’une propriété de message. Si une exécution de tâches de travail a échoué, la propriété du cycle de vie est définie sur *Échec* et la propriété de message est définie sur le message d’exception résultant et sa pile. Si une tâche a échoué, il est important d’afficher les détails des tâches de travail qui n'ont pas abouti pour une tâche donnée.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Attendre la fin de l’exécution d’une tâche
Le script PowerShell suivant peut être utilisé pour attendre l’exécution d’une tâche :

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Créez une stratégie d'exécution personnalisée
Tâches de bases de données prend en charge la création de stratégies d'exécution personnalisées qui peuvent être appliquées lors du démarrage des tâches.

Les stratégies d'exécution permettent de définir :

* Le nom : l'identificateur de la stratégie d'exécution.
* Délai d’attente de la tâche : délai avant l’annulation d’une tâche par Tâches de bases de données élastiques.
* Intervalle avant nouvelle tentative initiale : l'intervalle d'attente avant la première nouvelle tentative.
* Intervalle maximal avant nouvelle tentative : plafond des intervalles avant nouvelle tentative à utiliser.
* Coefficient d'interruption de l’intervalle avant nouvelle tentative : ce coefficient permet de calculer le prochain intervalle entre les tentatives.  La formule suivante est utilisée : (intervalle avant nouvelle tentative initiale) * Math.pow (coefficient d’interruption de l’intervalle), (nombre de tentatives) - 2). 
* Nombre maximal de tentatives : le nombre maximal de nouvelles tentatives effectuées dans une tâche.

La stratégie d'exécution par défaut utilise les valeurs suivantes :

* Le nom : la stratégie d'exécution par défaut
* Délai d’attente de la tâche : 1 semaine
* Intervalle avant nouvelle tentative initiale : 100 millisecondes
* Intervalle maximal avant nouvelle tentative : 30 minutes
* Coefficient de l'intervalle avant nouvelle tentative : 2
* Nombre maximal de tentatives : 2 147 483 647

Créez la stratégie d'exécution souhaitée :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Mettez à jour une stratégie d'exécution personnalisée
Mettez à jour la stratégie d'exécution souhaitée :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Annulation d’une tâche
La fonctionnalité Tâches de bases de données élastiques prend en charge les demandes d'annulation de tâches.  Si la fonctionnalité Tâches de bases de données élastiques détecte une demande d'annulation d'une tâche en cours d'exécution, il tente d'arrêter la tâche.

La fonctionnalité Tâches de bases de données élastiques peut effectuer une annulation de deux manières différentes :

1. Annuler des tâches en cours d’exécution : si une annulation est détectée pendant qu’une tâche est en cours d’exécution, l’annulation sera tentée au sein de l’aspect de la tâche en cours d’exécution.  Par exemple : si une requête de longue durée s’exécute lorsqu'une annulation est tentée, une tentative d'annulation de la requête sera effectuée.
2. Annuler des tentatives de tâches : si une annulation est détectée par le thread de contrôle avant de lancer l’exécution d’une tâche, le thread de contrôle permettra d’éviter le lancement de la tâche et de déclarer la requête comme étant annulée.

Si une annulation de tâche est demandée pour une tâche parente, la demande d'annulation sera respectée pour la tâche parente et toutes ses tâches enfants.

Pour envoyer une demande d’annulation, utilisez [**l’applet de commande Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) et définissez le paramètre **JobExecutionId**.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Supprimer une tâche et l’historique des tâches en mode asynchrone
Tâches de bases de données élastiques prend en charge la suppression des tâches asynchrone. La suppression d’une tâche peut être signalée et le système supprime la tâche et son historique une fois que toutes les exécutions de tâches auront été effectuées. Le système n'annule pas automatiquement les exécutions de tâches actives.  

Appelez [**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) pour annuler les exécutions de tâches actives.

Pour déclencher la suppression de tâches, utilisez [**l’applet de commande Remove-AzureSqlJob**](/powershell/module/elasticdatabasejobs/remove-azuresqljob) et définissez le paramètre **JobName**.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Créer une cible de base de données personnalisée
Vous pouvez définir des cibles de bases de données personnalisées pour une exécution directe ou pour une inclusion dans un groupe de bases de données personnalisées. Par exemple, puisque les **pools élastiques** ne sont pas encore pris en charge directement via les API PowerShell, vous pouvez créer simplement une cible de base de données personnalisée et une cible de collecte de base de données personnalisée qui englobe toutes les bases de données dans le pool.

Définissez les variables suivantes pour refléter les informations de base de données souhaitées :

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Créer une cible de collecte de bases de données personnalisées
Utilisez l’applet de commande [**New-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) pour définir une cible de collecte de bases de données personnalisée afin de permettre l’exécution sur plusieurs cibles de bases de données définies. Une fois le groupe de base de données créé, les bases de données peuvent être associées à la cible de collecte personnalisée.

Définissez les variables suivantes pour refléter la configuration de la cible de collecte personnalisée souhaitée :

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Ajouter des bases de données à une cible de collecte de base de données personnalisée
Pour ajouter une base de données à une collecte personnalisée spécifique, utilisez l’applet de commande [**Add-AzureSqlJobChildTarget**](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget).

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Examinez les bases de données dans une cible de collecte de base de données personnalisée
Utilisez l’applet de commande [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) pour récupérer les bases de données enfants dans une cible de collecte de base de données personnalisée. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créez une tâche pour exécuter un script sur une cible de collecte de base de données personnalisée
Utilisez l’applet de commande [**New-AzureSqlJob**](/powershell/module/elasticdatabasejobs/new-azuresqljob) pour créer une tâche sur un groupe de bases de données défini par une cible de collecte de base de données. La fonctionnalité Tâches de bases de données élastiques étendra la tâche en plusieurs tâches enfants correspondant chacune à une base de données associée à la cible de la collecte de base de données personnalisée et s’assurera que le script est exécuté sur chaque base de données. Encore une fois, il est important que les scripts soient idempotents pour résister à de nouvelles tentatives.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Collecte de données sur les bases de données
Vous pouvez utiliser une tâche pour exécuter une requête sur un groupe de bases de données et envoyer les résultats à une table spécifique. Le tableau peut être interrogé une fois les résultats de la requête affichés à partir de chaque base de données. Ceci fournit un mécanisme asynchrone permettant l’exécution d’une requête sur plusieurs bases de données. Les tentatives ayant échoué sont gérées automatiquement par l’exécution de nouvelles tentatives.

La table de destination spécifiée sera automatiquement créée si elle n’existe pas encore. La nouvelle table correspond au schéma du jeu de résultats retourné. Si l’exécution d’un script retourne plusieurs jeux de résultats, les tâches de bases de données élastiques enverront uniquement le premier jeu à la table de destination.

Le script PowerShell suivant exécute un script et collecte ses résultats dans une table spécifiée. Ce script part du principe qu’un script T-SQL, qui génère un jeu de résultats unique, et une cible de collecte de base de données personnalisée ont été créés.

Ce script utilise l’applet de commande [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget). Définissez les paramètres de script, les informations d’identification et la cible d’exécution :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Créer et démarrer une tâche pour les scénarios de collecte de données
Ce script utilise l’applet de commande [**Start-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution).

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Planifier un déclencheur d’exécution de tâche
Le script PowerShell suivant peut être utilisé pour créer une planification récurrente. Ce script utilise un intervalle de minutes, mais [**New-AzureSqlJobSchedule**](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) prend également en charge les paramètres -DayInterval, - HourInterval, - MonthInterval et - WeekInterval. Les planifications qui ne s'exécutent qu'une seule fois peuvent être créées en transmettant  - OneTime.

Créez une nouvelle planification :

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Déclencher une tâche exécutée selon un calendrier
Un déclencheur de tâches peut être défini pour un travail exécuté selon un calendrier. Le script PowerShell suivant peut être utilisé pour créer un déclencheur de tâches.

Utilisez [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) et définissez les variables suivantes pour qu’elles correspondent à la tâche et à la planification souhaitées :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Supprimer une association planifiée pour arrêter l’exécution planifiée d’une tâche
Pour interrompre l'exécution d’une tâche récurrente via un déclencheur de tâches, le déclencheur de tâches peut être supprimé. Supprimez un déclencheur de tâches pour arrêter une tâche qui s’exécute selon une planification à l’aide de l’applet de commande [**Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Récupérer des déclencheurs de tâche liés à un calendrier
Le script PowerShell suivant peut être utilisé pour obtenir et afficher les déclencheurs de tâche enregistrés dans un calendrier particulier.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Récupérer des déclencheurs de tâche liés à une tâche
Utilisez [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) pour obtenir et afficher les planifications contenant une tâche inscrite.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Créer une application de la couche Données (DACPAC) pour une exécution dans des bases de données
Pour créer un DACPAC, voir [Applications de la couche Données](https://msdn.microsoft.com/library/ee210546.aspx). Pour déployer un DACPAC, utilisez l’ [applet de commande New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). Le fichier DACPAC doit être accessible au service. Il est recommandé de charger un DACPAC créé dans Azure Storage et de créer une [signature d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md) pour ce DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Mettre à jour une application de la couche Données (DACPAC) pour une exécution dans des bases de données
Les DACPAC existants enregistrés dans des tâches de bases de données élastiques peuvent être mises à jour pour pointer vers de nouveaux URI. Utilisez [**l’applet de commande Set-AzureSqlJobContentDefinition**](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) pour mettre à jour l’URI de DACPAC sur un DACPAC enregistré existant :

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Créer une tâche pour appliquer une application de la couche Données (DACPAC) dans des bases de données
Après la création d’un DACPAC dans les tâches de bases de données élastiques, une tâche peut être créée pour appliquer le fichier DACPAC dans un groupe de bases de données. Le script PowerShell suivant peut être utilisé pour créer une tâche DACPAC dans une collection de bases de données personnalisées :

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
