---
title: Approvisionner des appareils Linux pour la surveillance à distance en C - Azure| Microsoft Docs
description: Explique comment connecter un appareil à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite en C et exécutée sous Linux.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 2c6b245e938b44891314c3e752c064ff152f2c9a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce didacticiel montre comment connecter un appareil physique à la solution préconfigurée de surveillance à distance.

## <a name="create-a-c-client-project-on-linux"></a>Créer un projet de client C sous Linux

Comme avec la plupart des applications incorporées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil est écrit en C. Dans ce didacticiel, vous générez l’application sur une machine exécutant Ubuntu (Linux).

Pour effectuer ces étapes, vous avez besoin d’un appareil exécutant Ubuntu 15.04 ou une version ultérieure. Avant de continuer, installez les packages requis sur votre appareil Ubuntu à l’aide de la commande suivante :

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Installation des bibliothèques clientes sur votre appareil

Les bibliothèques clientes Azure IoT Hub sont disponibles sous la forme d’un package que vous pouvez installer sur votre appareil Ubuntu à l’aide de la commande **apt-get** . Procédez comme suit pour installer le package contenant les fichiers d’en-tête et de bibliothèque du client IoT Hub sur votre ordinateur Ubuntu :

1. Dans un interpréteur de commandes, ajoutez le référentiel AzureIoT sur votre ordinateur :

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Installation du package azure-iot-sdk-c-dev

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Installation de l’analyseur JSON Parson JSON

Les bibliothèques clientes IoT Hub utilisent l’analyseur JSON Parson pour analyser les charges utiles des messages. Dans un dossier approprié sur votre ordinateur, clonez le référentiel GitHub Parson à l’aide de la commande suivante :

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Préparation du projet

Sur votre ordinateur Ubuntu, créez un dossier nommé `remote_monitoring`. Dans le dossier `remote_monitoring` :

- Créer les quatre fichiers `main.c`, `remote_monitoring.c`, `remote_monitoring.h` et `CMakeLists.txt`.
- Créez un dossier nommé `parson`.

Copiez les fichiers `parson.c` et `parson.h` depuis votre copie locale du référentiel Parson vers le dossier `remote_monitoring/parson`.

Ouvrez le fichier `remote_monitoring.c` dans un éditeur de texte. Ajoutez les instructions `#include` suivantes :

```c
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include <string.h>
```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Ajouter du code pour exécuter l’application

Ouvrez le fichier `remote_monitoring.h` dans un éditeur de texte. Ajoutez le code suivant :

```c
void remote_monitoring_run(void);
```

Ouvrez le fichier `main.c` dans un éditeur de texte. Ajoutez le code suivant :

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Génération et exécution de l’application

Les étapes suivantes décrivent comment utiliser *CMake* pour créer votre application cliente.

1. Dans un éditeur de texte, ouvrez le fichier **CMakeLists.txt** du dossier `remote_monitoring`.

1. Ajoutez les instructions suivantes pour définir comment créer votre application cliente :

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. Dans le dossier `remote_monitoring`, créez un dossier pour stocker les fichiers *make* générés par CMake. Ensuite, exécutez les commandes **cmake** et **make** comme suit :

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Exécutez l’application cliente et envoyez les données de télémétrie à IoT Hub :

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
