---
title: Événement de début de suppression de pool Azure Batch | Microsoft Docs
description: Référence pour l’événement de début de suppression de pool Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 8737b9ff6452730ff5a55fa7324e37f0fe715433
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-start-event"></a>Événement de début de suppression de pool

 Cet événement est émis quand une opération de suppression du pool a commencé. Étant donné que la suppression de pool est un événement asynchrone, vous pouvez vous attendre à ce qu’un événement de fin de suppression de pool soit émis au terme de l’opération de suppression.

 L’exemple suivant montre le corps d’un événement de début de suppression de pool.

```
{
    "id": "myPool1"
}
```

|Élément|Type|Notes|
|-------------|----------|-----------|
|id|Chaîne|ID du pool.|