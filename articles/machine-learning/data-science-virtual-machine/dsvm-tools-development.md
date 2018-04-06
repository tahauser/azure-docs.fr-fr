---
title: Outils de développement de la machine virtuelle DSVM - Azure | Microsoft Docs
description: Outils de développement de la machine virtuelle DSVM.
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 6f141fc03b64d0ca922d003f6352b7751ab9967d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Outils de développement sur la machine virtuelle DSVM

La machine virtuelle DSVM fournit un environnement de développement productif en regroupant plusieurs IDE et outils courants. Voici quelques-uns des outils disponibles sur la machine virtuelle DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE à usage général      |
| Versions DSVM prises en charge      | Windows      |
| Utilisations classiques      | Développement de logiciels    |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?      | Charge de travail Science des données (outils Python et R), charge de travail Azure (Hadoop, Data Lake), Node.js, outils SQL Server, [Visual Studio Tools pour AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Outils connexes sur la machine virtuelle DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE à usage général      |
| Versions DSVM prises en charge      | Windows, Linux     |
| Utilisations classiques      | Éditeur de code et Intégration de Git   |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) dans Windows, raccourci sur le Bureau ou terminal (`code`) dans Linux    |
| Outils connexes sur la machine virtuelle DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour R    |
| Versions DSVM prises en charge      | Windows, Linux      |
| Utilisations classiques      |  Développement R     |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files\RStudio\bin\rstudio.exe`) dans Windows, raccourci sur le Bureau (`/usr/bin/rstudio`) dans Linux      |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE basé sur le web pour R    |
| Versions DSVM prises en charge      | Linux      |
| Utilisations classiques      |  Développement R     |
| Comment l’utiliser/l’exécuter ?      | Activez le service avec _systemctl enable rstudio-server_, puis démarrez-le avec _systemctl start rstudio-server_. Vous pouvez ensuite vous connecter à RStudio Server à l’adresse http://your-vm-ip:8787.       |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage Julia   |
| Versions DSVM prises en charge      | Windows, Linux      |
| Utilisations classiques      |  Développement Julia     |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`C:\JuliaPro-0.5.1.1\Juno.bat`) dans Windows, raccourci sur le Bureau (`/opt/JuliaPro-VERSION/Juno`) dans Linux      |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | IDE client pour le langage Python    |
| Versions DSVM prises en charge      | Linux      |
| Utilisations classiques      |  Développement R     |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`/usr/bin/pycharm`) dans Linux      |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Qu’est-ce que c’est ?   | Outil de décisionnel et de visualisation interactive des données    |
| Versions DSVM prises en charge      | Windows  |
| Utilisations classiques      |  Visualisation des données et création de tableaux de bord   |
| Comment l’utiliser/l’exécuter ?      | Raccourci sur le Bureau (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Outils connexes sur la machine virtuelle DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

