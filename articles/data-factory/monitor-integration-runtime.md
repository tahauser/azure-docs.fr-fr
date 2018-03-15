---
title: "Surveiller le runtime d’intégration dans Azure Data Factory | Microsoft Docs"
description: "Découvrez comment surveiller plusieurs types de runtime d’intégration dans Azure Data Factory."
services: data-factory
documentationcenter: 
author: douglaslMS
manager: 
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: douglasl
ms.openlocfilehash: 74a4ad6438f9e66331f76e87d20d1eb8b29e8451
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Surveiller un runtime d’intégration dans Azure Data Factory  
Le **runtime d’intégration** est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Azure Data Factory propose trois types de runtimes d’intégration :

- Runtime d’intégration Azure
- Runtime d’intégration auto-hébergé
- Runtime d’intégration Azure SSIS

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la [documentation Data Factory version 1](v1/data-factory-introduction.md).

Pour obtenir l’état d’une instance de runtime d’intégration (IR), exécutez la commande PowerShell suivante : 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

Cette cmdlet renvoie des informations différentes en fonction du type de runtime d’intégration. Cet article explique les propriétés et les états de chacun des types de runtime d’intégration.  

## <a name="azure-integration-runtime"></a>Runtime d’intégration Azure
La ressource de calcul d’un runtime d’intégration Azure est entièrement gérée dans Azure, en toute flexibilité. Le tableau suivant fournit des descriptions des propriétés renvoyées par la commande **Get-AzureRmDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>properties
Le tableau suivant fournit des descriptions des propriétés renvoyées par la cmdlet pour un runtime d’intégration Azure :

| Propriété | Description |
-------- | ------------- | 
| NOM | Nom du runtime d’intégration Azure. |  
| État | État du runtime d’intégration Azure. | 
| Lieu | Emplacement du runtime d’intégration Azure. Pour plus d’informations sur l’emplacement d’un runtime d’intégration d’Azure, voir [Introduction au runtime d’intégration](concepts-integration-runtime.md). |
| DataFactoryName | Nom de la fabrique de données à laquelle le runtime d’intégration Azure appartient. | 
| ResourceGroupName | Nom du groupe de ressources auquel la fabrique de données appartient.  |
| Description | Description du runtime d’intégration Azure.  |

### <a name="status"></a>Statut
Le tableau suivant indique les états possibles d’un runtime d’intégration Azure :

| Statut | Commentaires/Scénarios | 
| ------ | ------------------ |
| En ligne | Le runtime d’intégration Azure est en ligne et prêt à être utilisé. | 
| Hors ligne | Le runtime d’intégration Azure est hors connexion en raison d’une erreur interne. |

## <a name="self-hosted-integration-runtime"></a>Runtime d’intégration auto-hébergé
Le tableau suivant fournit des descriptions des propriétés renvoyées par la cmdlet Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> Les propriétés renvoyées et l’état contiennent des informations sur le runtime d’intégration auto-hébergé dans son ensemble et sur chacun des nœuds du runtime.  

### <a name="properties"></a>properties

Le tableau suivant fournit des descriptions des propriétés de surveillance pour **chaque nœud** :

| Propriété | Description | 
| -------- | ----------- | 
| NOM | Nom du runtime d’intégration auto-hébergé et des nœuds qui lui sont associés. Le nœud est un ordinateur Windows local sur lequel le runtime d’intégration auto-hébergé est installé. |  
| Statut | État du runtime d’intégration auto-hébergé dans son ensemble et de chacun des nœuds. Exemple : En ligne/Hors connexion/Limité/etc. Pour plus d’informations sur ces états, voir la section suivante. | 
| Version | Version du runtime d’intégration auto-hébergé et de chacun des nœuds. La version du runtime d’intégration auto-hébergé est déterminée d’après la version de la majorité des nœuds dans le groupe. S’il existe des nœuds de différentes versions dans la configuration du runtime d’intégration auto-hébergé, seuls les nœuds dont le numéro de version est identique à celui du runtime d’intégration auto-hébergé logique fonctionnent correctement. Les autres sont en mode limité et ont besoin d’une mise à jour manuelle (uniquement si la mise à jour automatique échoue). | 
| Mémoire disponible | Mémoire disponible dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. | 
| Utilisation du processeur | Utilisation du processeur dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. |
| Réseau (entrée/sortie) | Utilisation du réseau dans un nœud de runtime d’intégration auto-hébergé. Cette valeur est un instantané en quasi temps réel. | 
| Tâches simultanées (en cours d’exécution/limite) | Nombre de travaux ou tâches qui s’exécutent sur chaque nœud. Cette valeur est un instantané en quasi temps réel. La limite correspond au nombre maximal de travaux simultanés pour chaque nœud. Cette valeur est définie selon la taille de l’ordinateur. Vous pouvez augmenter la limite pour monter en puissance l’exécution de tâches simultanées dans les scénarios avancés, où le processeur/la mémoire /le réseau sont sous-utilisés, alors que les activités expirent. Cette fonctionnalité est également disponible avec un runtime d’intégration d’auto-hébergé à nœud unique. |
| Rôle | Il existe deux types de rôles dans un runtime d’intégration auto-hébergé à nœuds multiples : répartiteur et rôle de travail. Tous les nœuds sont des rôles de travail, ce qui signifie qu’ils peuvent tous être utilisés pour exécuter des tâches. Il n’existe qu’un seul nœud répartiteur. Il est utilisé pour extraire des tâches ou des travaux auprès de services cloud et pour les répartir entre différents nœuds rôles de travail. Le nœud répartiteur est également un nœud rôle de travail. |

