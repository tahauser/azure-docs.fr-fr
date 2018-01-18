---
title: Environnements de calcul pris en charge par Azure Data Factory | Microsoft Docs
description: "Découvrez les environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory (tels qu’Azure HDInsight) pour transformer ou traiter les données."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 7733ea111de896ab0f825c85b89be25ebafdbd85
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Environnements de calcul pris en charge par Azure Data Factory
> [!NOTE]
> Cet article s’applique à la version 1 de Data factory, qui est généralement disponible (GA). Si vous utilisez la version 2 du service Azure Data Factory, qui est en préversion, consultez [Services liés de calcul V2 de calcul](../compute-linked-services.md).

Cet article décrit les différents environnements de calcul que vous pouvez utiliser pour traiter ou transformer des données. Il fournit également des détails sur les différentes configurations (à la demande ou de type « apporter votre propre configuration ») prises en charge par Data Factory lors de la configuration des services liés qui relient ces environnements de calcul à Azure Data Factory.

Le tableau suivant fournit une liste d’environnements de calcul pris en charge par Data Factory et les activités qui peuvent s’exécuter sur ces derniers. 

| Environnement de calcul                      | activités                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight à la demande](#azure-hdinsight-on-demand-linked-service) ou [votre propre cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Activités Machine Learning : exécution de lot et mise à jour de ressource](data-factory-azure-ml-batch-execution-activity.md) |
| [Service Analytique Azure Data Lake](#azure-data-lake-analytics-linked-service) | [Langage U-SQL du service Analytique Data Lake](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procédure stockée](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versions de HDInsight prises en charge dans Azure Data Factory
Azure HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées à tout moment. Le choix d'une version crée une version spécifique de la distribution de la plateforme de données Hortonworks (HDP) et un ensemble de composants qui sont contenus dans cette distribution. Microsoft met systématiquement à jour la liste des versions prises en charge de HDInsight pour fournir les derniers correctifs et composants de l’écosystème Hadoop. Pour plus d’informations, voir [Versions de HDInsight prises en charge](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> La version 3.3 de HDInsight basé sur Linux a été mise hors service le 31 juillet 2017. Les clients des services liés HDInsight à la demande de Data Factory v1 ont jusqu’au 15 décembre 2017 pour procéder aux tests et à la mise à niveau vers une version ultérieure de HDInsight. HDInsight basé sur Windows sera mis hors service le 31 juillet 2018.
>
> 

**Que se passera-t-il après la date de mise hors service ?** 

Après le 15 décembre 2017 :

- Vous ne pourrez plus créer de clusters HDInsight Linux version 3.3 (ni des versions précédentes) à l’aide d’un service lié HDInsight à la demande dans Azure Data Factory v1. 
- Si les [propriétés osType et/ou Version](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) ne sont pas explicitement spécifiées dans les définitions JSON existantes d’un service lié HDInsight à la demande d’Azure Data Factory v1, la valeur par défaut **Version=3.1, osType=Windows** sera remplacée par **Version=[dernière version HDI par défaut](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType=Linux**.

Après le 31 juillet 2018 :

- Vous ne pourrez plus créer de clusters HDInsight Windows à l’aide d’un service lié HDInsight à la demande dans Azure Data Factory v1. 

 **Actions recommandées** 

- Mettez à jour les [propriétés osType et/ou Version](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) des définitions du service lié HDInsight à la demande d’Azure Data Factory v1 vers de nouvelles versions de HDInsight basé sur Linux (HDInsight 3.6) afin de vous assurer de pouvoir utiliser les tout derniers correctifs et composants de l’écosystème Hadoop. 
- Avant le 15 décembre 2017, testez les activités de diffusion en continu Hive, Pig, MapReduce et Hadoop d’Azure Data Factory v1 qui désignent le service lié impacté pour vous assurer qu’elles sont compatibles avec la nouvelle valeur par défaut des propriétés *osType* et/ou *Version* (Version=3.6, osType=Linux) ou les propriétés Version et osType explicites de HDInsight vers lesquelles vous procédez à la mise à niveau. Pour plus d’informations sur la compatibilité, consultez les pages web [Effectuer la migration d’un cluster HDInsight Windows vers un cluster Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) et [Quels sont les composants et versions Hadoop disponibles avec HDInsight ?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions) dans la documentation. 
- Définissez explicitement la propriété osType sur Windows avant le 15 décembre 2017 si vous souhaitez continuer à utiliser un service lié HDInsight à la demande d’Azure Data Factory v1 pour créer des clusters HDInsight Windows. Toutefois, nous vous recommandons de procéder à la migration vers les clusters HDInsight Linux avant le 31 juillet 2018. 
- Mettez à jour la définition JSON d’une activité personnalisée DotNet pour utiliser plutôt un service lié Azure Batch si vous utilisez un service lié HDInsight à la demande pour exécuter une activité personnalisée DotNet d’Azure Data Factory v1. Pour en savoir plus, consultez la page web [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) de la documentation. 

>[!Note]
>Pour les clients utilisant votre service lié existant HDInsight avec apport de son propre cluster (BYOC, Bring Your Own Cluster) dans Azure Data Factory v1 ou ceux utilisant un service lié HDInsight à la demande et BYOC dans Azure Data Factory v2, la stratégie de prise en charge de la dernière version des clusters Azure HDInsight est déjà appliquée. Aucune action n’est donc requise. 
>
> 


## <a name="on-demand-compute-environment"></a>Environnement de calcul à la demande
Dans ce type de configuration, l'environnement de calcul est entièrement géré par le service Azure Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez créer un service lié pour un environnement de calcul à la demande, le configurer et contrôler les paramètres granulaires pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.

> [!NOTE]
> La configuration à la demande est actuellement prise en charge uniquement pour les clusters Azure HDInsight.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Service lié à la demande Azure HDInsight
Le service Azure Data Factory peut automatiquement créer un cluster HDInsight à la demande sous Windows/Linux pour traiter les données. Le cluster est créé dans la même région que celle du compte de stockage (propriété linkedServiceName dans JSON) associé au cluster.

Notez les points **importants** suivants sur le service lié HDInsight à la demande :

* Vous ne voyez pas le cluster HDInsight à la demande créé dans votre abonnement Azure. Le service Azure Data Factory gère le cluster HDInsight à la demande à votre place.
* Les journaux des tâches exécutées sur un cluster HDInsight à la demande sont copiés dans le compte de stockage associé au cluster HDInsight. Vous pouvez accéder à ces journaux à partir du portail Azure dans le panneau **Détails sur l’exécution d’activité** . Pour plus de détails, consultez l'article [Surveiller et gérer les pipelines](data-factory-monitor-manage-pipelines.md) .
* Vous êtes facturé uniquement lorsque le cluster HDInsight est actif et exécute des tâches.

> [!IMPORTANT]
> Il faut généralement au moins **20 minutes** pour mettre en service un cluster Azure HDInsight à la demande.
>
> 

### <a name="example"></a>exemples
Le JSON suivant définit un service lié HDInsight à la demande sous Linux. Le service Data Factory crée automatiquement un cluster HDInsight **sous Linux** lors du traitement d’une tranche de données. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il n’existe un cluster activé (**timeToLive**), et est supprimé une fois le traitement activé. 
>
> Comme un nombre croissant de tranches sont traitées, vous voyez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Les noms de ces conteneurs sont conformes au modèle suivant : `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.
>
> 

### <a name="properties"></a>properties
| Propriété                     | DESCRIPTION                              | Obligatoire |
| ---------------------------- | ---------------------------------------- | -------- |
| Type                         | La propriété de type doit être définie sur **HDInsightOnDemand**. | Oui      |
| clusterSize                  | Nombre de nœuds worker/données dans le cluster. Le cluster HDInsight est créé avec 2 nœuds principaux et le nombre de nœuds worker que vous spécifiez pour cette propriété. Les nœuds étant de taille Standard_D3 à 4 cœurs, un cluster à 4 nœuds de travail prend 24 cœurs (4\*4 = 16 nœuds pour les nœuds de travail + 2\*4 = 8 cœurs pour les nœuds principaux). Pour plus d’informations sur le niveau Standard_D3, voir [Création de clusters Hadoop basés sur Linux dans HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Oui      |
| timetolive                   | La durée d’inactivité autorisée pour le cluster HDInsight à la demande. Spécifie la durée pendant laquelle le cluster HDInsight à la demande reste actif après l’achèvement d’une exécution d’activité s’il n’existe aucun autre travail actif dans le cluster.<br/><br/>Par exemple, si une exécution d’activité prend 6 minutes et si la propriété TimeToLive est définie sur 5 minutes, le cluster reste actif pendant 5 minutes après les 6 minutes du traitement de l’exécution d’activité. Si une autre exécution d’activité intervient dans la fenêtre de 6 minutes, elle est traitée par le même cluster.<br/><br/>La création d’un cluster HDInsight à la demande étant une opération coûteuse (elle peut prendre du temps), utilisez ce paramètre selon le besoin pour améliorer les performances d’une fabrique de données en réutilisant un cluster HDInsight à la demande.<br/><br/>Si vous définissez la valeur de la propriété TimeToLive sur 0, le cluster est supprimé dès que l’exécution d’activité est terminée. En revanche, si vous définissez une valeur élevée, le cluster peut rester inactif inutilement, entraînant des coûts élevés. Par conséquent, il est important de définir la valeur appropriée en fonction de vos besoins.<br/><br/>Plusieurs pipelines peuvent partager l’instance du cluster HDInsight à la demande si la valeur de la propriété timetolive est correctement définie. | Oui      |
| version                      | Version du cluster HDInsight. Pour connaître les versions HDInsight autorisées, reportez-vous à la section [Versions de HDInsight prises en charge](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Si elle n’est pas spécifié, la [version HDI la plus récente par défaut](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions) est utilisée. | Non        |
| linkedServiceName            | Le service lié Azure Storage utilisé par le cluster à la demande pour le stockage et le traitement des données. Le cluster HDInsight est créé dans la même région que ce compte de stockage Azure.<p>Actuellement, vous ne pouvez pas créer un cluster HDInsight à la demande qui utilise un Azure Data Lake Store en guise de stockage. Si vous souhaitez stocker les données de résultat à partir du traitement HDInsight dans un Azure Data Lake Store, utilisez une activité de copie pour copier les données du stockage Blob Azure dans Azure Data Lake Store. </p> | Oui      |
| additionalLinkedServiceNames | Spécifie les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les enregistrer en votre nom. Ces comptes de stockage doivent être dans la même région que le cluster HDInsight, qui est créé dans la même région que le compte de stockage spécifié par linkedServiceName. | Non        |
| osType                       | Type de système d'exploitation. Valeurs autorisées : Linux et Windows. Si le type n’est pas spécifié, Linux est utilisé par défaut.  <br/>Nous vous recommandons vivement d’utiliser des clusters HDInsight basés sur Linux car HDInsight sera supprimé sur Windows le 31 juillet 2018. | Non        |
| hcatalogLinkedServiceName    | Le nom du service lié à SQL Azure pointant vers la base de données HCatalog. Le cluster HDInsight à la demande est créé en utilisant Azure SQL Database en tant que metastore. | Non        |

#### <a name="additionallinkedservicenames-json-example"></a>Exemple JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriétés avancées
Vous pouvez également spécifier les propriétés suivantes pour la configuration granulaire du cluster HDInsight à la demande.

| Propriété               | DESCRIPTION                              | Obligatoire |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Spécifie les paramètres de configuration de base (par exemple, core-site.xml) pour le cluster HDInsight à créer. | Non        |
| hBaseConfiguration     | Spécifie les paramètres de configuration HBase (hbase-site.xml) pour le cluster HDInsight. | Non        |
| hdfsConfiguration      | Spécifie les paramètres de configuration HDFS (hdfs-site.xml) pour le cluster HDInsight. | Non        |
| hiveConfiguration      | Spécifie les paramètres de configuration Hive (hive-site.xml) pour le cluster HDInsight. | Non        |
| mapReduceConfiguration | Spécifie les paramètres de configuration MapReduce (mapred-site.xml) pour le cluster HDInsight. | Non        |
| oozieConfiguration     | Spécifie les paramètres de configuration Oozie (oozie-site.xml) pour le cluster HDInsight. | Non        |
| stormConfiguration     | Spécifie les paramètres de configuration Storm (storm-site.xml) pour le cluster HDInsight. | Non        |
| yarnConfiguration      | Spécifie les paramètres de configuration Yarn (yarn-site.xml) pour le cluster HDInsight. | Non        |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemple : configuration de cluster HDInsight à la demande avec les propriétés avancées

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Tailles de nœuds
Vous pouvez spécifier les tailles du nœud principal, du nœud de données et du nœud zookeeper en utilisant les propriétés suivantes : 

| Propriété          | DESCRIPTION                              | Obligatoire |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Spécifie la taille du nœud principal. La valeur par défaut est Standard_D3. Pour plus d’informations, consultez la section **Spécification des tailles de nœud**. | Non        |
| dataNodeSize      | Spécifie la taille du nœud de données. La valeur par défaut est Standard_D3. | Non        |
| zookeeperNodeSize | Spécifie la taille du nœud ZooKeeper. La valeur par défaut est Standard_D3. | Non        |

#### <a name="specifying-node-sizes"></a>Spécification des tailles de nœud
Pour connaître les valeurs des chaînes à spécifier pour les propriétés mentionnées dans la section précédente, consultez [Tailles des machines virtuelles](../../virtual-machines/linux/sizes.md). Les valeurs doivent être conformes aux **applets de commande et API** référencées dans l’article. Comme vous pouvez le voir dans l’article, le nœud de données de grande taille (par défaut) a 7 Go de mémoire, ce qui risque de s’avérer insuffisant pour votre scénario. 

Si vous voulez créer des nœuds principaux et des nœuds worker de taille D4, spécifiez la valeur **Standard_D4** pour les propriétés headNodeSize et dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Si vous spécifiez une valeur incorrecte pour ces propriétés, vous risquez de rencontrer l’ **erreur** suivante : Impossible de créer le cluster. Exception: Unable to complete the cluster create operation. Operation failed with code ’400’. (L’opération a échoué avec le code « 400 ».) Cluster left behind state: 'Error'. Message: 'PreClusterCreationValidationFailure'. Quand vous recevez ce message d’erreur, vérifiez que vous utilisez les noms d’**applet de commande et d’API** figurant dans l’article [Tailles des machines virtuelles](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Actuellement, Azure Data Factory ne prend pas en charge les clusters HDInsight avec Azure Data Lake Store comme magasin principal. Utilisez Stockage Azure comme magasin principal pour les clusters HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Apportez votre propre environnement de calcul
Dans ce type de configuration, les utilisateurs peuvent inscrire un environnement de calcul existant en tant que service lié dans Data Factory. L'environnement de calcul est géré par l'utilisateur et le service Data Factory l'utilise pour exécuter les activités.

Ce type de configuration est pris en charge pour les environnements de calcul suivants :

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Service Analytique Azure Data Lake
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Service lié Azure HDInsight
Vous pouvez créer un service lié Azure HDInsight pour inscrire votre propre cluster HDInsight avec Data Factory.

### <a name="example"></a>exemples

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>properties
| Propriété          | DESCRIPTION                              | Obligatoire |
| ----------------- | ---------------------------------------- | -------- |
| Type              | La propriété de type doit être définie sur **HDInsight**. | Oui      |
| clusterUri        | L'URI du cluster HDInsight.        | Oui      |
| username          | Spécifiez le nom de l'utilisateur à utiliser pour se connecter à un cluster HDInsight existant. | Oui      |
| password          | Spécifiez le mot de passe du compte d'utilisateur.   | Oui      |
| linkedServiceName | Nom du service lié de stockage Azure faisant référence au stockage Blob Azure utilisé par le cluster HDInsight. <p>Actuellement, vous ne pouvez pas spécifier un service lié Azure Data Lake Store pour cette propriété. Vous pouvez accéder aux données d’Azure Data Lake Store à partir de scripts Hive/Pig si le cluster HDInsight a accès à Data Lake Store. </p> | Oui      |

## <a name="azure-batch-linked-service"></a>Service lié Azure Batch
Vous pouvez créer un service lié Azure Batch pour inscrire un pool de machines virtuelles (VM) Batch à une fabrique de données. Vous pouvez exécuter des activités personnalisées .NET à l'aide d'Azure Batch ou Azure HDInsight.

Consultez les rubriques suivantes si vous ne connaissez pas le service Azure Batch :

* [Présentation de base d’Azure Batch](../../batch/batch-technical-overview.md) pour une vue d’ensemble du service Azure Batch.
* Applet de commande [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) pour créer un compte Azure Batch (ou) [Portail Azure](../../batch/batch-account-create-portal.md) pour créer le compte Azure Batch à l’aide du portail Azure. Pour obtenir des instructions détaillées sur l'utilisation de l'applet de commande, consultez la rubrique [Utilisation de PowerShell pour gérer un compte Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) pour créer un pool Azure Batch.

### <a name="example"></a>exemples

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Ajoutez « **.\<nom région\>** » au nom de votre compte Batch pour la propriété **accountName**. Exemple :

```json
"accountName": "mybatchaccount.eastus"
```

Une autre option consiste à fournir le point de terminaison batchUri comme indiqué dans l’exemple suivant :

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>properties
| Propriété          | DESCRIPTION                              | Obligatoire |
| ----------------- | ---------------------------------------- | -------- |
| Type              | La propriété de type doit être définie sur **AzureBatch**. | Oui      |
| accountName       | Nom du compte Azure Batch.         | Oui      |
| accessKey         | Clé d'accès du compte Azure Batch.  | Oui      |
| poolName          | Nom du pool de machines virtuelles.    | Oui      |
| linkedServiceName | Nom du service lié Azure Storage associé à ce service lié Azure Batch. Ce service lié est utilisé pour présenter les fichiers nécessaires à l'exécution de l'activité et stocker les journaux d'exécution de l'activité. | Oui      |

## <a name="azure-machine-learning-linked-service"></a>Service lié Microsoft Azure Machine Learning
Vous créez un service lié Azure Machine Learning pour inscrire un point de terminaison de notation par lot Machine Learning pour une fabrique de données.

### <a name="example"></a>exemples

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>properties
| Propriété   | DESCRIPTION                              | Obligatoire |
| ---------- | ---------------------------------------- | -------- |
| type       | La propriété de type doit être définie sur **AzureML**. | Oui      |
| mlEndpoint | L'URL de la notation par lot.                   | Oui      |
| apiKey     | L'API du modèle d'espace de travail publié.     | Oui      |

## <a name="azure-data-lake-analytics-linked-service"></a>Service lié Analytique Azure Data Lake
Vous créez un service lié **Analytique Azure Data Lake** pour lier un service de calcul Analytique Azure Data Lake Analytics à une fabrique de données Azure. L’activité U-SQL Analytique Data Lake dans le pipeline fait référence à ce service lié. 

Le tableau suivant décrit les propriétés génériques utilisées dans la définition JSON. Vous pouvez ensuite choisir entre une authentification par principal de service et par informations d’identification utilisateur.

| Propriété                 | DESCRIPTION                              | Obligatoire                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | La propriété de type doit être définie sur **AzureDataLakeAnalytics**. | Oui                                      |
| **accountName**          | Nom du compte du service Analytique Azure Data Lake.  | Oui                                      |
| **dataLakeAnalyticsUri** | URI du service Analytique Azure Data Lake.           | Non                                        |
| **subscriptionId**       | ID d’abonnement Azure                    | Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). |
| **resourceGroupName**    | Nom du groupe de ressources Azure                | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé). |

### <a name="service-principal-authentication-recommended"></a>Authentification d’un principal du service (recommandée)
Pour utiliser l’authentification d’un principal du service, inscrivez une entité d’application dans Azure Active Directory (Azure AD) et lui accorder l’accès à Data Lake Store. Consultez la page [Authentification de service à service](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md) pour des instructions détaillées. Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :
* ID de l'application
* Clé de l'application 
* ID client

Utilisez l’authentification par principal de service en spécifiant les propriétés suivantes :

| Propriété                | DESCRIPTION                              | Obligatoire |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Spécifiez l’ID client de l’application.     | Oui      |
| **servicePrincipalKey** | Spécifiez la clé de l’application.           | Oui      |
| **client**              | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | Oui      |

**Exemple : authentification du principal de service**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Authentification des informations d’identification utilisateur
Vous pouvez également utiliser l’authentification par informations d’identification utilisateur pour Data Lake Analytics en spécifiant les propriétés suivantes :

| Propriété          | DESCRIPTION                              | Obligatoire |
| :---------------- | :--------------------------------------- | :------- |
| **authorization** | Cliquez sur le bouton **Autoriser** dans Data Factory Editor et saisissez vos informations d’identification, ce qui affecte l’URL d’autorisation générée automatiquement à cette propriété. | Oui      |
| **sessionId**     | ID de session OAuth issu de la session d’autorisation OAuth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Ce paramètre est généré automatiquement lorsque vous utilisez Data Factory Editor. | Oui      |

**Exemple : authentification des informations d’identification utilisateur**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiration du jeton
Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Le message d’erreur suivant peut s’afficher à **l’expiration du jeton** d’authentification : Erreur de l’opération d’informations d’identification : invalid_grant - AADSTS70002: Erreur de validation des informations d’identification. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z ».

| Type d’utilisateur                                | Expire après                            |
| :--------------------------------------- | :--------------------------------------- |
| Comptes d’utilisateurs NON gérés par Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 heures                                 |
| Comptes d’utilisateurs gérés par Azure Active Directory (AAD) | 14 jours après la dernière exécution de tranche de données. <br/><br/>90 jours, si une tranche basée sur un service lié OAuth est exécutée au moins une fois tous les 14 jours. |

Pour éviter ou résoudre cette erreur, accordez une nouvelle autorisation à l’aide du bouton **Autoriser** au moment de **l’expiration du jeton**, puis redéployer le service lié. Vous pouvez également générer par programmation des valeurs pour les propriétés **sessionId** et **authorization** à l’aide du code suivant :

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Pour plus d’informations sur les classes Data Factory utilisées dans le code, consultez les rubriques [AzureDataLakeStoreLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse, classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Ajoutez une référence à Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog. 

## <a name="azure-sql-linked-service"></a>Service lié Azure SQL
Créez un service lié Azure SQL et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) .

## <a name="azure-sql-data-warehouse-linked-service"></a>Service lié Azure SQL Data Warehouse
Créez un service lié Azure SQL Data Warehouse et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) .

## <a name="sql-server-linked-service"></a>Service SQL Server lié
Créez un service lié à SQL Server et utilisez-le avec l’ [activité de procédure stockée](data-factory-stored-proc-activity.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) .

