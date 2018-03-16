---
title: "Utilisation de l’interface de ligne de commande Azure sur Windows | Microsoft Docs"
description: "Utilisation de l’interface de ligne de commande Azure sur Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
ms.openlocfilehash: e54ea62b7b7a7adb4874866a540ed9a6a097fc42
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="using-the-azure-cli-on-windows"></a>Utilisation de l’interface de ligne de commande Azure sur Windows

L’interface de ligne de commande Azure (CLI) fournit un environnement de script et de ligne de commande pour la création et la gestion des ressources Azure. Azure CLI est disponible pour les systèmes d’exploitation Windows, Linux et Mac OS. Dans ces systèmes d’exploitation, les commandes CLI sont identiques, mais la syntaxe de script peut varier.

Ce document détaille la façon dont l’interface de ligne de commande Azure peut être installée et exécutée sur Windows, ainsi que sa syntaxe. Pour en savoir plus sur Azure CLI, voir la [documentation Azure CLI]( https://docs.microsoft.com/cli/azure).

## <a name="windows-subsystem-for-linux"></a>Sous-système Windows pour Linux

Le sous-système Windows pour Linux fournit un environnement Linux Ubuntu sur la Mise à jour anniversaire Microsoft Windows 10 et les éditions ultérieures. Une fois activé, le sous-système Windows pour Linux offre une expérience Bash native qui permet de créer et d’exécuter des scripts Azure CLI. Étant donné que le sous-système Windows pour Linux offre une expérience Bash native, les scripts Azure CLI peuvent être partagés par Mac OS, Linux et Windows sans nécessiter de modification.

Pour utiliser l’interface de ligne de commande Azure dans le sous-système Windows pour Linux, procédez comme suit.

|Tâche | Instructions |
|---|---|
| Activer le sous-système Windows pour Linux | [Documentation d’installation du sous-système Windows pour Linux](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Installer l’interface de ligne de commande Microsoft Azure |[Installer l’interface de ligne de commande sur le sous-système Windows pour Linux/Ubuntu 14.04](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

L’interface de ligne de commande Azure peut être exécutée en mode natif dans Windows. Dans cette configuration, le package Azure CLI est installé sur le système d’exploitation Windows, et les commandes peuvent être exécutées à partir de PowerShell. Dans cette configuration, les scripts et les commandes CLI Azure peuvent être exécutés sur n’importe quelle version prise en charge de Windows. Cependant, une syntaxe de script spécifique à la plateforme est nécessaire. C’est pourquoi les scripts ne peuvent pas toujours être partagés par Mac OS, Linux et Windows sans nécessiter de modification.

Pour utiliser l’interface de ligne de commande Azure sur Windows, installez le package en suivant les instructions d’[installation de l’interface de ligne de commande sur Windows](https://docs.microsoft.com/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Image Docker

Lorsque vous utilisez Docker pour Windows, vous pouvez démarrer une image Docker incluant l’interface de ligne de commande Azure. Cette image est basée sur Linux et inclut une expérience Bash native.  Lorsque vous utilisez Docker pour Windows et l’image de l’interface de ligne de commande Azure, les scripts peuvent être partagés par Windows, Linux et Mac OS. 

Pour utiliser l’interface de ligne de commande Azure sur Docker pour Windows, assurez-vous que Docker pour Windows est en cours d’exécution, puis exécutez la commande suivante.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Une fois cette opération terminée, une session Bash préchargée avec les outils Azure CLI démarre.

## <a name="next-steps"></a>Étapes suivantes

[Exemple de commande pour les machines virtuelles Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Exemples de commande pour Azure Web Apps](../../app-service/app-service-cli-samples.md)

[Exemples de commande pour Azure SQL](../../sql-database/sql-database-cli-samples.md)
