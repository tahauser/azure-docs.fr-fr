---
title: "Guide pratique pour configurer Azure Machine Learning Workbench afin qu’il fonctionne avec un IDE  | Microsoft Docs"
description: "Guide pour configurer Azure Machine Learning Workbench afin qu’il fonctionne avec votre IDE."
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 8a4bb8d825f89b08e9b7514d67ae2d2512cceec9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Guide pratique pour configurer Azure Machine Learning Workbench afin qu’il fonctionne avec un IDE 

Vous pouvez configurer Azure Machine Learning Workbench afin qu’il fonctionne avec les environnements de développement intégré (IDE) Python populaires. Il procure une expérience de développement de science des données fluide allant de la préparation des données à l’opérationnalisation, en passant par la création du code et le suivi des exécutions. Les IDE pris en charge sont les suivants :
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Configurer Workbench
1. Cliquez sur le menu **Fichier** dans le coin supérieur gauche de l’application. 
2. Sélectionnez l’option **Configurer un IDE projet** dans le menu volant. 
3. Entrez `VS Code` ou `PyCharm` dans le champ **Nom** (le nom est arbitraire).
4. Entrez l’emplacement de l’exécutable de l’IDE (nom du fichier exécutable et extension compris) dans **Chemin d’exécution**.

### <a name="default-install-path-for-visual-studio-code"></a>Chemin d’installation par défaut de Visual Studio Code  

* Windows 32 bits : `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64 bits : `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS : sélectionnez le chemin .app, par exemple `/Applications/Visual Studio Code.app` ; ensuite, l’application ajoute le reste du chemin automatiquement. Par défaut, le chemin complet du fichier exécutable est `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Si vous avez exécuté la commande `Shell Command: Install 'code' command in PATH` dans Visual Studio Code, vous pouvez également référencer le script Visual Studio Code à l’emplacement `/usr/local/bin/code`.

### <a name="default-install-path-for-pycharm"></a>Chemin d’installation par défaut de PyCharm 

* Windows 32 bits : `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe` 
* Windows 64 bits : `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`
* macOS : sélectionnez le chemin .app, par exemple « /Applications/PyCharm CE.app » ; ensuite, l’application ajoute le reste du chemin automatiquement. Par défaut, le chemin complet du fichier exécutable est `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. PyCharm peut également se trouver dans le dossier bin, `/usr/local/bin/charm`.

## <a name="open-project-in-ide"></a>Ouvrir un projet dans l’IDE 
Une fois la configuration effectuée, vous pouvez ouvrir un projet Azure Machine Learning en ouvrant le menu **Fichier** dans Azure Machine Learning Workbench, puis en cliquant sur **Ouvrir le projet (<nom_IDE>)**. Cette action ouvre le projet actif en cours dans l’IDE configuré. _Remarque : si vous n’êtes pas dans un projet, l’option **Ouvrir le projet (<nom_IDE>)** est désactivée._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Configuration du terminal intégré dans Visual Studio Code

### <a name="windows"></a>Windows 
Nous avons remplacé l’interpréteur de commandes par défaut PowerShell par cmd. Quand vous cliquez sur **Ouvrir le projet (<nom_IDE>)**, une invite de commandes apparaît : 

_Autorisez-vous le lancement de l’interpréteur de commandes `C:\windows\System32\cmd.exe` (défini en tant que paramètre d’espace de travail) dans le terminal ?_

Répondez `yes` pour autoriser la configuration de l’interpréteur de commandes afin qu’il fonctionne de manière fluide avec l’interface de ligne de commande d’Azure Machine Learning Workbench.

### <a name="mac"></a>Mac
Pour exécuter une commande `az` à l’aide du terminal intégré de Visual Studio Code sur Mac, vous devez définir manuellement `PATH` sur la même valeur que `PATH` dans le fichier `.vscode/settings.json` du projet sous la clé `terminal.integrated.env.osx`. Pour ce faire, exécutez la commande suivante dans le terminal : `PATH=<PATH in .vscode/settings>`
