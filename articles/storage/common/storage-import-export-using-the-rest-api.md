---
title: "Utilisation de l’API REST du service Azure Import/Export | Microsoft Docs"
description: "Découvrez où se trouvent les ressources pour l’utilisation de l’API REST du service Azure Import/Export, y compris des documents de référence et de procédure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Utilisation de l’API REST du service Azure Import/Export

Le service Microsoft Azure Import/Export expose une API REST pour activer le contrôle par programme des travaux d’importation/exportation. Vous pouvez utiliser l’API REST pour exécuter toutes les opérations d’importation/exportation que vous pouvez effectuer avec le [portail Azure](https://portal.azure.com/). Par ailleurs, vous pouvez utiliser l’API REST pour effectuer certaines opérations granulaires, comme le pourcentage d’achèvement d’un travail, qui ne sont pas disponibles actuellement dans le portail Azure.

Consultez [Transfert de données vers le Stockage Blob à l’aide du service Microsoft Azure Import/Export](../storage-import-export-service.md) pour obtenir une présentation du service Import/Export et un didacticiel qui explique comment utiliser le portail Azure afin de créer et gérer les travaux d’importation et d’exportation.

## <a name="service-endpoints"></a>Points de terminaison de service

Le service Azure Import/Export est un fournisseur de ressources pour Azure Resource Manager et fournit un ensemble d’API REST au point de terminaison HTTPS suivant pour gérer les travaux d’importation/exportation :

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Contrôle de version

Les demandes au service d’importation/exportation doivent spécifier le paramètre `api-version` et définir sa valeur sur `2016-11-01`.

## <a name="importexport-service-operations"></a>Opérations du service Import/Export

[Création d’un travail d’importation](../storage-import-export-creating-an-import-job.md)

[Création d’un travail d’exportation](../storage-import-export-creating-an-export-job.md)

[Récupération des informations d’état d’un travail](storage-import-export-retrieving-state-info-for-a-job.md)

[Énumération des travaux](../storage-import-export-enumerating-jobs.md)

[Annulation et suppression de travaux](storage-import-export-cancelling-and-deleting-jobs.md)

[Sauvegarde de manifestes de lecteur](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostic et récupération d’erreur pour les travaux d’importation / exportation](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Étapes suivantes

* [API REST d’importation/exportation de stockage](/rest/api/storageimportexport)
