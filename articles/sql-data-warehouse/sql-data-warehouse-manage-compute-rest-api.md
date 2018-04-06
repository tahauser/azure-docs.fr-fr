---
title: Suspendre, reprendre et mettre à l’échelle avec REST dans Azure SQL Data Warehouse | Microsoft Docs
description: Gérez la puissance de calcul dans SQL Data Warehouse via les API REST.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: kfile
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2018
ms.author: barbkess
ms.openlocfilehash: 518bbe23f1dcb9ffdffcfb67f875165617762c78
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>API REST pour Azure SQL Data Warehouse
API REST pour gérer le calcul dans Azure SQL Data Warehouse.

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul
Pour modifier les unités de l’entrepôt de données, utilisez l’API REST [Créer ou mettre à jour une base de données](/rest/api/sql/databases/createorupdate). L’exemple suivant définit les unités de l’entrepôt de données sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Suspension du calcul

Pour suspendre une base de données, utilisez l’API REST [Suspendre la base de données](/rest/api/sql/databases/pause). Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est interrompue. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Reprise du calcul

Pour démarrer une base de données, utilisez l’API REST [Reprendre la base données](/rest/api/sql/databases/resume). Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est démarrée. Le serveur est un groupe de ressources Azure appelé ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Vérifier l’état de la base de données

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir [Gérer le calcul](sql-data-warehouse-manage-compute-overview.md).

