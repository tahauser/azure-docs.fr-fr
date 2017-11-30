---
title: "Créer un hub IoT Azure à l’aide d’un modèle (PowerShell) | Microsoft Docs"
description: "Comment utiliser un modèle Azure Resource Manager pour créer un hub IoT avec PowerShell."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 242c50b61b00bbf71b26e2aea1a66e2b2c55dbd5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Créer un hub IoT avec un modèle Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Vous pouvez utiliser Azure Resource Manager pour créer et gérer des hubs Azure IoT par programmation. Ce didacticiel vous montre comment utiliser un modèle Azure Resource Manager pour créer un hub IoT à l’aide de PowerShell.

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Azure Resource Manager.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Azure PowerShell 1.0][lnk-powershell-install] ou version ultérieure.

> [!TIP]
> L’article [Utilisation d’Azure PowerShell avec Azure Resource Manager][lnk-powershell-arm] fournit des informations supplémentaires sur l’utilisation de PowerShell et des modèles Resource Manager pour créer des ressources Azure.

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure

Dans une invite de commandes PowerShell, entrez la commande suivante pour vous connecter à votre abonnement Azure :

```powershell
Login-AzureRmAccount
```

Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la commande suivante pour répertorier les abonnements Azure que vous pouvez utiliser :

```powershell
Get-AzureRMSubscription
```

Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour créer votre hub IoT. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

Vous pouvez utiliser les commandes suivantes pour découvrir où vous pouvez déployer un hub IoT et les versions de l'API actuellement prises en charge :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Créez un groupe de ressources pour contenir votre hub IoT avec la commande suivante dans l’un des emplacements pris en charge pour le hub IoT. Cet exemple crée un groupe de ressources appelé **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Envoyer un modèle pour créer un hub IoT

Utilisez un modèle JSON pour créer un hub IoT dans votre groupe de ressources. Vous pouvez également utiliser un modèle Azure Resource Manager pour apporter des modifications à un hub IoT existant.

1. Utilisez un éditeur de texte pour créer un modèle Azure Resource Manager appelé **template.json** avec la définition de ressource suivante pour créer un hub IoT standard. Cet exemple ajoute le hub IoT dans la zone **États-Unis de l’Est**, crée deux groupes de consommateurs (**cg1** et **cg2**) sur le point de terminaison compatible Event Hubs et utilise la version de l’API **2016-02-03**. Ce modèle vous demande également de transmettre le nom de l’IoT Hub en tant que paramètre appelé **hubName**. Pour obtenir la liste des emplacements qui prennent en charge IoT Hub, voir [Statut Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Enregistrez le fichier de modèle Azure Resource Manager sur votre ordinateur local. Cet exemple suppose que vous l’enregistrez dans un dossier appelé **c:\templates**.

3. Exécutez la commande suivante pour déployer votre nouvel IoT Hub, en utilisant le nom de votre IoT Hub en tant que paramètre. Dans cet exemple, le nom du hub IoT est `abcmyiothub`. Le nom de votre hub IoT doit être globalement unique :

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. La sortie affiche les clés du hub IoT que vous avez créé.

5. Pour vérifier que votre application a bien ajouté le nouveau hub IoT, accédez au [portail Azure][lnk-azure-portal] et affichez votre liste de ressources. Vous pouvez également utiliser l’applet de commande PowerShell **Get-AzureRmResource**.

> [!NOTE]
> Cet exemple d’application ajoute un hub IoT S1 Standard qui vous est facturé. Lorsque vous avez terminé, vous pouvez supprimer le hub IoT via le [portail Azure][lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Remove-AzureRmResource**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé un hub IoT à l’aide d’un modèle Azure Resource Manager avec PowerShell, vous pouvez aller encore plus loin :

* Découvrez les fonctionnalités de l’[API REST du fournisseur de ressources IoT Hub][lnk-rest-api].
* Pour plus d’informations sur les capacités d’Azure Resource Manager, voir [Vue d’ensemble d’Azure Resource Manager][lnk-azure-rm-overview].

Pour en savoir plus sur le développement d’un hub IoT, consultez les articles suivants :

* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les fonctionnalités IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
