---
title: Qu’est-il arrivé à mon projet service cloud ? | Microsoft Docs
description: Décrit ce qui se produit dans un projet services cloud une fois que vous vous connectez à un compte de stockage Azure à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8bbb87c313957ee697866a82ddec4c61d1420939
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il arrivé à mon projet services cloud (service connecté Azure Storage de Visual Studio) ?
## <a name="references-added"></a>Références ajoutées
Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour Azure Storage ajoutée
Les éléments ont été créés avec la clé et la chaîne de connexion du compte de stockage sélectionné. Des modifications ont été apportées aux fichiers suivants :

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

