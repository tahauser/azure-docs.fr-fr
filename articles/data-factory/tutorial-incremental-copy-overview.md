---
title: "Copier de façon incrémentielle des données en utilisant Azure Data Factory | Microsoft Docs"
description: "Ces didacticiels vous montrent comment copier de façon incrémentielle des données d’un magasin de données source dans un magasin de données de destination. Dans le premier, les données sont copiées à partir d’une table."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: e7582b2ea209e608abce721ef0cd3555e5ccec93
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Charger de façon incrémentielle des données d’un magasin de données source vers un magasin de données de destination

Dans une solution d’intégration de données, le chargement incrémentielle (ou delta) de données après un chargement initial complet de données est un scénario largement utilisé. Les didacticiels de cette section vous montrent différentes méthodes de chargement des données de façon incrémentielle à l’aide d’Azure Data Factory version 2.

## <a name="delta-data-loading-by-using-a-watermark"></a>Chargement de données delta à l’aide d’un filigrane
Dans ce cas, vous définissez un filigrane dans votre base de données source. Un filigrane est une colonne incluant une clé d’incrémentation ou l’horodatage de la dernière mise à jour. La solution de chargement delta charge les données modifiées entre un ancien filigrane et un nouveau filigrane. Le flux de travail de cette approche est illustré dans le diagramme suivant : 

![Flux de travail d’utilisation d’un filigrane](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

Pour des instructions pas à pas, consultez les didacticiels suivants : 

- [Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
- [Copier de façon incrémentielle des données provenant de plusieurs tables d’une base de données SQL Server locale vers Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-by-using-the-change-tracking-technology"></a>Chargement de données delta à l’aide de la technologie Change Tracking
La technologie Change Tracking est une solution légère dans SQL Server et Azure SQL Database qui fournit un mécanisme de suivi des modifications efficace pour les applications. Elle permet à une application d’identifier facilement les données qui ont été insérées, mises à jour ou supprimées. 

Le flux de travail de cette approche est illustré dans le diagramme suivant :

![Flux de travail pour l’utilisation de Change Tracking](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

Pour des instructions pas à pas, consultez les didacticiels suivants : <br/>
[Copier de façon incrémentielle des données d’Azure SQL Database dans le stockage Blob Azure à l’aide de la technologie Change Tracking](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Étapes suivantes
Accéder au didacticiel suivant : 

> [!div class="nextstepaction"]
>[Copier de façon incrémentielle les données d’une table d’Azure SQL Database dans un stockage Blob Azure](tutorial-incremental-copy-powershell.md)
