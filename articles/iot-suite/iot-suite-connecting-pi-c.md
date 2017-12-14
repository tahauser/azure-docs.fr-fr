---
title: "Provisionner un appareil Raspberry Pi pour la surveillance à distance à l’aide de C - Azure| Microsoft Docs"
description: "Explique comment connecter un appareil Raspberry Pi à la solution de surveillance à distance Azure IoT Suite préconfigurée avec une application écrite en C."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: cec5d9c2e81e6311514536f7605777d48d1f1c46
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Connecter votre appareil Raspberry Pi à la solution préconfigurée de surveillance à distance (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce didacticiel montre comment connecter un appareil physique à la solution préconfigurée de surveillance à distance. Comme avec la plupart des applications embarquées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil Raspberry Pi est écrit en C. Dans ce didacticiel, vous générez l’application sur un appareil Raspberry Pi exécutant le système d’exploitation Raspbian.

### <a name="required-hardware"></a>Matériel requis

Un ordinateur de bureau permettant de vous connecter à distance à la ligne de commande sur le Raspberry Pi.

[Starter Kit Microsoft IoT pour Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou composants équivalents. Ce didacticiel utilise les éléments suivants du kit :

- Raspberry Pi 3
- Carte MicroSD (avec NOOBS)
- Un câble mini USB
- Un câble Ethernet

### <a name="required-desktop-software"></a>Logiciels de bureau requis

Vous devez installer le client SSH sur votre ordinateur de bureau afin de pouvoir accéder à distance à la ligne de commande sur le Raspberry Pi.

- Windows n’inclut pas de client SSH. Nous vous recommandons d’utiliser [PuTTY](http://www.putty.org/).
- La plupart des distributions Linux et Mac OS incluent l’utilitaire de ligne de commande SSH. Pour plus d’informations, consultez [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Utilisation de SSH avec Linux ou Mac OS).

### <a name="required-raspberry-pi-software"></a>Logiciels Raspberry Pi requis

Les étapes suivantes vous montrent comment préparer votre appareil Raspberry Pi pour générer une application C qui se connecte à la solution préconfigurée :

1. Connectez-vous à votre appareil Raspberry Pi avec `ssh`. Pour plus d’informations, consultez [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) sur le [site web de Raspberry Pi](https://www.raspberrypi.org/).

1. Pour mettre à jour votre Raspberry Pi, utilisez la commande suivante :

    ```sh
    sudo apt-get update
    ```

1. Utilisez la commande suivante pour ajouter les outils et les bibliothèques de développement requis à votre appareil Raspberry Pi :

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. Utilisez les commandes suivantes pour installer les bibliothèques clientes IoT Hub :

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. Clonez l’analyseur Parson JSON sur votre appareil Raspberry Pi à l’aide des commandes suivantes :

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>Création d’un projet

Effectuez les étapes suivantes en utilisant la connexion `ssh` à votre appareil Raspberry Pi :

1. Créez un dossier appelé `remote_monitoring` dans votre dossier de base sur l’appareil Raspberry Pi. Accédez à ce dossier dans votre ligne de commande :

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Créez les quatre fichiers `main.c`, `remote_monitoring.c`, `remote_monitoring.h` et `CMakeLists.txt` dans le dossier `remote_monitoring`.

1. Créez un dossier nommé `parson` dans le dossier `remote_monitoring`.

1. Copiez les fichiers `parson.c` et `parson.h` depuis votre copie locale du dépôt Parson vers le dossier `remote_monitoring/parson`.

1. Ouvrez le fichier `remote_monitoring.c` dans un éditeur de texte. Sur l’appareil Raspberry Pi, vous pouvez utiliser l’éditeur de texte `nano` ou `vi`. Ajoutez les instructions `#include` suivantes :

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
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
