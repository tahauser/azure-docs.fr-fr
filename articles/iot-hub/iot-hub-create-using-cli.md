---
title: Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure (az.py) | Microsoft Docs
description: Création d’un Azure IoT Hub à l’aide de l’interface Azure CLI 2.0 (az.py).
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c89ecc31754d355d6469d54dcd45e1cc2ef106f0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Création d’un IoT Hub à l’aide de l’interface Azure CLI 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser l’interface Azure CLI 2.0 (az.py) pour créer et gérer des instances Azure IoT Hub de façon programmée. Cet article explique comment utiliser l’interface Azure CLI 2.0 (az.py) pour créer un IoT Hub.

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) : l'interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* Azure CLI 2.0 (az.py) : l’interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager, comme décrit dans cet article.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Connectez-vous à votre compte Azure et sélectionnez votre abonnement.

1. Dans l’invite de commande, exécutez la [commande login][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Suivez les instructions pour vous authentifier à l’aide du code et vous connecter à votre compte Azure via un navigateur web.

2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la [commande suivante pour répertorier les comptes Azure][lnk-az-account-command] que vous pouvez utiliser :
    
    ```azurecli
    az account list 
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour créer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Utilisez l’interface de ligne de commande Azure pour créer un groupe de ressources, puis ajoutez un IoT Hub.

1. Lorsque vous créez un IoT Hub, vous devez le créer dans un groupe de ressources. Utilisez un groupe de ressources existant, ou exécutez la [commande suivante pour en créer un][lnk-az-resource-command] :
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > L’exemple précédent crée le groupe de ressources dans l’emplacement États-Unis de l'Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant la commande `az account list-locations -o table`.
    >
    >

2. Exécutez la [commande suivante pour créer un IoT Hub][lnk-az-iot-command] dans votre groupe de ressources en utilisant un nom globalement unique pour votre IoT Hub :
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> La commande précédente crée un IoT Hub dans le niveau de tarification S1 pour lequel vous êtes facturé. Pour plus d’informations, voir la [tarification d’Azure IoT Hub][lnk-iot-pricing].
>

## <a name="remove-an-iot-hub"></a>Supprimer un hub IoT

Vous pouvez utiliser l’interface de ligne de commande Azure pour [supprimer une ressource][lnk-az-resource-command], par exemple un IoT Hub, ou un groupe de ressources et toutes ses ressources, y compris les IoT Hubs.

Pour supprimer un IoT Hub, exécutez la commande suivante :

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Pour supprimer un groupe de ressources et toutes ses ressources, exécutez la commande suivante :

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le développement pour IoT Hub, consultez les articles suivants :

* [Guide du développeur d’IoT Hub][lnk-devguide]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
