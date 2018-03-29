---
title: Exemples Azure PowerShell pour Azure Data Factory | Microsoft Docs
description: Exemples Azure PowerShell - Scripts pour vous aider à créer et à gérer des fabriques de données.
services: data-factory
author: douglaslMS
manager: douglaslMS
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 7071ab88fd42d26c841d46c2cc42dbfdca6418cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Exemples Azure PowerShell pour Azure Data Factory

Le tableau suivant comprend des liens vers des exemples de scripts Azure PowerShell pour Azure Data Factory.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, généralement disponible (GA), consultez [les exemples pour la version 1 de Data Factory](v1/data-factory-samples.md).

| |  |
|---|---|
|**Copie de données**||
|[Copier des objets blob à partir d’un dossier vers un autre dossier dans un stockage Blob Azure](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des objets blob depuis un dossier du Stockage Blob Azure vers un autre dossier de ce même stockage. |
|[Copier des données d’une base de données SQL Server locale vers le Stockage Blob Azure](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell copie des données depuis une base de données SQL Server locale vers un stockage Blob Azure. |
|[Copie en bloc](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell copie les données de plusieurs tables dans une base de données SQL Azure vers un entrepôt de données SQL Azure. |
|[Copie incrémentielle](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cet exemple de script PowerShell charge uniquement les enregistrements nouveaux ou mis à jour à partir d’un magasin de données source vers un magasin de données récepteur, après la copie initiale complète des données de la source vers le récepteur. |
|**Transformation de données**||
|[Transformer des données à l’aide d’un cluster Spark](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell transforme les données en exécutant un programme sur un cluster Spark. |
| **Intégrer par « lift-and-shift » des packages SSIS sur Azure**||
|[Créer un runtime d’intégration Azure-SSIS](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ce script PowerShell provisionne un runtime d’intégration SSIS-Azure qui exécute les packages SSIS (SQL Server Integration Services) dans Azure. |



