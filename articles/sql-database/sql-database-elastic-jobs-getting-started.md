---
title: "Prise en main des tâches de base de données élastiques | Microsoft Docs"
description: "Utilisez des tâches de base de données élastique pour exécuter des scripts T-SQL qui s’étendent sur plusieurs bases de données."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 2540de0e-2235-4cdd-9b6a-b841adba00e5
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: c5fe5256d4a8b8479f5e4aff95ec03fc1ba1018a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="getting-started-with-elastic-database-jobs"></a>Prise en main de Tâches de bases de données élastiques
Les tâches de base de données élastique (préversion) pour Azure SQL Database vous permettent d’exécuter, de manière efficace, des scripts T-SQL qui s’étendent sur plusieurs bases de données, tout en apportant automatiquement de nouvelles tentatives et des garanties d’achèvement final. Pour plus d’informations sur la fonctionnalité Tâches de base de données élastique, voir [Travaux élastiques](sql-database-elastic-jobs-overview.md).

Cet article développe l’exemple présenté dans [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md). Il explique comment créer et gérer des tâches qui gèrent un groupe de bases de données associées. Il n’est pas nécessaire d’utiliser les outils de mise à l’échelle élastique pour tirer parti des avantages des tâches élastiques.

## <a name="prerequisites"></a>Prérequis
Téléchargez et exécutez l’exemple de la rubrique [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Créez un gestionnaire des cartes de partitions à l’aide de l’exemple d’application
Vous allez maintenant créer un gestionnaire de cartes de partitions avec plusieurs partitions, puis insérer des données dans les partitions. Si vos partitions comportent déjà des données partitionnées, vous pouvez ignorer ces étapes et passer à la section suivante.

1. Créez et exécutez l’exemple d’application de la rubrique **Prise en main des outils de base de données élastique** . Suivez la procédure jusqu’à l’étape 7 dans la section [Télécharger et exécuter l’exemple d’application](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). À la fin de l’étape 7, vous verrez l’invite de commande suivante :

   ![invite de commande](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. Dans la fenêtre de commande, entrez « 1 » et appuyez sur **Entrée**. Cela crée le gestionnaire des cartes de partitions et ajoute deux partitions sur le serveur. Entrez « 3 », puis appuyez sur **Entrée**. Répétez cette action quatre fois. Cela permet d’insérer des lignes d’exemples de données dans vos partitions.
3. Le [portail Azure](https://portal.azure.com) doit alors montrer trois nouvelles bases de données :

   ![Confirmation Visual Studio](./media/sql-database-elastic-query-getting-started/portal.png)

   À ce stade, nous créons une collecte de base de données personnalisée reflétant toutes les bases de données dans la carte de partitions. Cela nous permet de créer et d’exécuter une tâche qui ajoute une nouvelle table aux partitions.

Dans ce cas précis, nous créons généralement une cible de carte de partitions, à l’aide de l’applet de commande **New-AzureSqlJobTarget** . La base de données du gestionnaire de cartes de partitions doit être définie en tant que base de données cible et la carte de partitions spécifique doit être spécifiée en tant que cible. Au lieu de cela, nous énumérerons toutes les bases de données du serveur et nous ajouterons les bases de données à la nouvelle collecte personnalisée, à l'exception de la base de données principale.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Crée une collecte personnalisée et ajoute toutes les bases de données du serveur à la cible de la collecte personnalisée, à l’exception de la base de données principale.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créez un script T-SQL pour l’exécuter sur des bases de données
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Créez la tâche pour exécuter un script sur le groupe de bases de données personnalisé

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Exécutez la tâche
Le script PowerShell suivant peut être utilisé pour exécuter une tâche existante :

Mettez à jour la variable suivante pour refléter le nom de la tâche souhaitée à exécuter :

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Récupérez l'état de l'exécution d'une tâche unique
Utilisez la même applet de commande **Get-AzureSqlJobExecution** avec le paramètre **IncludeChildren** pour afficher l’état de l’exécution des tâches enfants, à savoir l’état spécifique à chaque exécution de tâche sur chaque base de données ciblée par la tâche.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Afficher l’état sur plusieurs exécutions de tâches
L’applet de commande **Get-AzureSqlJobExecution** dispose de plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de tâches, filtrées selon les paramètres fournis. L'exemple suivant présente certaines façons d'utiliser Get-AzureSqlJobExecution :

Récupérez toutes les exécutions de tâches de niveau supérieur actives :

   ```
    Get-AzureSqlJobExecution
   ```

Récupérez toutes les exécutions de tâches de niveau supérieur, y compris les exécutions de tâches inactives :

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Récupérez toutes les exécutions de tâches enfants d'un ID d'exécution de tâches fourni, y compris les exécutions de tâches inactives :

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Récupérez toutes les exécutions de tâches créées à l'aide d'une planification/combinaison de tâches, y compris les tâches inactives :

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Récupérez toutes les tâches ciblant une carte de partitions spécifiée, y compris les tâches inactives :

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Récupérez toutes les tâches ciblant une collecte personnalisée spécifiée, y compris les tâches inactives :

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Récupérez la liste des exécutions de tâches de travail dans l'exécution d'une tâche spécifique :

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Récupérez les détails d’exécution de tâches de travail :

Le script PowerShell suivant peut être utilisé pour afficher les détails d'une exécution de tâches de travail, qui est particulièrement utile lors du débogage des échecs d'exécution.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Récupérez des échecs dans les exécutions de tâches de travail
L'objet JobTaskExecution inclut une propriété pour le cycle de vie de la tâche, ainsi qu’une propriété Message. Si une exécution de tâches de travail a échoué, la propriété du cycle de vie est définie sur *Échec* et la propriété de message est définie sur le message d’exception résultant et sa pile. Si une tâche a échoué, il est important d’afficher les détails des tâches de travail qui n'ont pas abouti pour une tâche donnée.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>En attente d’une exécution de tâche à effectuer
Le script PowerShell suivant peut être utilisé pour attendre l’exécution d’une tâche :

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Créez une stratégie d'exécution personnalisée
Tâches de bases de données prend en charge la création de stratégies d'exécution personnalisées qui peuvent être appliquées lors du démarrage des tâches.

Les stratégies d'exécution permettent de définir :

* Le nom : l'identificateur de la stratégie d'exécution.
* Délai d’attente de la tâche : délai avant l’annulation d’une tâche par Tâches de base de données élastique.
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

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Mettez à jour une stratégie d'exécution personnalisée
Mettez à jour la stratégie d'exécution souhaitée :

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Annulation d’une tâche
La fonctionnalité Tâches de bases de données élastiques prend en charge les demandes d’annulation de tâches.  Si la fonctionnalité Tâches de base de données élastique détecte une demande d’annulation d’une tâche en cours d’exécution, elle tente d’arrêter la tâche.

La fonctionnalité Tâches de bases de données élastiques peut effectuer une annulation de deux manières différentes :

1. Annulation des tâches en cours d’exécution : si une annulation est détectée pendant qu’une tâche est en cours d’exécution, l’annulation est tentée au sein de l’étape de la tâche en cours d’exécution.  Par exemple : si une requête de longue durée s’exécute quand une annulation est tentée, une tentative d’annulation de la requête est effectuée.
2. Annuler des tentatives de tâches : si une annulation est détectée par le thread de contrôle avant de lancer l’exécution d’une tâche, le thread de contrôle évite le lancement de la tâche et déclare la requête comme étant annulée.

Si une annulation de tâche est demandée pour une tâche parente, la demande d’annulation est honorée pour la tâche parente et toutes ses tâches enfants.

Pour envoyer une demande d’annulation, utilisez l’applet de commande **Stop-AzureSqlJobExecution** et définissez le paramètre **JobExecutionId**.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Supprimez une tâche via son nom et l'historique de la tâche
Tâches de bases de données élastiques prend en charge la suppression des tâches asynchrone. Une tâche peut être marquée pour suppression. Le système supprime alors la tâche et son historique une fois toutes les exécutions de tâches accomplies. Le système n’annule pas automatiquement les exécutions de tâches actives.  

Au lieu de cela, Stop-AzureSqlJobExecution doit être appelé pour annuler les exécutions de tâches actives.

Pour déclencher la suppression de tâches, utilisez l’applet de commande **Remove-AzureSqlJob** et définissez le paramètre **JobName**.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Création d’une cible de base de données personnalisée
Les cibles de base de données personnalisées peuvent être définies dans Tâches de bases de données élastiques, qui peut être utilisé directement pour l'exécution ou l’inclusion dans un groupe de base de données personnalisé. Dans la mesure où les **pools élastiques** ne sont pas encore pris en charge directement via les API PowerShell, vous créez simplement une cible de base de données personnalisée et une cible de collecte de base de données personnalisée qui englobe toutes les bases de données dans le pool.

Définissez les variables suivantes pour refléter les informations de base de données souhaitées :

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Créez une cible de collecte de base de données personnalisée
Une cible de collecte de base de données personnalisée peut être définie pour permettre l'exécution sur plusieurs cibles de base de données définies. Une fois le groupe de base de données créé, les bases de données peuvent être associées à la cible de collecte personnalisée.

Définissez les variables suivantes pour refléter la configuration de la cible de collecte personnalisée souhaitée :

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Ajoutez des bases de données pour une cible de collecte de base de données personnalisée
Les cibles de base de données peuvent être associées à des cibles de collecte de base de données personnalisées pour créer un groupe de bases de données. Chaque fois qu’une tâche, visant une cible de collecte de base de données personnalisée, est créée, elle est développée afin de cibler les bases de données associées au groupe durant l’exécution.

Ajoutez la base de données souhaitée pour une collecte personnalisée :

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Examinez les bases de données dans une cible de collecte de base de données personnalisée
Utilisez l’applet de commande **Get-AzureSqlJobTarget** pour récupérer les bases de données enfants dans une cible de collecte de base de données personnalisée.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créez une tâche pour exécuter un script sur une cible de collecte de base de données personnalisée
Utilisez l’applet de commande **New-AzureSqlJob** pour créer une tâche sur un groupe de bases de données défini par une cible de collecte de base de données. La fonctionnalité Tâches de base de données élastique développe la tâche en plusieurs tâches enfants correspondant chacune à une base de données associée à la cible de la collecte de base de données personnalisée et s’assure que le script est exécuté sur chaque base de données. Encore une fois, il est important que les scripts soient idempotents pour résister à de nouvelles tentatives.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Collecte de données sur les bases de données
**Tâches de bases de données élastiques** prend en charge l'exécution d'une requête sur un groupe de bases de données et envoie les résultats de la table d’une base de données spécifiée. Le tableau peut être interrogé une fois les résultats de la requête affichés à partir de chaque base de données. Ceci fournit un mécanisme asynchrone, permettant d’exécuter une requête sur plusieurs bases de données. Les cas d'échec, notamment l’indisponibilité temporaire d’une des bases de données, sont gérés automatiquement par le biais de tentatives.

La table de destination spécifiée est automatiquement créée s’il n’existe pas encore de table correspondant au schéma du jeu de résultats retourné. Si l’exécution d’un script retourne plusieurs jeux de résultats, la fonctionnalité Tâches de base de données élastique envoie uniquement le premier à la table de destination fournie.

Le script PowerShell suivant peut être utilisé pour exécuter un script qui collecte ses résultats dans une table spécifiée. Ce script part du principe qu'un script T-SQL, qui génère un jeu de résultats unique, et une cible de collecte de base de données personnalisée ont été créés.

Définissez les valeurs suivantes pour refléter le script, les informations d'identification et les cibles d'exécution souhaités :

   ```
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
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Créez et démarrez une tâche pour les scénarios de collecte de données
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Créez une planification pour l'exécution de tâches à l'aide d'un déclencheur de tâches
Le script PowerShell suivant peut être utilisé pour créer une planification récurrente. Ce script utilise un intervalle d’une minute, mais New-AzureSqlJobSchedule prend également en charge les paramètres -DayInterval, - HourInterval, - MonthInterval et - WeekInterval. Les planifications qui ne s'exécutent qu'une seule fois peuvent être créées en transmettant  - OneTime.

Créez une nouvelle planification :
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Créez un déclencheur de tâches pour une tâche exécutée selon un calendrier
Un déclencheur de tâches peut être défini pour un travail exécuté selon un calendrier. Le script PowerShell suivant peut être utilisé pour créer un déclencheur de tâches.

Définissez les variables suivantes pour qu’elles correspondent à la tâche et à la planification souhaitées :

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Supprimez une association planifiée pour arrêter une tâche depuis l'exécution de la planification
Pour interrompre l'exécution d’une tâche récurrente via un déclencheur de tâches, le déclencheur de tâches peut être supprimé.
Supprimez un déclencheur de tâches pour arrêter une tâche qui s'exécute selon une planification à l'aide de l’applet de commande **Remove-AzureSqlJobTrigger** .

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importez les résultats de la requête de base de données élastique dans Excel
 Vous pouvez importer les résultats d’une requête vers un fichier Excel.

1. Lancez Excel 2013.
2. Accédez au ruban **Données** .
3. Cliquez sur **À partir d’autres sources** et sur **À partir de SQL Server**.

   ![Importation au format Excel à partir d’autres sources](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. Dans l’ **Assistant de connexion de données** saisissez le nom du serveur et les informations de connexion. Cliquez ensuite sur **Suivant**.
5. Dans la boîte de dialogue **Sélectionner la base de données qui contient les données que vous souhaitez**, sélectionnez la base de données **ElasticDBQuery**.
6. Sélectionnez la table **Clients** dans la liste et cliquez sur **Suivant**. Puis, cliquez sur **Terminer**.
7. Dans le formulaire **Importer des données**, sous **Sélectionner le mode d’affichage des données dans votre classeur**, sélectionnez **Table** et cliquez sur **OK**.

Toutes les lignes de la table **Clients** , stockées dans des partitions différentes, remplissent la feuille Excel.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant utiliser les fonctions de données Excel. Utilisez la chaîne de connexion avec votre nom de serveur, votre nom de base de données et les informations d’identification pour connecter vos outils d’intégration BI et de données à la base de données de requête élastique. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Traitez la base de données de requête élastique et les tables externes comme n’importe quelles bases de données SQL Server et tables SQL Server auxquelles vous vous connectez avec votre outil.

### <a name="cost"></a>Coût
La fonction de requête de base de données élastique n’entraîne pas de frais supplémentaires. Toutefois, pour l’instant, cette fonctionnalité n’est disponible que sur les bases de données premium comme point de terminaison, mais les partitions peuvent provenir de n’importe quel niveau de service.

Pour plus d’informations sur la tarification, consultez la page [Tarification - Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
