---
title: "Migrer vers les outils de développement Azure Resource Manager pour HDInsight | Microsoft Docs"
description: "Procédure de migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 8ce1d6300731af5ae972675a08ef64f5c4ffa342
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight

HDInsight déconseille les outils Azure Service Manager (ASM) pour HDInsight. Si vous avez utilisé Azure PowerShell, l’interface de ligne de commande Azure ou le SDK .NET HDInsight pour travailler avec les clusters HDInsight, nous vous invitons désormais à utiliser les versions Azure Resource Manager (ARM) de PowerShell, de l’interface de ligne de commande et du SDK .NET. Cet article fournit des repères sur la façon de migrer vers la nouvelle approche ARM. Cet article souligne également, le cas échéant, les différences entre les approches ASM et ARM pour HDInsight.

> [!IMPORTANT]
> La prise en charge de PowerShell, de l’interface de ligne de commande et du SDK .NET ASM sera interrompue le **1er janvier 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Utilisation de l’interface de ligne de commande Azure vers Azure Resource Manager
L’interface de ligne de commande Azure est maintenant en mode Azure Resource Manager (ARM) par défaut, sauf si vous mettez à niveau à partir d’une installation précédente. Dans ce cas, vous devrez peut-être utiliser la commande `azure config mode arm` pour passer en mode ARM.

Les commandes de base que l’interface de ligne de commande Azure a fournies pour fonctionner avec HDInsight à l’aide d’Azure Service Management (ASM) sont identiques lors de l’utilisation d’ARM. Toutefois, certains paramètres et commutateurs peuvent avoir des nouveaux noms, et de nombreux nouveaux paramètres sont disponibles lorsque vous utilisez ARM. Par exemple, vous pouvez maintenant utiliser `azure hdinsight cluster create` pour spécifier le réseau virtuel Azure dans lequel un cluster devrait être créé, ou des informations sur les metastores Hive et Oozie.

Les commandes de base pour travailler avec HDInsight via le Azure Resource Manager sont :

* `azure hdinsight cluster create` - crée un nouveau cluster HDInsight
* `azure hdinsight cluster delete` - supprime un cluster HDInsight existant
* `azure hdinsight cluster show` -affiche des informations sur un cluster existant
* `azure hdinsight cluster list` - répertorie les clusters HDInsight pour votre abonnement Azure

Utilisez le commutateur `-h` pour inspecter les paramètres et les commutateurs disponibles pour chaque commande.

### <a name="new-commands"></a>Nouvelles commandes
Les nouvelles commandes disponibles avec Azure Resource Manager sont :

* `azure hdinsight cluster resize` -modifie de manière dynamique le nombre de nœuds worker dans le cluster
* `azure hdinsight cluster enable-http-access` - active l’accès HTTPs au cluster (activé par défaut)
* `azure hdinsight cluster disable-http-access` - désactive l’accès HTTPs au cluster
* `azure hdinsight script-action` - fournit des commandes pour créer/gérer des actions de script sur un cluster
* `azure hdinsight config` - fournit des commandes pour créer un fichier de configuration qui peut être utilisé avec la commande `hdinsight cluster create` pour fournir des informations de configuration.

### <a name="deprecated-commands"></a>Commandes déconseillées
Si vous utilisez les commandes `azure hdinsight job` pour envoyer des travaux vers votre cluster HDInsight, ceux-ci ne sont pas disponibles via les commandes ARM. Si vous devez envoyer des travaux par programme vers HDInsight à partir de scripts, vous devez à la place utiliser les API REST fournies par HDInsight. Pour plus d’informations sur l’envoi de travaux à l’aide des API REST, consultez les documents suivants.

* [Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Exécution de requêtes Hive avec Hadoop dans HDInsight via Curl](hadoop/apache-hadoop-use-hive-curl.md)
* [Exécution à distance des tâches Pig avec Hadoop sur HDInsight à l’aide de Curl](hadoop/apache-hadoop-use-pig-curl.md)

