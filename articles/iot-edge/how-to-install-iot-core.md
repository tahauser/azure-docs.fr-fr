---
title: Installer Azure IoT Edge sur IoT Standard | Microsoft Docs
description: Installer le runtime Azure IoT Edge sur un appareil Windows IoT Standard
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 03/05/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 75d2b370ed6118a30153a001a4b654d7212b56cd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installer le runtime IoT Edge sur Windows IoT Standard - préversion

Utilisés conjointement, Azure IoT Edge et [Windows IoT Core](https://docs.microsoft.com/windows/iot-core/) permettent de mettre en place une architecture de type « edge computing » (traitement des données à la périphérie du réseau) même sur des petits appareils. Le runtime Azure IoT Edge peut s’exécuter même sur les tout petits appareils SBC (Single Board Computer) qui sont très répandus dans le domaine IoT. 

Cet article explique comment provisionner le runtime sur une carte de développement [MinnowBoard Turbot][lnk-minnow] exécutant Windows IoT Standard. Windows IoT Core prend uniquement en charge Azure IoT Edge sur les processeurs Intel x64. 

## <a name="install-the-runtime"></a>Installer le runtime

1. Installez le [Tableau de bord Windows 10 IoT Standard][lnk-core] sur un système hôte.
1. Suivez les étapes fournies dans [Configurer votre appareil][lnk-board] pour configurer votre carte avec l’image MinnowBoard Turbot/MAX Build 16299. 
1. Allumez l’appareil, puis [connectez-vous à distance avec PowerShell][lnk-powershell].
1. Dans la console PowerShell, installez le runtime de conteneurs : 

   ```powershell
   Invoke-WebRequest https://master.dockerproject.org/windows/x86_64/docker-0.0.0-dev.zip -o temp.zip
   Expand-Archive .\temp.zip $env:ProgramFiles -f
   Remove-Item .\temp.zip
   $env:Path += ";$env:programfiles\docker"
   SETX /M PATH "$env:Path"
   dockerd --register-service
   start-service docker
   ```

   >[!NOTE]
   >Le runtime de conteneurs provient du serveur de builds du projet Moby et est uniquement disponible à des fins d’évaluation. Il n’est ni testé, ni approuvé, ni pris en charge par Docker.

1. Installez le runtime IoT Edge et vérifiez votre configuration :

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Ce script fournit les éléments suivants : 
   * Python 3.6
   * Le script de contrôle IoT Edge (iotedgectl.exe)

Vous verrez peut-être des informations de sortie de l’outil iotedgectl.exe en vert dans la fenêtre PowerShell à distance. Elles n’indiquent pas nécessairement des erreurs. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez un appareil exécutant le runtime IoT Edge, découvrez comment [Déployer et surveiller des modules IoT Edge à l’échelle][lnk-deploy].

<!--Links-->
[lnk-minnow]: https://minnowboard.org/ 
[lnk-core]: https://docs.microsoft.com/windows/iot-core/connect-your-device/iotdashboard
[lnk-board]: https://developer.microsoft.com/windows/iot/Docs/GetStarted/mbm/sdcard/stable/getstartedstep2
[lnk-powershell]: https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell
[lnk-deploy]: how-to-deploy-monitor.md
[lnk-docker-install]: https://docs.docker.com/engine/installation/linux/docker-ce/binaries#install-server-and-client-binaries-on-windows
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers