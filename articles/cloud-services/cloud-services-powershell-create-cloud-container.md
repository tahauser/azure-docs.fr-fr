---
title: "Créer un conteneur de service cloud avec PowerShell | Microsoft Docs"
description: "Cet article explique comment créer un conteneur de service cloud avec PowerShell. Le conteneur héberge les rôles Web et de travail."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utiliser une commande Azure PowerShell pour créer un conteneur de service cloud vide
Cet article explique comment créer rapidement un conteneur Cloud Services à l’aide d’applets de commande Azure PowerShell. Procédez comme suit :

1. Installez l’applet de commande Microsoft Azure PowerShell à partir de la page [Téléchargements d’Azure PowerShell](http://aka.ms/webpi-azps) .
2. Ouvrez l’invite de commandes PowerShell.
3. Utilisez l’applet de commande [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) pour vous connecter.

   > [!NOTE]
   > Pour obtenir des instructions complémentaires sur l’installation de l’applet de commande Azure PowerShell et sur la connexion à votre abonnement Azure, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Créez un conteneur de service cloud Azure vide à l’aide de l’applet de commande **New-AzureService** .

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Suivez cet exemple pour appeler l’applet de commande :

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Pour plus d’informations sur la création du service cloud Azure, exécutez :

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Étapes suivantes
* Pour gérer le déploiement du service cloud, reportez-vous aux rubriques relatives aux commandes [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0) et [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0). Pour plus d’informations, vous pouvez également consulter l’article [Configuration des services cloud](cloud-services-how-to-configure-portal.md) .
* Pour publier votre projet de service cloud dans Azure, consultez l’exemple de code **PublishCloudService.ps1** issu du [référentiel de services cloud archivé](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
