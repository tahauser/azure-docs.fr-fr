---
title: Installer Azure IoT Edge sur IoT Standard | Microsoft Docs
description: Installer le runtime Azure IoT Edge sur un appareil Windows IoT Standard
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: veyalla
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: be2a80645d23e709d6c5cfb3978498bbe85eca34
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="install-the-iot-edge-runtime-on-windows-iot-core---preview"></a>Installer le runtime IoT Edge sur Windows IoT Standard - préversion

Le runtime Azure IoT Edge peut s’exécuter même sur les tout petits appareils SBC (Single Board Computer) qui sont très répandus dans le domaine IoT. Cet article explique comment provisionner le runtime sur une carte de développement [MinnowBoard Turbot][lnk-minnow] exécutant Windows IoT Standard.

## <a name="install-the-runtime"></a>Installer le runtime

1. Installez le [Tableau de bord Windows 10 IoT Standard][lnk-core] sur un système hôte.
1. Suivez les étapes fournies dans [Configurer votre appareil][lnk-board] pour configurer votre carte avec l’image MinnowBoard Turbot/MAX Build 16299. 
1. Allumez l’appareil, puis [connectez-vous à distance avec PowerShell][lnk-powershell].
1. Dans la console PowerShell, [installez les binaires Docker][lnk-docker-install].
1. Exécutez la commande suivante dans la console PowerShell pour installer le runtime IoT Edge et vérifier votre configuration :

   ```powershell
   Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
   ```

   Ce script fournit les éléments suivants : 
   * Python 3.6
   * Le script de contrôle IoT Edge (iotedgectl.exe)

Vous verrez peut-être des informations de sortie de l’outil iotedgectl.exe en rouge dans la fenêtre PowerShell à distance. Elles n’indiquent pas nécessairement des erreurs. 

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