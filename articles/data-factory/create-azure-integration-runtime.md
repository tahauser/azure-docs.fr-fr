---
title: Créer un runtime d’intégration Azure dans Azure Data Factory | Microsoft Docs
description: Apprenez à créer un runtime d’intégration Azure dans Azure Data Factory, qui est utilisé pour copier des données et répartir des activités de transformation.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: e2ebb3db4d51bf4ec1dea4f2f5cc4cc9dcb2ad70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Comment créer et configurer un runtime d’intégration Azure
Le runtime d’intégration (IR) représente l’infrastructure de calcul utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Pour plus d’informations sur le runtime d’intégration, voir [Runtime d’intégration](concepts-integration-runtime.md).

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), consultez [Documentation de Data Factory V1](v1/data-factory-introduction.md).

Un runtime d’intégration Azure fournit un calcul entièrement géré pour effectuer en mode natif un déplacement de données et distribuer des activités de transformation de données à des services de calcul tels que HDInsight. Il est hébergé dans l’environnement Azure et prend en charge la connexion à des ressources dans un environnement de réseau public avec des points de terminaison accessibles au public.

Ce document décrit comment créer et configurer un runtime d’intégration Azure. 

## <a name="default-azure-ir"></a>Runtime d’intégration Azure par défaut
Par défaut, chaque fabrique de données a un runtime d’intégration Azure dans le service principal, qui prend en charge les opérations sur les banques de données et les services de calcul en ligne dans un réseau public. L’emplacement de ce runtime d’intégration Azure est résolu automatiquement. Si la propriété **connectVia** n’est pas spécifiée dans la définition du service lié, le runtime d’intégration Azure spécifié est utilisé. Vous devez uniquement créer explicitement un runtime d’intégration Azure lorsque vous souhaitez définir explicitement l’emplacement du runtime d’intégration, ou si vous souhaitez grouper virtuellement les exécutions d’activité sur différents runtimes d’intégration à des fins de gestion. 

## <a name="create-azure-ir"></a>Créer un runtime d’intégration Azure
Vous pouvez créer un runtime d’intégration à l’aide de l’applet de commande PowerShell **Set-AzureRmDataFactoryV2IntegrationRuntime**. Pour créer un runtime d’intégration Azure, vous spécifiez le nom, l’emplacement et le type de la commande. Voici un exemple de commande pour créer un runtime d’intégration Azure avec l’emplacement défini sur « West Europe » (Europe de l’ouest) :

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pour un runtime d’intégration Azure, le type doit être défini sur **Géré**. Vous n’avez pas besoin spécifier les détails du calcul, car il est entièrement géré de façon élastique dans le cloud. Spécifiez des détails de calcul tels que la taille de nœud et le nombre de nœuds lorsque vous souhaitez créer un runtime d’intégration Azure-SSIS. Pour plus d’informations, voir [Créer et configurer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md).

Vous pouvez configurer un runtime d’intégration Azure existant pour modifier son emplacement à l’aide de l’applet de commande PowerShell Set-AzureRmDataFactoryV2IntegrationRuntime. Pour plus d’informations sur l’emplacement d’un runtime d’intégration Azure, voir [Présentation du runtime d’intégration](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Utiliser un runtime d’intégration Azure

Une fois un runtime d’intégration Azure créé, vous pouvez le référencer dans votre définition de service lié. Voici un exemple de la façon dont vous pouvez référencer le runtime d’intégration Azure créé ci-dessus à partir d’un service lié de Stockage Azure :  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants concernant la création d’autres types de runtimes d’intégration :

- [Créer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md)
- [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md)
 