Pour plus d’informations sur d’autres méthodes d’exécution interactive de MapReduce, Hive et Pig, consultez [Utilisation de MapReduce avec Hadoop sur HDInsight](hadoop/hdinsight-use-mapreduce.md), [Utilisation de Hive avec Hadoop dans HDInsight](hadoop/hdinsight-use-hive.md) et [Utilisation de Pig avec Hadoop sur HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemples
**Création d’un cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nouvelle commande (ARM) - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Suppression d’un cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster delete myhdicluster`
* Nouvelle commande (ARM) - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Énumérer les clusters**

* Ancienne commande (ASM) - `azure hdinsight cluster list`
* Nouvelle commande (ARM) - `azure hdinsight cluster list`

> [!NOTE]
> Pour la commande list, le fait de spécifier le groupe de ressources à l’aide de `-g` renvoie uniquement les clusters dans le groupe de ressources spécifié.
> 
> 

**Afficher les informations du cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster show myhdicluster`
* Nouvelle commande (ARM) - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migration d’Azure PowerShell vers Azure Resource Manager
Les informations générales concernant Azure PowerShell en mode Azure Resource Manager (ARM) sont disponibles dans la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

Les applets de commande Azure PowerShell ARM peuvent être installées côte à côte avec les applets de commande ASM. Les applets de commande des deux modes se distinguent par leurs noms.  Le mode ARM a *AzureRmHDInsight* dans les noms d’applets de commande au lieu de *AzureHDInsight* en mode ASM.  Par exemple, *New-AzureRmHDInsightCluster* et *New-AzureHDInsightCluster*. Toutefois, certains paramètres et commutateurs peuvent avoir des nouveaux noms, et de nombreux nouveaux paramètres sont disponibles lorsque vous utilisez ARM.  Par exemple, plusieurs applets de commande exigent un nouveau commutateur appelé *-ResourceGroupName*. 

Avant de pouvoir utiliser les applets de commande HDInsight, vous devez vous connecter à votre compte Azure et créer un nouveau groupe de ressources :

* Login-AzureRmAccount ou [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Applets de commande renommées
Pour répertorier les applets de commande HDInsight ASM dans la console Windows PowerShell :

    help *azurermhdinsight*

Le tableau suivant répertorie les applets de commande ASM et leurs noms en mode ARM :

| Applets de commande ASM | Applets de commande ARM |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Nouvelles applets de commande
Les nouvelles applets de commande suivantes sont uniquement disponibles en mode ARM. 

**Applets de commandes associées à une action de script :**

* **Get-AzureRmHDInsightPersistedScriptAction**: obtient les actions de script persistantes pour un cluster et les répertorie par ordre chronologique ou obtient des informations pour une action de script persistante spécifiée. 
* **Get-AzureRmHDInsightScriptActionHistory**: obtient l’historique d’actions de script pour un cluster et les répertorie par ordre chronologique inversé, ou obtient des informations d’une action de script exécutée précédemment. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: retire une action de script persistante d’un cluster HDInsight.
* **Set-AzureRmHDInsightPersistedScriptAction**: définit une action de script exécutée précédemment comme action de script persistante.
* **Submit-AzureRmHDInsightScriptAction**: envoie une nouvelle action de script vers un cluster Azure HDInsight. 

Pour plus d’informations sur l’utilisation, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

**Applets de commande associées à l’identité du cluster :**

* **Add-AzureRmHDInsightClusterIdentity**: ajoute une identité de cluster à un objet configuration de cluster pour que le cluster HDInsight puisse accéder à Azure Data Lake Store. Consultez la page [Créer un cluster HDInsight avec Data Lake Store à l’aide d’Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemples
**Créer un cluster**

Ancienne commande (ASM) : 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nouvelle commande (ARM) :

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Supprimer un cluster**

Ancienne commande (ASM) :

    Remove-AzureHDInsightCluster -name $clusterName 

Nouvelle commande (ARM) :

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Répertorier un cluster**

Ancienne commande (ASM) :

    Get-AzureHDInsightCluster

Nouvelle commande (ARM) :

    Get-AzureRmHDInsightCluster 

**Afficher le cluster**

Ancienne commande (ASM) :

    Get-AzureHDInsightCluster -Name $clusterName

Nouvelle commande (ARM) :

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Autres exemples
* [Création de clusters HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Envoi de tâches Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Envoi de tâches Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Envoi de tâches Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Migration vers le Kit de développement logiciel (SDK) .NET HDInsight ARM
Le [Kit de développement logiciel (SDK) .NET HDInsight (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) Azure Service Management est maintenant déconseillé. Nous vous invitons à utiliser le [Kit de développement logiciel (SDK) .NET HDInsight (ARM)](https://msdn.microsoft.com/library/azure/mt271028.aspx)Azure Resource Management. Les packages HDInsight ASM suivants sont désormais déconseillés.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Cette section fournit des liens vers des informations supplémentaires sur la façon d’effectuer certaines tâches à l’aide du Kit de développement logiciel (SDK) ARM.

| Procédure d’utilisation du Kit de développement logiciel (SDK) .NET HDInsight ARM | Liens |
| --- | --- |
| Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personnaliser un cluster à l’aide d’une action de script avec le Kit de développement logiciel (SDK) .NET |Consultez [Personnaliser des clusters HDInsight sous Linux à l’aide d’une action de script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Authentifier des applications de manière interactive à l’aide d’Azure Active Directory avec le Kit de développement logiciel (SDK) .NET |Consultez [Exécution de requêtes Hive avec le Kit de développement logiciel (SDK) .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Dans cet article, l’extrait de code utilise l’approche de l’authentification interactive. |
| Authentifier des applications de manière non interactive à l’aide d’Azure Active Directory avec le Kit de développement logiciel (SDK) .NET |Consultez [Créer des applications HDInsight d’authentification non interactives](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Envoyer une tâche Hive à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Envoyer une tâche Pig à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Envoyer une tâche Sqoop à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Répertorier les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Répertorier les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Mettre à l’échelle les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Mettre à l’échelle les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Autoriser/révoquer l’accès aux clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Autoriser/révoquer l’accès aux clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Mettre à jour les informations d’identification de l’utilisateur HTTP pour les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Mettre à jour les informations d’identification de l’utilisateur HTTP pour les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Rechercher le compte de stockage par défaut pour les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Rechercher le compte de stockage par défaut pour les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Supprimer des clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Supprimer des clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemples
Vous trouverez ci-dessous des exemples de la façon dont une opération est effectuée à l’aide du Kit de développement logiciel (SDK) ASM et de l’extrait de code équivalent pour le SDK ARM.

**Création d’un client CRUD de cluster**

* Ancienne commande (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nouvelle commande (ARM) (autorisation du principal de service)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nouvelle commande (ARM) (autorisation de l’utilisateur)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Création d’un cluster**

* Ancienne commande (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nouvelle commande (ARM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Activation de l’accès HTTP**

* Ancienne commande (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nouvelle commande (ARM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Suppression d’un cluster**

* Ancienne commande (ASM)
  
        client.DeleteCluster(dnsName);
* Nouvelle commande (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);

