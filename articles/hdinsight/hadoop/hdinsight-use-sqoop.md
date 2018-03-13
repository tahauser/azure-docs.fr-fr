---
title: "Exécuter des tâches Apache Sqoop avec Azure HDInsight (Hadoop) | Microsoft Docs"
description: "Découvrez comment utiliser Azure PowerShell à partir d'un poste de travail pour exécuter des commandes Sqoop import et export entre un cluster HDInsight et une base de données SQL Azure."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 2fdcc6b7-6ad5-4397-a30b-e7e389b66c7a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: e2f4601daf9aa3537f9170c3516d62ab1bd602e5
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="use-sqoop-with-hadoop-in-hdinsight"></a>Utilisation de Sqoop avec Hadoop dans HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop dans HDInsight pour effectuer des opérations d’importation et d’exportation entre un cluster HDInsight et la base de données SQL Azure ou SQL Server.

Bien que Hadoop soit préférable pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut être également nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

[Sqoop][sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre les clusters Hadoop et les bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQL Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL Server comme base de données relationnelle.

Pour obtenir la liste des versions Sqoop prises en charge par les clusters HDInsight, consultez la rubrique [Quels sont les composants et versions Hadoop disponibles avec HDInsight ?][hdinsight-versions]

## <a name="understand-the-scenario"></a>Présentation du scénario

Le cluster HDInsight inclut des exemples de données. Vous utilisez les deux éléments suivants :

* Un fichier journal log4j situé sous */example/data/sample.log*. Ce fichier inclut les journaux suivants :
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Une table Hive appelée *hivesampletable*, qui référence le fichier de données situé sous */hive/warehouse/hivesampletable*. Cette table contient des données relatives aux appareils mobiles. 
  
  | Champ | Type de données |
  | --- | --- |
  | clientid |chaîne |
  | querytime |chaîne |
  | market |chaîne |
  | deviceplatform |chaîne |
  | devicemake |chaîne |
  | devicemodel |chaîne |
  | state |chaîne |
  | country |chaîne |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

Dans ce didacticiel, vous utilisez ces deux jeux de données pour tester l’importation et l’exportation Sqoop.

## <a name="create-cluster-and-sql-database"></a>Création du cluster et de la base de données SQL
Cette section vous montre comment créer un cluster, une base de données SQL Database et les schémas de base de données SQL pour exécuter le didacticiel à l’aide du portail Azure et d’un modèle Azure Resource Manager. Ce modèle se trouve dans les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Le modèle Resource Manager appelle un package bacpac pour déployer les schémas de table vers la base de données SQL Database.  Le package bacpac est disponible dans un conteneur d’objets blob public : https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Si vous souhaitez utiliser un conteneur privé pour stocker les fichiers bacpac, appliquez les valeurs suivantes au modèle :
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Si vous préférez utiliser Azure PowerShell pour créer le cluster et la base de données SQL Database, consultez l’[Annexe A](#appendix-a---a-powershell-sample).

> [!NOTE]
> L’importation à l’aide d’un modèle ou du portail Azure ne prend en charge que l’importation d’un fichier BACPAC à partir du stockage Blob Azure.

**Pour configurer l’environnement à l’aide d’un modèle de gestion des ressources**
1. Cliquez sur l’image suivante pour ouvrir un modèle Resource Manager dans le portail Azure.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Entrez les propriétés suivantes :

    - **Abonnement** : indiquez votre abonnement Azure.
    - **Groupe de ressources** : créez un groupe de ressources Azure ou sélectionnez un groupe de ressources existant.  Un groupe de ressources est destiné à la gestion.  C’est un conteneur d’objets.
    - **Emplacement** : sélectionnez une région.
    - **Nom de cluster** : saisissez le nom du cluster Hadoop.
    - **Nom d’utilisateur et mot de passe de cluster**: le nom de connexion par défaut est admin.
    - **Nom d’utilisateur et mot de passe SSH**.
    - **Nom et mot de passe de connexion au serveur de base de données SQL**.
    - **_artifacts Location** (Emplacement _artifacts) : utilisez la valeur par défaut, sauf si vous souhaitez utiliser votre propre fichier backpac à un emplacement différent.
    - **_artifacts Location Sas Token** (Jeton SAP d’emplacement _artifacts) : laissez ce champ vide.
    - **Bacpac File Name** (Nom du fichier bacpac) : utilisez la valeur par défaut, sauf si vous souhaitez utiliser votre propre fichier backpac.
     
        Les valeurs suivantes sont codées en dur dans la section des variables :
        
        |NOM|Valeur|
        |----|-----|
        | Nom du compte de stockage par défaut | &lt;CluterName>store |
        | Nom du serveur de base de données SQL Azure. | &lt;ClusterName>dbserver |
        | Nom de la base de données SQL Azure | &lt;ClusterName>db |
     
3. **J’accepte les termes et conditions mentionnés ci-dessus**.
4. Cliquez sur **Achat**. Une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle s’affiche. La création du cluster et de la base de données SQL prend environ 20 minutes.

Si vous choisissez d’utiliser une base de données SQL Azure ou Microsoft SQL Server existante

* **Base de données SQL Azure**: vous devez configurer une règle de pare-feu pour le serveur de base de données SQL Azure afin d’autoriser l'accès depuis votre station de travail. Pour des instructions sur la création d’une base de données SQL Azure et la configuration d’un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Azure][sqldatabase-get-started]. 
  
  > [!NOTE]
  > Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels qu’Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l’activer depuis le portail Azure. Pour obtenir des instructions sur la création d’une base de données SQL Azure et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d’une base de données SQL][sqldatabase-create-configue].
  > 
  > 
* **SQL Server**: si votre cluster HDInsight se trouve sur le même réseau virtuel que SQL Server dans Azure, vous pouvez utiliser les étapes décrites dans cet article pour importer et exporter des données vers une base de données SQL Server.
  
  > [!NOTE]
  > HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.
  > 
  > 
  
  * Pour créer et configurer un réseau virtuel, consultez [Créer un réseau virtuel au moyen du portail Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    
    * Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.
      
      > [!NOTE]
      > Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l’application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.
      > 
      > 
    * Lorsque vous utilisez SQL Server sur une machine virtuelle Azure, toute configuration du réseau virtuel peut être utilisée si la machine virtuelle qui héberge SQL Server est membre du même réseau virtuel que HDInsight.
  * Pour créer un cluster HDInsight sur un réseau virtuel, consultez la rubrique [Création de clusters Hadoop dans HDInsight à l’aide d’options personnalisées](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]
    > SQL Server doit également autoriser l'authentification. Vous devez utiliser une connexion SQL Server pour compléter les étapes décrites dans cet article.
    > 
    > 

**Pour valider la configuration**

1. Ouvrez le groupe de ressources dans le portail Azure. Le groupe contient normalement quatre ressources :

    - le cluster
    - le serveur de base de données
    - la base de données
    - le compte de stockage par défaut

2. Ouvrez la base de données dans Microsoft SQL Server Management Studio.  Deux bases de données y sont normalement déployées :

    ![Azure HDInsight Sqoop SQL Management Studio](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Exécuter des tâches Sqoop
HDInsight peut exécuter des tâches Sqoop à l’aide de différentes méthodes. Utilisez la table suivante pour choisir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Utilisez-le** si vous souhaitez... | ... un interpréteur de commandes **interactif** | ... un traitement par **lots** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Kit de développement logiciel (SDK) .NET pour Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (pour l’instant) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |

## <a name="limitations"></a>Limites
* Exportation en bloc : avec HDInsight sous Linux, le connecteur Sqoop utilisé pour exporter des données vers Microsoft SQL Server ou la base de données SQL Azure ne prend pas en charge les insertions en bloc.
* Traitement par lots : avec HDInsight sous Linux, lorsque vous utilisez le commutateur `-batch` pour effectuer des insertions, Sqoop effectue plusieurs insertions plutôt qu’un traitement par lots des opérations d’insertion.

## <a name="next-steps"></a>Étapes suivantes
Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

* [Utilisation de Hive avec HDInsight](../hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](../hdinsight-use-pig.md)
* [Chargement de données vers HDInsight][hdinsight-upload-data] : découvrez d’autres méthodes pour charger des données vers HDInsight ou Stockage Blob Azure.

## <a name="appendix-a---a-powershell-sample"></a>Annexe A - Exemple PowerShell
L’exemple PowerShell effectue les étapes suivantes :

1. Connexion à Azure.
2. Création d’un groupe de ressources Azure. Pour en savoir plus, voir [Utilisation d’Azure PowerShell avec le Gestionnaire de ressources Azure](../../azure-resource-manager/powershell-azure-resource-manager.md)
3. Création d’un serveur Base de données SQL Azure, d’une base de données SQL Azure et de deux tables. 
   
    Si vous utilisez SQL Server à la place, utilisez les instructions suivantes pour créer les tables :
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Le moyen le plus simple d’examiner la base de données et les tables consiste à utiliser Visual Studio. Le serveur de base de données et la base de données peuvent être examinés à l’aide du portail Azure.
4. Créez un cluster HDInsight.
   
    Pour examiner le cluster, vous pouvez utiliser le portail Azure ou Azure PowerShell.
5. Prétraitez le fichier de données source.
   
    Ce tutoriel explique comment exporter un fichier journal log4j (fichier délimité) et une table Hive vers une base de données SQL Azure. Le fichier délimité s’appelle */example/data/sample.log*. Vous trouverez plus haut dans ce didacticiel quelques exemples de journaux log4j. Certaines lignes du fichier journal sont vides et d'autres ressemblent à ce qui suit :
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Cela convient pour d'autres exemples qui utilisent ces données, mais nous devons supprimer ces exceptions pour pouvoir importer le fichier dans la base de données SQL Azure ou SQL Server. Notez que l’exportation Sqoop échoue s’il y a une chaîne ou une ligne vide contenant moins d’éléments que le nombre de champs définis dans la table de la base de données SQL Azure. La table log4jlogs contient sept champs de type chaîne.
   
    Cette procédure crée un fichier sur le cluster : tutorials/usesqoop/data/sample.log. Pour examiner le fichier de données modifiées, vous pouvez utiliser le portail Azure, un outil d’exploration Azure Storage, ou Azure PowerShell. Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l’utilisation d’Azure PowerShell pour télécharger un fichier et afficher son contenu.
6. Exportez un fichier de données vers la base de données SQL Azure.
   
    Le fichier source est tutorials/usesqoop/data/sample.log. La table vers laquelle les données sont exportées est nommée log4jlogs.
   
   > [!NOTE]
   > En dehors des informations de la chaîne de connexion, les étapes décrites dans cette section doivent fonctionner pour une base de données SQL Azure ou pour SQL Server. Elles ont été testées avec la configuration suivante :
   > 
   > * **Configuration de point à site du réseau virtuel Azure**: un réseau virtuel connectant le cluster HDInsight à un serveur SQL Server dans un centre de données privé. Pour plus d'informations, consultez la page [Configuration d'un réseau privé virtuel (VPN) de point à site dans le portail de gestion](../../vpn-gateway/vpn-gateway-point-to-site-create.md) .
   > * **Azure HDInsight** : pour plus d’informations sur la création d’un cluster sur un réseau virtuel, consultez la rubrique [Création de clusters Hadoop dans HDInsight à l’aide d’options personnalisées](../hdinsight-hadoop-provision-linux-clusters.md).
   > * **SQL Server 2014**: configuré de manière à autoriser l'authentification et à exécuter le package de configuration du client VPN pour établir une connexion sécurisée au réseau virtuel.
   > 
   > 
7. Exportez une table Hive vers la base de données SQL Azure.
8. Importez la table mobiledata dans le cluster HDInsight.
   
    Pour examiner le fichier de données modifiées, vous pouvez utiliser le portail Azure, un outil d’exploration Azure Storage, ou Azure PowerShell.  Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l’utilisation d’Azure PowerShell pour télécharger un fichier et afficher son contenu.

### <a name="the-powershell-sample"></a>Exemple PowerShell

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "http://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

#region - Create Azure resouce group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch{
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzureRmSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzureRmSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzureRMSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzureStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzureRmHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzureRmHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzureRmHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzureRmHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
