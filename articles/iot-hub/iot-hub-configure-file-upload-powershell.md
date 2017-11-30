---
title: Utilisation de Azure PowerShell pour configurer le chargement du fichier | Microsoft Docs
description: "Comment utiliser les applets de commande Azure PowerShell pour configurer votre IoT Hub afin d’activer les téléchargements de fichiers à partir d’appareils connectés. Comprend des informations sur la configuration du compte de stockage Azure de destination."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: c6966fd4a60681643c2a690013035bde20abee78
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurer les chargements de fichiers IoT Hub à l’aide de Powershell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Pour utiliser la [fonctionnalité de chargement de fichiers dans IoT Hub][lnk-upload], vous devez d’abord associer un compte de stockage Azure à votre IoT Hub. Vous pouvez utiliser un compte de stockage existant ou en créer un nouveau.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Applets de commande Azure PowerShell][lnk-powershell-install].
* Un IoT Hub Azure. Si vous n’avez pas de IoT Hub, vous pouvez utiliser [l’applet de commande New-AzureRmIoTHub][lnk-powershell-iothub] afin d’en créer un ou utiliser le portail pour [créer un IoT Hub][lnk-portal-hub].
* Un compte de stockage Azure. Si vous n’avez pas de compte de stockage Azure, vous pouvez utiliser [l’applet de commande PowerShell de stockage Azure][lnk-powershell-storage] afin d’en créer un ou utiliser le portail pour [créer un compte de stockage][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Vous connecter à votre compte Azure et sélectionner votre abonnement.

1. À l’invite PowerShell, exécutez l’applet de commande **Login-AzureRmAccount** :

    ```powershell
    Login-AzureRmAccount
    ```

1. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la commande suivante pour répertorier les abonnements Azure que vous pouvez utiliser :

    ```powershell
    Get-AzureRMSubscription
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour gérer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Récupérez vos détails du compte de stockage

Les étapes suivantes supposent que vous avez créé votre compte de stockage à l’aide du modèle de déploiement de **Resource Manager** et non à partir du modèle de déploiement **classique**.

Pour configurer les chargements de fichiers en provenance de vos appareils, vous avez besoin de la chaîne de connexion d’un compte de stockage Azure. Le compte de stockage doit être situé dans le même abonnement que votre IoT Hub. Vous avez également besoin du nom d’un conteneur d’objets blob dans le compte de stockage. Utilisez la commande suivante pour récupérer vos clés de compte de stockage :

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Prenez note de la valeur de clé de compte de stockage **key1** . Vous en aurez besoin dans les étapes suivantes.

Vous pouvez utiliser un conteneur d’objets blob existant pour les chargements de fichiers ou en créer un nouveau :

* Pour répertorier les conteneurs d’objets blob existants dans votre compte de stockage, utilisez les commandes suivantes :

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* Pour créer un conteneur d’objet blob dans votre compte de stockage, utilisez les commandes suivantes :

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configuration de votre IoT Hub

Vous pouvez désormais configurer votre IoT Hub pour activer la [fonctionnalité de chargement de fichiers][lnk-upload] à l’aide des détails de votre compte de stockage.

La configuration requiert les valeurs suivantes :

**Conteneur de stockage** : Un conteneur d’objets blob dans un compte de stockage Azure de votre abonnement Azure actuel à associer à votre IoT Hub. Vous avez extrait les informations de compte de stockage nécessaires dans la section précédente. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

**Recevoir des notifications pour les fichiers chargés** : activez ou désactivez les notifications de chargement de fichiers.

**SAS TTL**: ce paramètre est la durée de vie des URI de signature d’accès partagé renvoyés à l’appareil par IoT Hub. Défini sur 1 heure par défaut.

**Durée de vie par défaut des paramètres de notification de fichiers**: la durée de vie d’une notification de chargement avant son expiration. Défini sur 1 jour par défaut.

**Nombre maximal de remises de notifications de fichier**: le nombre de tentatives de remise d’une notification de chargement de fichier par l’IoT Hub. Défini sur 10 par défaut.

Utilisez l’applet de commande PowerShell suivant pour configurer les paramètres de chargement sur votre IoT Hub :

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités de chargement de fichiers d’IoT Hub, consultez [Chargements de fichiers avec IoT Hub][lnk-upload].

Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gérer en bloc des appareils IoT][lnk-bulk]
* [Métriques d’IoT Hub][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Déploiement d’une IA sur des appareils de périphérie avec Azure IoT Edge][lnk-iotedge]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md