Certains paramètres de ces propriétés semblent plus logiques en présence de deux nœuds ou plus (scénario d’augmentation du nombre d’instances) dans le runtime d’intégration auto-hébergé. 
  
### <a name="status-per-node"></a>État (par nœud)
Le tableau suivant indique les états possibles d’un nœud de runtime d’intégration Azure auto-hébergé :

| Statut | Description |
| ------ | ------------------ | 
| En ligne | Le nœud est connecté au service Data Factory. |
| Hors ligne | Le nœud est hors connexion. |
| Mise à niveau | Le nœud est en cours de mise à jour automatique. |
| Limité | En raison d’un problème de connectivité. Éventuellement dû à un problème de port HTTP 8050, à un problème de connectivité du bus de service ou à un problème de synchronisation des informations d’identification. |
| Inactif | La configuration du nœud est différente de celle de la majorité des autres nœuds. |

Un nœud peut être inactif quand il ne parvient pas à se connecter à d’autres nœuds.

### <a name="status-overall-self-hosted-integration-runtime"></a>État (runtime d’intégration auto-hébergé dans son ensemble)
Le tableau suivant indique les états possibles d’un runtime d’intégration Azure auto-hébergé. Cet état dépend des états de tous les nœuds appartenant au runtime. 

| Statut | Description |
| ------ | ----------- | 
| Inscription requise | Aucun nœud n’est encore inscrit dans ce runtime d’intégration auto-hébergé. |
| En ligne | Tous les nœuds sont en ligne. |
| Hors ligne | Aucun nœud n’est en ligne. |
| Limité | L’état de tous les nœuds de ce runtime d’intégration auto-hébergé n’est pas sain. Cet état sert d’avertissement. Il peut indiquer que certains nœuds sont en panne. Cela peut être dû à un problème de synchronisation des informations d’identification sur le nœud répartiteur ou le nœud rôle de travail. |

Utilisez la cmdlet **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric**. Il s’agit d’extraire la charge utile JSON qui contient les propriétés détaillées du runtime d’intégration auto-hébergé et les valeurs des instantanés pendant toute la durée d’exécution de la cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Exemple de sortie (suppose que deux nœuds sont associés à ce runtime d’intégration auto-hébergé) :

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Runtime d’intégration Azure SSIS
Le runtime d’intégration Azure-SSIS est un cluster entièrement géré de machines virtuelles (ou nœuds) Azure. Il est dédié à l’exécution de vos packages SSIS. Il n’exécute aucune autre activité d’Azure Data Factory. Une fois configuré, vous pouvez interroger ses propriétés et surveiller ses états (dans son ensemble ou selon les nœuds).

### <a name="properties"></a>properties

