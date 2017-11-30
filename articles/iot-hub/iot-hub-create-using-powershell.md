---
title: "Créer un IoT Hub Azure à l’aide d’une cmdlet PowerShell | Microsoft Docs"
description: "Comment utiliser une cmdlet PowerShell pour créer un IoT Hub."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1d22e1a20d18e06686f893b1f4ae22e6373633b3
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Créez un IoT Hub à l’aide de la cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser les cmdlets Azure PowerShell pour créer et gérer des IoT Hubs Azure. Ce didacticiel vous montre comment créer un IoT Hub à l’aide de PowerShell.

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Azure Resource Manager.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Applets de commande Azure PowerShell][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure
Dans une invite de commandes PowerShell, entrez la commande suivante pour vous connecter à votre abonnement Azure :

```powershell
Login-AzureRmAccount
```

Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la commande suivante pour répertorier les abonnements Azure que vous pouvez utiliser :

```powershell
Get-AzureRMSubscription
```

Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour créer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Vous avez besoin d’un groupe de ressources pour déployer un IoT Hub. Vous pouvez utiliser un groupe de ressources existant ou en créer un.

Vous pouvez utiliser la commande suivante pour détecter les emplacements où vous pouvez déployer un IoT Hub :

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Pour créer un groupe de ressources pour votre IoT Hub dans l’un des emplacements pris en charge pour l’IoT Hub, utilisez la commande suivante. Cet exemple crée un groupe de ressources appelé **MyIoTRG1** dans la région **États-Unis de l’Est** :

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Pour créer un IoT Hub dans le groupe de ressources que vous avez créé à l’étape précédente, utilisez la commande suivante. Cet exemple crée un hub **S1** appelé **MyTestIoTHub** dans la région **États-Unis de l’Est** :

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Le nom du IoT Hub doit être unique.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Vous pouvez afficher tous les IoT Hubs de votre abonnement à l’aide de la commande suivante :

```powershell
Get-AzureRmIotHub
```

L’exemple précédent ajoute un IoT Hub S1 Standard qui vous est facturé. Vous pouvez supprimer l’IoT Hub à l’aide de la commande suivante :

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Vous pouvez également supprimer un groupe de ressources et toutes les ressources qu’il contient à l’aide de la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé un IoT Hub à l’aide d’une cmdlet PowerShell, vous pouvez aller encore plus loin :

* Découvrez d’autres [cmdlets PowerShell pour travailler avec votre IoT Hub][lnk-iothub-cmdlets].
* Découvrez les capacités de [l’API REST du fournisseur de ressources IoT Hub][lnk-rest-api].

Pour en savoir plus sur le développement pour IoT Hub, consultez les articles suivants :

* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
