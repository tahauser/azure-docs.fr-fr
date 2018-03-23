---
title: Code du fichier d’événements XEvent pour SQL Database | Microsoft Docs
description: Fournit PowerShell et Transact-SQL pour un exemple de code en deux phases qui montre l’utilisation de la cible du fichier d’événements dans un événement étendu sur Azure SQL Database. Le service Azure Storage est nécessaire pour ce scénario.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 10/05/2017
ms.author: genemi
ms.openlocfilehash: 630e0ca84479757d3bb4397aa63ee8a440e69427
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Code cible du fichier d’événements pour les événements étendus dans SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Vous pouvez utiliser un exemple de code complet pour capturer et signaler les informations liées à un événement étendu.

Dans Microsoft SQL Server, la [cible du fichier d’événements](http://msdn.microsoft.com/library/ff878115.aspx) est utilisée pour stocker les sorties d’événement dans un fichier sur un disque dur local. Mais ce type de fichier n’est pas disponible dans Azure SQL Database. À la place, nous utilisons le service Azure Storage pour prendre en charge la cible du fichier d’événements.

Cette rubrique présente un exemple de code en deux phases :

* PowerShell, pour créer un conteneur Azure Storage dans le cloud.
* Transact-SQL :
  
  * Pour affecter le conteneur Azure Storage à une cible du fichier d’événements.
  * Pour créer et démarrer la session d’événement, etc.

## <a name="prerequisites"></a>Prérequis


* Un compte et un abonnement Azure. Vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Une base de données dans laquelle vous pouvez créer une table.
  
  * Vous pouvez aussi [créer une base de données de démonstration **AdventureWorksLT**](sql-database-get-started.md) en quelques minutes.
* SQL Server Management Studio (ssms.exe), dans l’idéal, la version de sa dernière mise à jour mensuelle. 
  Vous pouvez télécharger la dernière version de ssms.exe :
  
  * À partir de la rubrique [Télécharger SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [En utilisant un lien direct vers le téléchargement.](http://go.microsoft.com/fwlink/?linkid=616025)
* Les [modules Azure PowerShell](http://go.microsoft.com/?linkid=9811175) doivent être installés.
  
  * Ces modules fournissent des commandes, telles que **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Phase 1 : code PowerShell pour le conteneur Azure Storage

Il s’agit de la première phase de l’exemple de code en deux phases.

Le script commence par des commandes à supprimer s’il a déjà été exécuté, et est réexécutable.

1. Collez le script PowerShell dans un éditeur de texte simple, tel que Notepad.exe, puis enregistrez-le dans un fichier avec l’extension **.ps1**.
2. Démarrez PowerShell ISE en tant qu’administrateur.
3. À l’invite, tapez <br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>et appuyez sur Entrée.
4. Dans PowerShell ISE, ouvrez votre fichier **.ps1** . Exécutez le script.
5. Tout d’abord, le script ouvre une nouvelle fenêtre pour vous permettre de vous connecter à Azure.
   
   * Si vous réexécutez le script sans interrompre votre session, vous avez la possibilité de commenter la commande **Add-AzureAccount** .

![PowerShell ISE, avec le module Azure installé, prêt à exécuter le script.][30_powershell_ise]

### <a name="powershell-code"></a>Code PowerShell

Ce script PowerShell suppose que vous avez déjà exécuté l’applet de commande Import-Module pour le module AzureRM. Pour consulter la documentation de référence, utilisez [l’explorateur de modules PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Add-AzureRmAccount (or  Login-AzureRmAccount), just one time.';
#Add-AzureRmAccount;   # Same as  Login-AzureRmAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'West US';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzureRmStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Prenez note des quelques valeurs nommées que le script PowerShell affiche à la fin de son exécution. Vous devrez modifier ces valeurs dans le script Transact-SQL lors de la phase 2 ci-après.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Phase 2 : code Transact-SQL utilisant le conteneur Azure Storage

* Lors de la phase 1 de cet exemple de code, vous avez exécuté un script PowerShell pour créer un conteneur Azure Storage.
* Dans la phase 2, le script Transact-SQL suivant doit utiliser ce conteneur.

Le script commence par des commandes à supprimer s’il a déjà été exécuté, et est réexécutable.

Le script PowerShell a affiché quelques valeurs nommées à la fin de son exécution. Vous devez modifier le script Transact-SQL pour utiliser ces valeurs. Recherchez les occurrences de **TODO** dans le script Transact-SQL pour trouver les parties du code à modifier.

1. Ouvrez SQL Server Management Studio (ssms.exe).
2. Connectez-vous à votre base de données Azure SQL Database.
3. Cliquez pour ouvrir un nouveau volet de requête.
4. Collez le script Transact-SQL suivant dans le volet de requête.
5. Recherchez chaque occurrence de **TODO** dans le script et effectuez les modifications appropriées.
6. Enregistrez le script, puis exécutez-le.


> [!WARNING]
> La valeur de clé SAS générée par le script PowerShell précédent pourrait commencer par un « ? » (point d’interrogation). Lorsque vous utilisez la clé SAP dans le script T-SQL suivant, vous devez *supprimer le point d’interrogation « ? » au début*. Dans le cas contraire, vos efforts peuvent être bloqués par la sécurité.


### <a name="transact-sql-code"></a>Code Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Si la liaison avec la cible n’a pas pu se faire au moment de l’exécution, vous devez arrêter et redémarrer la session d’événement :

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Sortie

Quand le script Transact-SQL a fini de s’exécuter, cliquez sur une cellule sous l’en-tête de colonne **event_data_XML**. Un élément **<event>** s’affiche, avec une instruction UPDATE.

Voici un élément **<event>** généré pendant le test :


```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```


Le script Transact-SQL précédent a utilisé la fonction système suivante pour lire le fichier d’événement :

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Une explication des options avancées de l’affichage des données d’événements étendus est disponible dans :

* [Affichage avancée des données cibles à partir d’événements étendus](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversion de l’exemple de code pour l’exécuter sur SQL Server

Vous voulez maintenant exécuter l’exemple de code Transact-SQL précédent sur Microsoft SQL Server.

* Par souci de simplicité, vous allez remplacer entièrement le conteneur Stockage Azure par un simple fichier tel que **C:\myeventdata.xel**. Le fichier doit être stocké sur le disque dur local de l’ordinateur qui héberge SQL Server.
* Vous n’avez pas besoin d’instructions Transact-SQL de type **CREATE MASTER KEY** et **CREATE CREDENTIAL**.
* Dans l’instruction **CREATE EVENT SESSION**, dans sa clause **ADD TARGET**, vous devez remplacer la valeur Http affectée à **filename=** par une chaîne de chemin d’accès complet comme **C:\myfile.xel**.
  
  * Vous n’avez pas besoin de compte Azure Storage.

## <a name="more-information"></a>Plus d’informations

Pour plus d’informations sur les comptes et les conteneurs du service Azure Storage, consultez :

* [Utilisation du stockage d’objets blob à partir de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Désignation et référencement des conteneurs, des objets BLOB et des métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Utilisation du conteneur racine](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Leçon 1 : Créer une stratégie d’accès stockée et une signature d’accès partagé sur un conteneur Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Leçon 2 : Créer des informations d’identification SQL Server à l’aide d’une signature d’accès partagé](http://msdn.microsoft.com/library/dn466435.aspx)
* [Événements étendus pour Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