| Propriété/état | Description |
| --------------- | ----------- |
| CreateTime | Heure UTC de création du runtime d’intégration Azure-SSIS. |
| Nœuds | Nœuds disponibles ou alloués de votre runtime d’intégration Azure-SSIS avec états spécifiques aux nœuds (démarrage/disponibilité/recyclage/indisponibilité) et erreurs exploitables. |
| OtherErrors | Erreurs exploitables non spécifiques aux nœuds de votre runtime d’intégration Azure-SSIS. |
| LastOperation | Résultat de la dernière opération de démarrage ou d’arrêt de votre runtime d’intégration Azure-SSIS avec erreurs exploitables en cas d’échec. |
| État | État d’ensemble (initial/démarrage/arrêt/arrêté) de votre runtime d’intégration Azure-SSIS. |
| Lieu | Emplacement de votre runtime d’intégration Azure-SSIS. |
| NodeSize | Taille de chacun des nœuds de votre runtime d’intégration Azure-SSIS. |
| NodeCount | Nombre de nœuds de votre runtime d’intégration Azure-SSIS. |
| MaxParallelExecutionsPerNode | Nombre d’exécutions en parallèle par nœud dans votre runtime d’intégration Azure-SSIS. |
| CatalogServerEndpoint | Point de terminaison du serveur Azure SQL Database/Managed Instance existant pour héberger la base de données SSISDB. |
| CatalogAdminUserName | Nom d’utilisateur administrateur du serveur Azure SQL Database/ Managed Instance existant. Le service Data Factory utilise ces informations pour préparer et gérer pour vous la base de données SSISDB. |
| CatalogAdminPassword | Mot de passe administrateur du serveur Azure SQL Database/ Managed Instance existant. |
| CatalogPricingTier | Niveau de tarification de la base de données SSISDB hébergée par le serveur Azure SQL Database existant.  Ne s’applique pas à Azure SQL Managed Instance hébergeant la base de données SSISDB. |
| VNetId | ID de ressource de réseau virtuel (VNet) que votre runtime d’intégration Azure-SSIS doit rejoindre. |
| Sous-réseau | Nom du sous-réseau que pour votre runtime d’intégration Azure-SSIS doit rejoindre. |
| ID | ID de ressource de votre runtime d’intégration Azure-SSIS. |
| type | Type (managé/auto-hébergé) de votre runtime d’intégration Azure-SSIS. |
| ResourceGroupName | Nom du groupe de ressources Azure dans lequel votre fabrique de données et le runtime d’intégration Azure-SSIS ont été créés. |
| DataFactoryName | Nom de votre service Azure Data Factory. |
| NOM | Nom de votre runtime d’intégration Azure-SSIS. |
| Description | Description de votre runtime d’intégration Azure-SSIS. |

  
### <a name="status-per-node"></a>État (par nœud)

| Statut | Description |
| ------ | ----------- | 
| Démarrage en cours | Ce nœud est en cours de préparation. |
| Disponible | Ce nœud est prêt pour vous permettre de déployer ou d’exécuter des packages SSIS. |
| Recyclage | Ce nœud est en cours de réparation ou de redémarrage. |
| Non disponible | Ce nœud n’est pas prêt pour vous permettre de déployer ou d’exécuter des packages SSIS. Il présente des erreurs ou problèmes sur lesquels vous pouvez intervenir et que vous pouvez résoudre. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>État (runtime d’intégration Azure SSIS dans son ensemble)

| État global | Description | 
| -------------- | ----------- | 
| Initial | Les nœuds de votre runtime d’intégration Azure-SSIS ont été alloués ou préparés. | 
| Démarrage en cours | Les nœuds de votre runtime d’intégration Azure-SSIS sont en cours d’affectation ou de préparation et la facturation a commencé. |
| Démarré | Les nœuds de votre runtime d’intégration Azure-SSIS ont été alloués ou préparés et ils sont prêts pour vous permettre de déployer et d’exécuter des packages SSIS. |
| En cours d’arrêt  | Les nœuds de votre runtime d’intégration Azure-SSIS sont libérés. |
| Arrêté | Les nœuds de votre runtime d’intégration Azure-SSIS ont été libérés et la facturation a pris fin. |

Pour plus d’informations sur le runtime d’intégration Azure-SSIS, voir les articles suivants :

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Procédures : Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Créer un runtime d’intégration Azure-SSIS). Cet article s’appuie sur le didacticiel et fournit des instructions sur la façon d’utiliser Azure SQL Managed Instance (préversion privée) et d’associer le runtime d’intégration à un VNet. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Attacher un runtime d’intégration Azure-SSIS à un VNet). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure (VNet). Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants pour surveiller les pipelines de différentes façons : 

- [Démarrages rapides : create a data factory](quickstart-create-data-factory-dot-net.md) (Créer une fabrique de données)
- [Use Azure Monitor to monitor Data Factory pipelines](monitor-using-azure-monitor.md) (Utiliser Azure Monitor pour surveiller les pipelines Data Factory)