---
title: Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ? | Microsoft Docs
description: Décrit ce qui s’est produit dans un projet de tâche web une fois que vous vous connectez à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b7bebd7801e102b9a3173841ce2289ac575cd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ?
## <a name="references-added"></a>Références ajoutées
Le package NuGet Azure Storage a été ajouté ou mis à jour dans votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier App.config de votre projet, les entrées **AzureWebJobsStorage** et **AzureWebJobsDashboard** ont été mises à jour avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, consultez [Ressources de documentation relatives à Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

