---
title: "Approvisionner un appareil X.509 simulé auprès du service Azure IoT Hub à l’aide de Python | Microsoft Docs"
description: "Démarrage rapide d’Azure : Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Python Device SDK"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: e274b60c89b8e03aa8823588546fa1a97c474142
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Python Device SDK
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ces étapes indiquent comment simuler un appareil X.509 sur votre ordinateur de développement sous Windows et comment utiliser l’exemple de code Python pour connecter cet appareil simulé au service Device Provisioning et à votre hub IoT. 

Veillez à compléter les étapes décrites dans la section relative à la [configuration du service d’approvisionnement d’appareil Azure IoT Hub avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer.


## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Assurez-vous que [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) ou [Visual Studio 2017](https://www.visualstudio.com/vs/) sont bien installés sur votre ordinateur. La charge de travail « Développement Desktop en C++ » doit être activée pour l’installation de Visual Studio.

1. Téléchargez et installez le [système de génération de CMake](https://cmake.org/download/).

1. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 

1. Ouvrez une invite de commandes ou Git Bash. Clonez le référentiel GitHub pour l’exemple de code de simulation d’appareil.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Créez un dossier dans votre copie locale de ce référentiel GitHub pour le processus de génération de CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. Exécutez la commande suivante pour créer la solution Visual Studio pour le client d’approvisionnement.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-device-enrollment-entry"></a>Créer une entrée d’inscription d’appareil

1. Ouvrez la solution générée dans le dossier *cmake* nommé `azure_iot_sdks.sln` générez-la dans Visual Studio.

1. Cliquez avec le bouton droit de la souris sur le projet **dice\_device\_enrollment** dans le dossier **Provision\_Tools**, puis sélectionnez **Définir comme projet de démarrage**. Exécutez la solution. Dans la fenêtre Sortie, entrez `i` pour l’inscription individuelle lorsque vous y êtes invité. La fenêtre Sortie affiche un certificat X.509 généré localement pour votre appareil simulé. Copiez dans le Presse-papiers la sortie débutant par *-----BEGIN CERTIFICATE-----* et se terminant par *-----END CERTIFICATE-----*, en faisant bien attention à inclure également ces deux lignes. 

    ![Application de l’inscription des appareils Dice](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
1. Créez un fichier nommé **_X509testcertificate.pem_** sur votre ordinateur Windows, ouvrez-le dans un éditeur de votre choix et copiez le contenu du presse-papiers dans ce fichier. Enregistrez le fichier . 

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service d'approvisionnement.

1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles** et cliquez sur le bouton **Ajouter** dans la partie supérieure. 

1. Sous l’**entrée Ajouter la liste d’inscription**, entrez les informations suivantes :
    - Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    - Sous le *fichier .pem ou .cer du certificat*, sélectionnez le fichier de certificat **_X509testcertificate.pem_** créé au cours des étapes précédentes à l’aide du widget *Explorateur de fichiers*.
    - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        - Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil. 
        - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
    - Cela fait, cliquez sur le bouton **Enregistrer**. 

    ![Saisir les informations d’inscription pour l’appareil X.509 dans le panneau du portail](./media/python-quick-create-simulated-device-x509/enter-device-enrollment.png)  

   Lorsque l’inscription aboutit, votre appareil X.509 apparaît en tant que **riot-device-cert** sous la colonne *ID d’inscription* dans l’onglet *Inscriptions individuelles*. 


## <a name="simulate-the-device"></a>Simuler l’appareil

1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Vue d’ensemble**. Notez les valeurs _ID Scope_ (Étendue d’ID) et _Global Service Endpoint_ (Point de terminaison de service global).

    ![Informations sur le service](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Téléchargez et installez [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/).
    - Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145) pour autoriser l’utilisation de DLL natives depuis Python.

1. Suivez [ces instructions](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) pour générer les packages Python.

    > [!NOTE]
        > Si vous utilisez `pip`, assurez-vous d’installer également le package `azure-iot-provisioning-device-client`.

1. Accédez au dossier des exemples.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. À l’aide de votre environnement IDE Python, modifiez le script python nommé **provisioning\_device\_client\_sample.py**. Modifiez les variables _GLOBAL\_PROV\_URI_ et _ID\_SCOPE_ d’après les valeurs notées précédemment.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

1. Exécutez l’exemple. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. L’application va se connecter, inscrire l’appareil et afficher un message informant de la réussite de l’inscription.

    ![inscription réussie](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

1. Dans le portail, accédez au IoT Hub lié à votre service d’approvisionnement, ouvrez le panneau **Device Explorer**. En cas de réussite de l’approvisionnement de l’appareil simulé X.509 sur le Hub, son ID de périphérique s’affiche sur le panneau **Device Explorer**, avec un *ÉTAT* **activé**. Notez que vous devrez peut-être cliquer sur le bouton **Actualiser** dans la partie supérieure si vous avez déjà ouvert le panneau avant d’exécuter l’exemple d’application de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/python-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez le panneau **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez le panneau **IoT Devices** (Appareils IoT) pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un appareil X.509 simulé sur un ordinateur Windows. Vous l’avez également approvisionné vers votre hub IoT à l’aide du service Azure IoT Hub Device Provisioning figurant sur le portail. Pour savoir comment inscrire un appareil X.509 au moyen d’un programme, poursuivez avec le démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide d’Azure : Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
