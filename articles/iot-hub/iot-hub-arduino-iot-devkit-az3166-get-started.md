---
title: "Kit IoT DevKit dans le cloud - Connecter le kit IoT DevKit AZ3166 à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer le kit IoT DevKit AZ3166 et le connecter à Azure IoT Hub pour qu’il puisse envoyer des données sur la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: xshi
ms.openlocfilehash: 1ad9200376bc4959e7572ae8d5be11b278a42cc0
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Connecter IoT DevKit AZ3166 à Azure IoT Hub dans le cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Vous pouvez utiliser le kit [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) pour développer et réaliser des prototypes de solutions IoT (Internet of Things) qui tirent parti des services Microsoft Azure. Il comprend une carte compatible Arduino avec un grand choix de périphériques et de capteurs élaborés, un package de carte open source et un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) de plus en plus riche.

## <a name="what-you-do"></a>Procédure
Connectez le kit [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) à un hub Azure IoT que vous créez, collectez les données de température et d’humidité à partir des capteurs et envoyez-les au hub IoT.

Vous n’avez pas encore de DevKit ? Essayez le [simulateur DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [obtenez-en un](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Contenu

* Comment connecter le kit IoT DevKit à un point d’accès sans fil et préparer votre environnement de développement.
* Comment créer un hub IoT et enregistrer un appareil auprès du kit MXChip IoT DevKit.
* Comment collecter les données des capteurs en exécutant un exemple d’application sur le kit MXChip IoT DevKit.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Une carte MXChip IoT DevKit avec câble micro-USB. [Achetez-en une maintenant](https://aka.ms/iot-devkit-purchase).
* Un ordinateur exécutant Windows 10 ou macOS 10.10+.
* Un abonnement Azure actif. [Activez un compte Microsoft Azure pour un essai gratuit de 30 jours](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Préparation du matériel

Raccordez le matériel à votre ordinateur.

Vous avez besoin du matériel suivant :

* Carte DevKit
* Câble micro-USB

![Matériel requis](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Pour brancher le kit DevKit sur votre ordinateur :

1. Branchez l’extrémité USB sur l’ordinateur.
2. Branchez la fiche micro-USB sur le kit DevKit.
3. Le témoin vert de l’alimentation confirme le branchement.

![Branchements du matériel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurer le Wi-Fi

Les projets IoT sont tributaires de la connectivité Internet. Utilisez les instructions suivantes pour configurer la connexion du kit DevKit au Wi-Fi.

### <a name="enter-ap-mode"></a>Activer le mode « point d’accès »

Maintenez le bouton B enfoncé, appuyez sur le bouton de réinitialisation (Reset) et relâchez-le, puis relâchez le bouton B. Votre kit DevKit passe en mode « point d’accès » pour la configuration du Wi-Fi. L’écran affiche l’identificateur SSID (Service Set Identifier) du kit DevKit et l’adresse IP du portail de configuration.

![Bouton de réinitialisation, bouton B et SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Se connecter au point d’accès DevKit

Utilisez à présent un autre appareil compatible Wi-Fi (un téléphone mobile ou un ordinateur) pour vous connecter au SSID du kit DevKit (mis en évidence dans l’image précédente). Laissez le mot de passe vide.

![Bouton de connexion et informations réseau](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurer le Wi-Fi du kit DevKit

Ouvrez l’adresse IP affichée sur l’écran du kit DevKit dans le navigateur de votre ordinateur ou de votre téléphone mobile, sélectionnez le réseau Wi-Fi auquel vous souhaitez que le kit DevKit se connecte et tapez le mot de passe. Sélectionnez **Connecter**.

![Zone de mot de passe et bouton de connexion](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dès que la connexion est établie, le kit DevKit redémarre au bout de quelques secondes. L’adresse IP et le nom du Wi-Fi apparaissent alors à l’écran :

![Nom Wi-Fi et adresse IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L’adresse IP présentée sur la photo peut ne pas correspondre à l’adresse IP réellement attribuée et affichée sur l’écran du kit DevKit. Cette divergence est normale dans la mesure où le Wi-Fi utilise le DHCP pour attribuer dynamiquement les adresses IP.

Dès lors que le Wi-Fi est configuré, vos informations d’identification pour cette connexion sont persistantes sur l’appareil, même s’il est débranché. Par exemple, si vous configurez le kit DevKit pour le Wi-Fi à votre domicile, et que vous l’emportez ensuite au bureau, vous devez reconfigurer le mode « point d’accès » (en commençant à l’étape Activer le mode « point d’accès ») pour connecter le kit DevKit au Wi-Fi de votre bureau. 

## <a name="start-using-the-devkit"></a>Commencer à utiliser le kit DevKit

L’application qui s’exécute par défaut sur le kit DevKit vérifie la dernière version du microprogramme installée et affiche certaines données de diagnostic des capteurs.

### <a name="upgrade-to-the-latest-firmware"></a>Passer à la dernière version du microprogramme

> [!NOTE] 
> Depuis la version 1.1, le kit DevKit active ST-SAFE dans le chargeur de démarrage. Vous devez mettre à niveau le microprogramme si vous exécutez une version antérieure à v1.1 afin que cela fonctionne.

Si une mise à niveau du microprogramme est nécessaire, l’écran affiche la version actuelle et la version la plus récente du microprogramme. Pour la mise à niveau, suivez le guide [Mettre à niveau le microprogramme](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).

![Affichage de la versions actuelle et de la version plus récente du microprogramme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Cette opération n’est nécessaire qu’une seule fois. Dès que vous commencez à développer sur le kit DevKit et que vous chargez votre application, la dernière version du microprogramme est fournie avec votre application.

### <a name="test-various-sensors"></a>Tester divers capteurs

Appuyez sur le bouton B pour tester les capteurs. Continuez d’appuyer sur ce bouton et de relâcher votre pression à chaque fois pour passer en revue tous les capteurs.

![Affichage d’un capteur et du bouton B](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Il s’agit maintenant de configurer l’environnement de développement, c’est-à-dire les outils et les packages qui vous permettront de créer de superbes applications IoT. Vous pouvez choisir la version Windows ou macOS en fonction de votre système d’exploitation.

### <a name="windows"></a>Windows

Nous vous encourageons à utiliser le package d’installation pour préparer l’environnement de développement. Si vous rencontrez des problèmes, vous pouvez suivre les [étapes de l’installation manuelle](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) pour y parvenir.

#### <a name="download-the-latest-package"></a>Télécharger le dernier package

Le fichier .zip que vous téléchargez contient tous les outils et packages nécessaires pour le développement DevKit.

> [!div class="button"]
[Télécharger](https://aka.ms/devkit/prod/installpackage/latest)

Le fichier .zip contient les outils et les packages suivants. Si certains composants sont déjà installés, le script les détecte et les ignore.

* Node.js et Yarn : runtime pour le script d’installation et les tâches automatisées.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) : expérience multiplateforme en ligne de commande pour la gestion des ressources Azure. Le fichier MSI contient les modules Python et pip dépendants.
* [Visual Studio Code](https://code.visualstudio.com/) (code VS) : éditeur de code léger pour le développement DevKit.
* [Extension Visual Studio Code pour Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) : extension qui permet le développement Arduino dans Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software) : outil sur lequel l’extension pour Arduino s’appuie.
* Package de la carte DevKit : chaînes d’outils, bibliothèques et projets pour le kit DevKit.
* Utilitaire ST-Link : pilotes et outils essentiels.

#### <a name="run-the-installation-script"></a>Exécuter le script d’installation

Dans l’Explorateur de fichiers Windows, localisez le fichier .zip et extrayez-le. Recherchez `install.cmd`, cliquez dessus avec le bouton droit et sélectionnez **Exécuter en tant qu’administrateur**.

![Explorateur de fichiers](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Pendant l’installation, vous voyez la progression de chaque outil et package.

![Progression de l’installation](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> Selon votre environnement, il est possible que vous rencontriez un échec lors de l’installation de l’IDE Arduino. Dans ce cas, vous pouvez essayer [d’installer l’IDE Arduino individuellement](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) et exécuter install.cmd à nouveau. Sinon, suivez les [étapes manuelles](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) pour installer tous les outils et packages nécessaires.

#### <a name="install-drivers"></a>Installer les pilotes

L’extension VS Code pour Arduino s’appuie sur l’IDE Arduino. Si c’est la première fois que vous installez l’IDE Arduino, vous êtes amené à installer les pilotes correspondants :

![bien-démarrer-pilote](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

L’installation devrait prendre environ 10 minutes, en fonction de la vitesse de votre connexion Internet. Après l’installation, les raccourcis Visual Studio Code et IDE Arduino apparaissent sur votre bureau.

> [!NOTE] 
> Au démarrage de VS Code, il peut arriver qu’une erreur s’affiche indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Pour résoudre le problème, fermez VS Code et redémarrez l’IDE Arduino. VS Code devrait ainsi localiser correctement le chemin de l’IDE Arduino.

### <a name="macos"></a>macOS

Nous vous encourageons à utiliser l’expérience d’installation en un clic pour préparer l’environnement de développement. Si vous rencontrez des problèmes, vous pouvez suivre les [étapes de l’installation manuelle](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) pour y parvenir.

#### <a name="install-homebrew"></a>Installer Homebrew

> [!NOTE] 
> Si vous avez installé Homebrew, vous pouvez ignorer cette étape.

Suivez les [instructions d’installation d’Homebrew](https://docs.brew.sh/Installation.html).

#### <a name="download-the-latest-package"></a>Télécharger le dernier package
Le fichier .zip que vous téléchargez contient tous les outils et packages nécessaires pour le développement DevKit.

> [!div class="button"]
[Télécharger](https://aka.ms/devkit/prod/installpackage/mac/latest)

Le fichier .zip contient les outils et les packages suivants. Si certains composants sont déjà installés, le script les détecte et les ignore.

* Node.js et Yarn : runtime pour le script d’installation et les tâches automatisées.
* [Azure CLI 2.0 :](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos) expérience multiplateforme en ligne de commande pour la gestion des ressources Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (code VS) : éditeur de code léger pour le développement DevKit.
* [Extension Visual Studio Code pour Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) : extension qui permet le développement Arduino dans Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software) : outil sur lequel l’extension pour Arduino s’appuie.
* Package de la carte DevKit : chaînes d’outils, bibliothèques et projets pour le kit DevKit.
* Utilitaire ST-Link : pilotes et outils essentiels.

#### <a name="run-the-installation-script"></a>Exécuter le script d’installation

Dans le Finder, localisez le fichier .zip et extrayez son contenu :

![macOS finder](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Lancez l’application Terminal, localisez le dossier dans lequel vous avez extrait le fichier .zip et exécutez la commande suivante :

```bash
./install.sh
```

![macOS install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Si vous rencontrez une erreur d’autorisation Homebrew, exécutez `brew doctor` pour résoudre le problème. Pour plus d’informations, consultez le [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos).

Tous les outils et packages nécessaires sont à présent installés pour macOS.


## <a name="open-the-project-folder"></a>Ouvrir le dossier de projet

### <a name="start-vs-code"></a>Démarrer VS Code

Assurez-vous que votre DevKit n’est pas connecté. Démarrez d’abord VS Code et branchez ensuite le kit DevKit sur votre ordinateur. VS Code trouve automatiquement le kit DevKit et ouvre une page de présentation :

![Page d’introduction](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Au démarrage de VS Code, il peut arriver qu’une erreur s’affiche indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Fermez VS Code et redémarrez l’IDE Arduino. VS Code devrait ainsi localiser correctement le chemin de l’IDE Arduino.


### <a name="open-the-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Sous l’onglet **Exemples Arduino**, accédez à **Exemples pour MXCHIP AZ3166** > **AzureIoT** et sélectionnez **GetStarted**.

![Onglet des exemples Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, exécutez votre tâche par `Ctrl+P` (macOS : `Cmd+P`) en entrant `task cloud-provision`.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires :

![Ligne de commande interactive](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Générer et charger l’ébauche de projet Arduino

### <a name="windows"></a>Windows

1. Utilisez `Ctrl+P` pour exécuter `task device-upload`.
2. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche l’ID du kit DevKit et sa « Configuration ».

Cela permet de définir la chaîne de connexion qui récupère à partir de l’étape `task cloud-provision`.

Ensuite, VS Code commence la vérification et le chargement de l’ébauche de projet Arduino :

![Vérification et téléchargement de l’ébauche de projet Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

> [!NOTE] 
> L’erreur « Erreur : AZ3166 : package inconnu » se produit parfois. Elle est due au fait que l’index du package de la carte n’est pas actualisé. Suivez ces [étapes de la FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) pour la résoudre.

### <a name="macos"></a>macOS

1. Placez le kit DevKit en mode de configuration : maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche « Configuration ».
2. Utilisez `Cmd+P` pour exécuter `task device-upload`.

Cela permet de définir la chaîne de connexion qui récupère à partir de l’étape `task cloud-provision`.

Ensuite, VS Code commence la vérification et le chargement de l’ébauche de projet Arduino :

![device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

> [!NOTE] 
> L’erreur « Erreur : AZ3166 : package inconnu » se produit parfois. Elle est due au fait que l’index du package de la carte n’est pas actualisé. Suivez ces [étapes de la FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) pour la résoudre.


## <a name="test-the-project"></a>Tester le projet

Dans VS Code, procédez comme suit pour ouvrir et configurer Serial Monitor (analyse en série) :

1. Cliquez sur le mot `COM[X]` dans la barre d’état pour définir le port COM approprié avec `STMicroelectronics` : ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Cliquez sur l’icône de branchement « power » dans la barre d’état pour ouvrir l’analyse en série : ![serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Dans la barre d’état, cliquez sur le numéro qui représente la vitesse de transmission et définissez-la sur `115200` : ![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

L’exemple d’application s’exécute correctement si les résultats suivants s’affichent :

* L’analyse en série affiche les mêmes informations que dans la capture d’écran ci-dessous.
* La LED de MXChip IoT DevKit clignote.

![Sortie finale dans VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, vous pouvez consulter [Questions fréquentes (FAQ)](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Vous pouvez également nous faire part de vos remarques en laissant un commentaire sur cette page.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté un kit MXChip IoT DevKit à votre hub IoT et avez envoyé des données de capteur collectées à votre hub IoT.

Pour continuer d’apprendre à utiliser Azure IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

- [Gérer la messagerie de périphérique cloud avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Enregistrer les messages IoT Hub dans le stockage de données Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Utiliser Power BI pour visualiser des données de capteur en temps réel provenant d’Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Utiliser Web Apps pour visualiser des données de capteur en temps réel depuis Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Prévision météo utilisant les données de capteur de votre IoT Hub dans Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestion des appareils avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Surveillance à distance et notifications avec Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
