---
title: Configurer Azure-SSIS Integration Runtime pour de hautes performances | Documents Microsoft
description: "Découvrez comment configurer les propriétés d’Azure-SSIS Integration Runtime pour de hautes performances"
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d0e75ad85731b10f9a993c2fa62f30c0142ed05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configurer Azure-SSIS Integration Runtime pour de hautes performances

Cet article décrit comment configurer Azure-SSIS Integration Runtime pour de hautes performances. Le Azure-SSIS Integration Runtime vous permet de déployer et d’exécuter des packages SQL Server Integration Services (SSIS) dans Azure. Pour en savoir plus sur Azure-SSIS Integration Runtime, consultez l’article [Runtime d’intégration](concepts-integration-runtime.md#azure-ssis-integration-runtime). Pour plus d’informations sur le déploiement et l’exécution de packages SSIS sur Azure, consultez [Migration « lift-and-shift » des charges de travail SQL Server Integration Services dans le cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Cet article contient des résultats et observations de performance suite à des tests interne effectués par des membres de l’équipe de développement SSIS. Vos résultats peuvent varier. Effectuez vos propres tests avant de finaliser vos paramètres de configuration, qui affectent les coûts et les performances.

## <a name="properties-to-configure"></a>Propriétés à configurer

La partie suivante d’un script de configuration montre les propriétés que vous pouvez configurer lorsque vous créez une infrastructureAzure-SSIS Integration Runtime. Pour le script PowerShell et la description complets, consultez [Déployer des packages SQL Server Integration Services pour Azure](tutorial-deploy-ssis-packages-azure.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** est l’emplacement du nœud du rôle de travail du runtime d’intégration. Le nœud du rôle de travail maintient une connexion constante avec la base de données du catalogue SSIS (SSISDB) sur une base de données Azure SQL. Définissez **AzureSSISLocation** au même emplacement que le serveur SQL Database qui héberge SSISDB, ce qui permet au runtime d’intégration de fonctionner le plus efficacement possible.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
La version préliminaire publique d’Azure Data Factory v2, y compris Azure-SSIS IR, prend en charge les options suivantes :
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2.

Dans le test interne officiel effectué par l’équipe d’ingénierie SSIS, la série D semble mieux appropriée à l’exécution du package SSIS que la série A.

-   Le rapport performance/prix de la série D est supérieur à celui de la série A.
-   Le débit de la série D est supérieur à celui de la série A à prix équivalent.

### <a name="configure-for-execution-speed"></a>Configurer pour la vitesse d’exécution
Si vous n’avez pas de nombreux packages à exécuter et que vous voulez exécuter rapidement des packages, utilisez les informations données dans le tableau suivant pour choisir un type de machine virtuelle approprié à votre scénario.

Ces données représentent une seule exécution de package sur un seul nœud de rôle de travail. Le package charge 10 millions d’enregistrements avec des colonnes de prénoms et de noms depuis le stockage Blob Azure, génère une colonne avec des noms complets et écrit les enregistrements dont le nom complet a plus de 20 caractères dans le stockage blob Azure.

![Vitesse d’exécution du package Azure-SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Configurer pour le débit global

Si vous avez beaucoup de packages à exécuter et que vous vous souciez surtout du débit global, utilisez les informations données dans le tableau suivant pour choisir un type de machine virtuelle approprié à votre scénario.

![Débit global maximal d’Azure-SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajuste l’évolutivité du runtime d’intégration. Le débit du runtime d’intégration est proportionnel au **AzureSSISNodeNumber**. Définir le **AzureSSISNodeNumber** une petite valeur dans un premier temps, surveiller le débit du runtime d’intégration, puis modifiez la valeur pour votre scénario. Pour reconfigurer le nœud du rôle de travail, consultez [Gérer Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Lorsque vous utilisez déjà un nœud de rôle de travail puissant pour exécuter des packages, l’augmentation de **AzureSSISMaxParallelExecutionsPerNode** peut augmenter le débit global du runtime d’intégration. Pour les nœuds Standard_D1_v2, 1 à 4 exécutions parallèles par nœud sont prises en charge. Pour tous les autres types de nœuds, 1 à 8 exécutions parallèles par nœud sont prises en charge.
Vous pouvez estimer la valeur appropriée sur la base du coût de votre package et sur les configurations suivantes des nœuds de rôle de travail. Pour plus d’informations, consultez [Tailles de machines virtuelles à usage général](../virtual-machines/windows/sizes-general.md).

| Taille             | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | Débit de stockage temporaire local max : E/S par seconde / Mbits/s de lecture / Mbits/s d’écriture | Disques de données max / débit : E/S par seconde | Nombre max de cartes réseau / Performance réseau attendue (Mbits/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1 500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3 000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6 000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1 000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2 000                                       |

Voici les instructions à suivre pour définir la valeur correcte pour la propriété **AzureSSISMaxParallelExecutionsPerNode** : 

1. Commencez par définir une petite valeur.
2. Augmentez-la par petites quantités pour vérifier si le débit global est amélioré.
3. Cessez d’augmenter la valeur lorsque le débit global atteint la valeur maximale.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** est le niveau de tarification pour la base de données du catalogue SSIS (SSISDB) sur une base de données Azure SQL. Ce paramètre affecte le nombre maximal de rôles de travail dans l’instance IR, la vitesse de la file d’attente de l’exécution d’un package et la vitesse de chargement du journal d’exécution.

-   Si vous ne vous souciez pas de la vitesse d’exécution de la file d’attente du package et de chargement du journal d’exécution, vous pouvez choisir le niveau tarifaire le plus bas de la base de données. Azure SQL Database avec la tarification de base prend en charge 8 threads de travail dans une instance de runtime d’intégration.

-   Choisissez une base de données plus puissante que la base de données de base si le nombre de rôles de travail est supérieur à 8, ou si le nombre de cœurs est supérieur à 50. Dans le cas contraire, la base de données devient le goulot d’étranglement de l’instance du runtime d’intégration et les performances s’en ressentent.

Vous pouvez également ajuster le niveau tarifaire de la base de données en fonction des informations d’utilisation de l’[unité de transaction de base de données](../sql-database/sql-database-what-is-a-dtu.md) (DTU) disponibles sur le portail Azure.

## <a name="design-for-high-performance"></a>Conception pour de hautes performances
La conception d’un package SSIS à exécuter sur Azure diffère de la conception d’un package pour une exécution locale. Au lieu de combiner plusieurs tâches indépendantes dans le même package, séparez-les en plusieurs packages pour une exécution plus efficace dans Azure-SSIS Integration Runtime. Créez une exécution pour chaque package, afin qu’aucun package ne doive attendre que l’exécution des autres soit terminée. Cette approche tire parti de l’évolutivité d’Azure-SSIS Integration Runtime et améliore le débit global.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur Azure-SSIS Integration Runtime. Consulter [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).