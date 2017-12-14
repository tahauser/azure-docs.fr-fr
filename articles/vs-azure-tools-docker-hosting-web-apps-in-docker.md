---
title: "Déployer un conteneur Linux Docker ASP.NET Core sur un hôte Docker distant | Microsoft Docs"
description: "Découvrez comment utiliser Visual Studio Tools pour Docker pour déployer une application web ASP.NET Core dans un conteneur Docker fonctionnant sur une machine virtuelle hôte Azure Docker"
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 60efffd9313f6972ae46fd1925d999597d3c6ba2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Déployer un conteneur ASP.NET sur un hôte Docker distant
## <a name="overview"></a>Vue d'ensemble
Docker est un moteur de conteneur léger, semblable à certains égards à une machine virtuelle, que vous pouvez utiliser pour héberger des applications et des services.
Ce didacticiel vous guide dans l’utilisation de l’extension [Visual Studio Tools pour Docker](https://docs.microsoft.com/dotnet/articles/core/docker/visual-studio-tools-for-docker) pour déployer une application ASP.NET Core sur un hôte Docker sur Azure à l’aide de PowerShell.

## <a name="prerequisites"></a>Composants requis
Ce qui suit est requis pour suivre ce didacticiel :

* Créer une machine virtuelle hôte Azure Docker, comme le décrit la rubrique [Utiliser Docker Machine avec Azure](virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Installer la dernière version de [Visual Studio](https://www.visualstudio.com/downloads/)
* Télécharger le [kit de développement logiciel (SDK) Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)
* Installer [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Créez une application web ASP.NET Core
La procédure suivante vous accompagne dans la création d’une application ASP.NET Core qui sera utilisée dans ce didacticiel.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Ajouter la prise en charge Docker
[!INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. Utilisez le Script PowerShell DockerTask.ps1
1. Ouvrez une invite de commandes PowerShell vers le répertoire racine de votre projet. 
   
   ```
   PS C:\Src\WebApplication1>
   ```
2. Confirmez l’exécution de l’hôte distant. L’état (State) doit indiquer « Running » (En cours d’exécution) 
   
   ```
   docker-machine ls
   NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
   MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
   ```
   
3. Générez l’application à l’aide du paramètre -Build
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
   ```  

   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
   > ```  
   > 
   > 
4. Exécutez l’application à l’aide du paramètre -Run
   
   ```
   PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
   ```
   
   > ```
   > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
   > ```
   > 
   > 
   
   Une fois Docker terminé, vous devriez obtenir un résultat semblable à ce qui suit :
   
   ![Affichez votre application][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
