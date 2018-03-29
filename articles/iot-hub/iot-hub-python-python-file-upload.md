---
title: Charger les fichiers d’un appareil sur Azure IoT Hub avec Python | Microsoft Docs
description: Guide pratique pour charger les fichiers d’un appareil sur le cloud avec Azure IoT device SDK pour Python. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 6f1a40f254473ae7d02168f0cfd6ad5c0d461d82
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce didacticiel explique comment utiliser les [fonctionnalités de chargement d’IoT Hub](iot-hub-devguide-file-upload.md) pour charger un fichier sur le [Stockage Blob Azure](../storage/index.yml). Ce didacticiel explique les procédures suivantes :

- Fournir en toute sécurité un conteneur de stockage pour le chargement d’un fichier
- Utiliser le client Python pour charger un fichier par le biais de votre hub IoT

Les didacticiels [Bien démarrer avec IoT Hub](iot-hub-node-node-getstarted.md) et [Envoyer des messages du cloud vers un appareil avec IoT Hub](iot-hub-node-node-c2d.md) illustrent les fonctionnalités de base de la messagerie d’un appareil vers le cloud et du cloud vers un appareil offertes par IoT Hub. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Lorsque vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez quand même exploiter la sécurité et la fiabilité d’IoT Hub.

> [!NOTE]
> À l’heure actuelle, le kit IoT Hub Python SDK prend uniquement en charge le chargement de fichiers basés sur des caractères, tels que les fichiers **.txt**.

À la fin de ce didacticiel, vous allez exécuter l’application console Python :

* **FileUpload.py**, qui charge un fichier sur le stockage à l’aide du kit Python Device SDK.

> [!NOTE]
> IoT Hub prend en charge de nombreuses plateformes d’appareils et de nombreux langages (notamment C, .NET, Javascript, Python et Java) par le biais des kits Azure IoT device SDK. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Python 2.x ou 3.x][lnk-python-download]. Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. Si vous utilisez Python 2.x, vous devrez peut-être [installer ou mettre à niveau *pip*, le système de gestion de package Python][lnk-install-pip].
* Si vous utilisez le système d’exploitation Windows, utilisez le [package redistribuable Visual C++][lnk-visual-c-redist] pour autoriser l’utilisation de DLL natives de Python.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cette section, créez l’application d’appareil pour charger un fichier sur IoT Hub.

1. À partir de votre invite de commandes, exécutez la commande suivante pour installer le package **azure-iothub-device-client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. À l’aide d’un éditeur de texte, créez un fichier **FileUpload.py** dans votre dossier de travail.

1. Ajoutez les variables et instructions `import` ci-dessous au début du fichier **FileUpload.py**. Remplacez `deviceConnectionString` par la chaîne de connexion de l’appareil de votre hub IoT :

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name on storage after upload]"
    ```

1. Créez un rappel pour la fonction **upload_blob** :

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Ajoutez le code suivant pour connecter le client et charger le fichier. Ajoutez également la routine `main` :

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. Enregistrez et fermez le fichier **UploadFile.py**.

1. Copiez un exemple de fichier texte dans le dossier de travail et renommez-le `sample.txt`.

    > [!NOTE]
    > À l’heure actuelle, le kit IoT Hub Python SDK prend uniquement en charge le chargement de fichiers basés sur des caractères, tels que les fichiers **.txt**.


## <a name="run-the-application"></a>Exécution de l'application

Vous êtes maintenant prêt à exécuter l’application.

1. À partir d’une invite de commandes dans votre dossier de travail, exécutez la commande suivante :

    ```cmd/sh
    python FileUpload.py
    ```

1. La capture d’écran ci-dessous montre la sortie de l’application **FileUpload** :

    ![Sortie de l’application simulated-device](./media/iot-hub-python-python-file-upload/1.png)

1. Vous pouvez utiliser le portail pour afficher le fichier chargé dans le conteneur de stockage que vous avez configuré :

    ![Fichier chargé](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programme][lnk-create-hub]
* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

<!-- Links -->
[Centre de développement Azure IoT]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
