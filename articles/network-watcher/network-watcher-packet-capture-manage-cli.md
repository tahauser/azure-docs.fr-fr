---
title: "Gérer les captures de paquets avec Azure Network Watcher - Azure CLI 2.0 | Microsoft Docs"
description: "Cette page explique comment gérer la fonctionnalité de capture de paquets de Network Watcher à l’aide d’Azure CLI 2.0"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 2c0cb9b72d23f46e60c96efe96a9ad32ba6fc746
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-20"></a>Gérer les captures de paquets avec Azure Network Watcher à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [API REST Azure](network-watcher-packet-capture-manage-rest.md)

La fonctionnalité de capture des paquets Network Watcher vous permet de créer des sessions de capture afin d’effectuer le suivi du trafic en direction et en provenance d’une machine virtuelle. Les filtres sont fournis pour la session de capture afin de vous garantir que vous capturez uniquement le trafic souhaité. La capture des paquets permet de diagnostiquer les anomalies réseau de manière proactive et réactive. Elle permet aussi de collecter des statistiques réseau, d’obtenir des informations sur les intrusions, de déboguer des communications client-serveur, etc. En déclenchant à distance des captures de paquets, cette fonctionnalité simplifie l’exécution manuelle de la capture de paquets sur l’ordinateur souhaité, ce qui permet d’économiser un temps précieux.

Dans cet article, notre CLI nouvelle génération, Azure CLI 2.0, est utilisée pour le modèle de déploiement de gestion des ressources. Celle-ci est disponible pour Windows, Mac et Linux.

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

Cet article passe en revue les différentes tâches de gestion actuellement disponibles pour la capture de paquets.

- [**Démarrer une capture de paquets**](#start-a-packet-capture)
- [**Arrêter une capture de paquets**](#stop-a-packet-capture)
- [**Supprimer une capture de paquets**](#delete-a-packet-capture)
- [**Télécharger une capture de paquets**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Avant de commencer

Cet article part du principe que vous disposez des ressources suivantes :

- Une instance de Network Watcher dans la région où vous souhaitez créer une capture de paquets
- Une machine virtuelle avec l’extension de capture de paquets activée.

> [!IMPORTANT]
> La capture de paquets nécessite l’exécution d’un agent sur la machine virtuelle. L’Agent est installé en tant qu’extension. Pour obtenir des instructions sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](../virtual-machines/windows/extensions-features.md).

## <a name="install-vm-extension"></a>Installer une extension de machine virtuelle

### <a name="step-1"></a>Étape 1 :

Exécutez l’applet de commande `az vm extension set` pour installer l’agent de capture de paquets sur la machine virtuelle invitée.

Pour les machines virtuelles Windows :

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Pour les machines virtuelles Linux :

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
````

### <a name="step-2"></a>Étape 2

Pour vous assurer que l’agent est installé, exécutez l’applet de commande `vm extension show` et transmettez-lui le groupe de ressources ainsi que le nom de la machine virtuelle. Vérifiez la liste obtenue afin de vous assurer que l’agent est bien installé.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

L’exemple suivant montre la réponse à l’exécution de `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Démarrer une capture de paquets

Une fois les étapes précédentes terminées, l’agent de capture de paquets est installé sur la machine virtuelle.

### <a name="step-1"></a>Étape 1 :

L’étape suivante consiste à récupérer l’instance Network Watcher. Le nom du Network Watcher est transmis à l’applet de commande `az network watcher show` à l’étape 4.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>Étape 2

Récupérez un compte de stockage. Ce compte de stockage permet de stocker le fichier de capture de paquets.

```azurecli
azure storage account list
```

### <a name="step-3"></a>Étape 3

Des filtres peuvent être utilisés pour limiter les données stockées par la capture des paquets. L’exemple suivant présente la configuration d’une capture de paquets à l’aide de plusieurs filtres.  Les trois premiers filtres collectent le trafic TCP sortant uniquement à partir de l’adresse IP locale 10.0.0.3 vers les ports de destination 20, 80 et 443.  Le dernier filtre collecte uniquement le trafic UDP.

```azurecli
az network watcher packet-capture create --resource-group {resoureceurceGroupName} --vm {vmName} --name packetCaptureName --storage-account gwteststorage123abc --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

L’exemple suivant est la sortie attendue de l’exécution de l’applet de commande `az network watcher packet-capture create`.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Obtenir une capture de paquets

L’exécution de l’applet de commande `az network watcher packet-capture show` extrait le statut d’une capture de paquets en cours d’exécution ou terminée.

```azurecli
az network watcher packet-capture show --name packetCaptureName --location westcentralus
```

L’exemple suivant présente la sortie de l’applet de commande `az network watcher packet-capture show`. L’exemple suivant est postérieur à la capture. La valeur PacketCaptureStatus est Stopped, et la valeur StopReason est TimeExceeded. Cela indique que la capture de paquets a réussi et s’est correctement exécutée.

```
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/providers/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapt
ure_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="stop-a-packet-capture"></a>Arrêter une capture de paquets

Lors de l’exécution de l’applet de commande `az network watcher packet-capture stop`, toute session de capture en cours est arrêtée.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> L’applet de commande ne retourne aucune réponse lorsqu’elle est exécutée dans une session de capture en cours d’exécution ou dans une session existante déjà arrêtée.

## <a name="delete-a-packet-capture"></a>Supprimer une capture de paquets

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> La suppression d’une capture de paquets ne supprime pas le fichier dans le compte de stockage.

## <a name="download-a-packet-capture"></a>Télécharger une capture de paquets

Une fois votre session de capture de paquets terminée, le fichier de capture peut être téléchargé vers le stockage d’objets blob ou dans un fichier local sur la machine virtuelle. L’emplacement de stockage de la capture de paquets est défini lors de la création de la session. L’explorateur de stockage Microsoft Azure est un outil très pratique pour accéder à ces fichiers de capture enregistrés dans un compte de stockage. Vous pouvez le télécharger ici : http://storageexplorer.com/

Si un compte de stockage est spécifié, les fichiers de capture de paquets sont enregistrés dans un compte de stockage à l’emplacement suivant :

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment automatiser les captures de paquets avec des alertes de machine virtuelle en consultant [Create an alert triggered packet capture (Créer une capture de paquets déclenchée par alerte)](network-watcher-alert-triggered-packet-capture.md)

Recherchez si certains types de trafic sont autorisés au sein ou en dehors de votre machine virtuelle en consultant [Check IP flow verify (Vérifier les flux IP)](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
