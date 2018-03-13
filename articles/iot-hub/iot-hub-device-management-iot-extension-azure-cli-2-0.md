---
title: "Gestion des appareils Azure IoT avec l’extension IoT pour Azure CLI 2.0 | Microsoft Docs"
description: "Utilisez l’extension IoT de l’outil Azure CLI 2.0 pour la gestion des appareils Azure IoT Hub, avec les méthodes directes et les options de gestion des propriétés souhaitées du jumeau."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: gestion des appareils iot azure, gestion des appareils azure iot hub, gestion des appareils iot, gestion des appareils iot hub
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: e83aa590cc41abcd661e6f0fef450833c816dac4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Utiliser l’extension IoT d’Azure CLI 2.0 pour la gestion des appareils Azure IoT Hub

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[L’extension IoT pour Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) est une nouvelle extension IoT open source qui s’ajoute aux fonctionnalités [d’Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) et comprend des commandes permettant d’interagir avec Azure Resource Manager et les points de terminaison de gestion. Azure CLI 2.0 comprend des commandes permettant d’interagir avec Azure Resource Manager et les points de terminaison de gestion. Par exemple, vous pouvez l’utiliser pour créer une machine virtuelle Azure ou un hub IoT. Une extension de l’interface CLI permet à un service Azure d’enrichir l’interface CLI, donnant ainsi accès à des fonctionnalités supplémentaires propres au service. L’extension IoT offre aux développeurs IoT un accès en ligne de commande à toutes les fonctionnalités d’IoT Hub, d’IoT Edge et du service IoT Hub Device Provisioning.

| Option de gestion          | Tâche                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Méthodes directes             | Faites agir un appareil comme commençant/arrêtant d’envoyer des messages ou comme redémarrant l’appareil.                                        |
| Propriétés souhaitées pour la représentation    | Mettez un appareil dans certains états, par exemple en réglant un voyant sur le vert ou en définissant l’intervalle d’envoi de télémétrie sur 30 minutes.         |
| Propriétés signalées pour la représentation   | Obtenez l’état signalé d’un appareil. Par exemple, l’appareil signale que le voyant clignote maintenant.                                    |
| Balises de représentation                  | Stocker les métadonnées spécifiques à l’appareil dans le cloud, par exemple, l’emplacement de déploiement d’un distributeur automatique.                         |
| Requêtes de représentations d’appareil        | Interrogez toutes les représentations d’appareil pour récupérer ceux avec des conditions arbitraires, comme l’identification des appareils qui sont disponibles pour utilisation. |

Pour plus d’explications sur les différences et des conseils sur l’utilisation de ces options, consultez [l’aide sur la communication appareil-à-cloud](iot-hub-devguide-d2c-guidance.md) et [l’aide sur la communication cloud-à-appareil](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Les représentations d’appareil sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez. Pour plus d’informations sur les représentations d’appareil, consultez [Prise en main des représentations d’appareils](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Contenu

Vous allez apprendre à utiliser l’extension IoT pour Azure CLI 2.0 avec différentes options de gestion sur votre ordinateur de développement.

## <a name="what-you-do"></a>Procédure

Exécutez Azure CLI 2.0 et l’extension IoT pour Azure CLI 2.0 avec différentes options de gestion.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.

- Vérifiez que votre appareil exécute l’application cliente tout au long de ce didacticiel.

- [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

- Installez Azure CLI 2.0. Pour une installation sur Windows, le plus simple consiste à télécharger et à installer le [MSI](https://aka.ms/InstallAzureCliWindows). Vous pouvez également suivre les instructions d’installation sur [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI 2.0 dans votre environnement. La version d’Azure CLI 2.0 doit être au minimum 2.0.24. Utilisez `az –version` pour valider. 

- Installez l’extension IoT. Le plus simple consiste à exécuter `az extension add --name azure-cli-iot-ext`. Le [Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) décrit différentes manières d’installer l’extension.


## <a name="log-in-to-your-azure-account"></a>Connexion à votre compte Azure

Connectez-vous à votre compte Azure en exécutant la commande suivante :

```bash
az login
```

## <a name="direct-methods"></a>Méthodes directes

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Propriétés souhaitées du jumeau d’appareil

Définissez un intervalle de propriété = 3000 en exécutant la commande suivante :

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Cette propriété peut être lue sur votre appareil.

## <a name="device-twin-reported-properties"></a>Propriétés signalées du jumeau d’appareil

Obtenez les propriétés signalées de l’appareil en exécutant la commande suivante :

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

Une des propriétés est $metadata.$lastUpdated qui indique la dernière fois que cet appareil a envoyé ou reçu un message.

## <a name="device-twin-tags"></a>Balises du jumeau d’appareil

Affichez les balises et les propriétés de votre appareil en exécutant la commande suivante :

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Ajoutez un champ role = temperature&humidity à l’appareil en exécutant la commande suivante :

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Requêtes de représentations d’appareil

Interrogez les appareils avec une balise role = temperature&humidity en exécutant la commande suivante :

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Interrogez les appareils, à l’exception de ceux qui ont une balise role = temperature&humidity en exécutant la commande suivante :

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à analyser des messages appareil vers cloud et à envoyer des messages cloud vers appareil entre votre appareil IoT et l’instance IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]