---
title: "Comment utiliser Azure CLI 2.0 et l’extension IoT pour gérer les services Device Provisioning | Microsoft Docs"
description: "Apprendre à utiliser Azure CLI 2.0 et l’extension IoT pour gérer les services Device Provisioning"
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>Comment utiliser Azure CLI 2.0 et l’extension IoT pour gérer les services Device Provisioning

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) est un outil à ligne de commande open source et multiplateforme, destiné à la gestion des ressources Azure telles que IoT Edge. Azure CLI 2.0 est disponible sur Windows, Linux et Mac OS. Azure CLI 2.0 vous permet de gérer les ressources Azure IoT Hub, les instances de service Device Provisioning et les hubs liés dès l’installation.

L’extension IoT enrichit Azure CLI 2.0 avec des fonctionnalités telles que la gestion des périphériques et la fonctionnalité complète de IoT Edge.

Dans ce didacticiel, vous commencez par les étapes de configuration de Azure CLI 2.0 et de l’extension IoT. Puis, vous découvrez comment exécuter des commandes CLI afin d’effectuer des opérations de service Device Provisioning. 

## <a name="installation"></a>Installation 

### <a name="step-1---install-python"></a>Étape 1 - Installez Python

[Python 2.7x ou Python 3.x](https://www.python.org/downloads/) est nécessaire.

### <a name="step-2---install-azure-cli-20"></a>Étape 2 - Installez Azure CLI 2.0

Suivez les [instructions d’installation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) pour configurer Azure CLI 2.0 dans votre environnement. Votre version de Azure CLI 2.0 doit être au minimum la version 2.0.24, ou une version ultérieure. Utilisez `az –version` pour valider. Cette version prend en charge les commandes d’extension az et introduit l’infrastructure de la commande Knack. Une méthode d’installation simple sur Windows consiste à télécharger et installer le [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Étape 3 - Installez l’extension IoT

[Le fichier Lisez-moi de l’extension IoT](https://github.com/Azure/azure-iot-cli-extension) décrit plusieurs manières d’installer l’extension. La façon la plus simple consiste à exécuter `az extension add --name azure-cli-iot-ext`. Après l’installation, vous pouvez utiliser `az extension list` pour valider les extensions actuellement installées ou `az extension show --name azure-cli-iot-ext` pour afficher les détails concernant l’extension IoT. Pour supprimer l’extension, vous pouvez utiliser `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Opérations de base du service Device Provisioning
L’exemple vous montre comment vous connecter à votre compte Azure, créer un groupe de ressources Azure (un conteneur contenant les ressources associées à une solution Azure), créer un IoT Hub, créer un service Device Provisioning, faire la liste des services Device Provisioning existants et créer un hub IoT lié avec les commandes CLI. 

Effectuez les étapes d’installation décrites précédemment avant de commencer. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?v=17.39a) dès maintenant. 


### <a name="1-log-in-to-the-azure-account"></a>1. Se connecter au compte Azure
  
    az login

![se connecter][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Créer un groupe de ressources IoTHubBlogDemo dans eastus

    az group create -l eastus -n IoTHubBlogDemo

![Créer un groupe de ressources][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Créer deux services Device Provisioning

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Créer un service DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Faire la liste de tous les services Device Provisioning sous ce groupe de ressources

    az iot dps list --resource-group IoTHubBlogDemo

![Faire la liste des services DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Créer un hub IoT blogDemoHub sous le groupe de ressources nouvellement créé

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Créer un hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Lier un hub IoT existant à un service Device Provisioning

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Lier un hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Inscrire l’appareil
> * Démarrer l’appareil
> * Vérifier que l’appareil est enregistré

Passez au didacticiel suivant pour apprendre à approvisionner plusieurs appareils sur des hubs à charge équilibrée. 

> [!div class="nextstepaction"]
> [Approvisionner des appareils sur des hubs IoT à charge équilibrée](./tutorial-provision-multiple-hubs.md)
