---
title: 'DevKit IoT dans le cloud : Connecter le DevKit IoT MXChip à Azure IoT Hub | Documents Microsoft'
description: Dans ce didacticiel, découvrez comment envoyer l’état des capteurs sur IoT DevKit AZ3166 vers Azure IoT Suite à des fins de surveillance et de visualisation.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 92efd0970bcf516c4210f831a0c2f23b3ee7b5d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-suite-for-remote-monitoring"></a>Connecter le DevKit MXChip IoT à Azure IoT Suite pour la surveillance à distance

Dans ce didacticiel, vous allez apprendre à exécuter un exemple d’application sur votre kit DevKit pour envoyer les données des capteurs vers Azure IoT Suite.

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de l’[extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Suivez le [Guide de mise en route](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) pour :

* Connecter votre DevKit au Wi-Fi
* Préparer l’environnement de développement

Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire via l’une de ces deux méthodes :

* Activez un [compte d’évaluation Microsoft Azure gratuit pendant 30 jours](https://azureinfo.microsoft.com/us-freetrial.html).
* Réclamez votre [crédit Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) si vous êtes abonné à MSDN ou Visual Studio

## <a name="create-an-azure-iot-suite"></a>Créer une solution Azure IoT Suite

1. Accédez au [site Azure IoT Suite](https://www.azureiotsuite.com/) et cliquez sur **Créer une solution**.
  ![Sélectionner le type de solution Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > Par défaut, cet exemple crée un IoT Hub S2 après avoir créé une solution IoT Suite. Si cet IoT Hub n’est pas utilisé avec un très grand nombre d’appareils, nous vous recommandons vivement de passer de le rétrograder de la version S2 à la version S1 et de supprimer IoT Suite pour pouvoir aussi supprimer l’IoT Hub associé au moment requis. 

2. Sélectionnez **Surveillance à distance**.

3. Entrez le nom d’une solution, sélectionnez un abonnement et une région, puis cliquez sur **Créer une solution**. La solution peut prendre un certain temps à configurer.
  ![Créer une solution](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Une fois la configuration terminée, cliquez sur **Lancer**. Certains appareils simulés sont créés pour la solution pendant le processus de configuration. Cliquez sur **APPAREILS** pour les contrôler. ![Tableau de bord](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Cliquez sur **AJOUTER UN APPAREIL**.

6. Cliquez sur **Ajouter nouveau** pour **Appareil personnalisé**.
  ![Ajouter un appareil](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Cliquez sur **Me laisser définir mon propre ID d’appareil**, entrez `AZ3166`, puis cliquez sur **Créer**.
  ![Créer un appareil avec un ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Notez le **Nom d’hôte IoT Hub**, puis cliquez sur **Terminé**.

## <a name="open-the-remotemonitoring-sample"></a>Ouvrir l’exemple RemoteMonitoring

1. Déconnectez le kit DevKit de votre ordinateur, s’il est connecté.

2. Démarrez VS Code.

3. Connectez le kit DevKit à votre ordinateur. VS Code détecte automatiquement votre kit DevKit et ouvre les pages suivantes :
  * La page d’introduction du kit DevKit.
  * Exemples Arduino : exemples pratiques pour démarrer avec le kit DevKit.

4. Développez la section **EXEMPLES ARDUINO** à gauche, accédez à **Exemples pour MXCHIP AZ3166 > AzureIoT** et sélectionnez **RemoteMonitoring**. Une nouvelle fenêtre VS Code s’ouvre avec le dossier de projet qu’elle contient.
  > [!NOTE]
  > S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Exemples**.

## <a name="provision-required-azure-services"></a>Configurer les services Azure requis

Dans la fenêtre de la solution, exécutez votre tâche par `Ctrl+P` (macOS : `Cmd+P`) en entrant `task cloud-provision` dans la zone de texte fournie :

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires :

![Approvisionner des ressources Azure](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

1. Utilisez `Ctrl+P` (macOS : `Cmd + P`) et tapez **task config-device-connection**.

2. Le terminal vous demande si vous souhaitez utiliser la chaîne de connexion qui extrait à partir de l’étape `task cloud-provision`. Vous pourriez également entrer votre propre chaîne de connexion d’appareil en cliquant sur « Créer nouveau... »

3. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche l’ID du kit DevKit et « Configuration ».
  ![Entrer la chaîne de connexion](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Après avoir terminé `task config-device-connection`, cliquez sur `F1` pour charger les commandes VS Code et sélectionnez `Arduino: Upload`. Ensuite, VS Code démarre la vérification et le chargement de l’ébauche de projet Arduino : ![vérification et chargement de l’ébauche de projet Arduino](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

Lorsque l’exemple d’application s’exécute, le kit DevKit envoie les données des capteurs par Wi-Fi à votre solution Azure IoT Suite. Pour voir le résultat, procédez comme suit :

1. Accédez à votre solution Azure IoT Suite, puis cliquez sur **Tableau de bord**.

2. Dans la console de la solution Azure IoT Suite, vous verrez l’état des capteurs de votre kit DevKit.

![Données des capteurs dans Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Modifier l’ID de l’appareil

Vous pouvez modifier l’ID de l’appareil dans IoT Hub en suivant [ce guide](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Et si vous souhaitez remplacer le **AZ3166** codé en dur par un ID d’appareil personnalisé dans le code. Vous trouverez la ligne de code que vous pouvez modifier [ici](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) si ou contactez-nous via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un appareil DevKit à votre solution Azure IoT Suite et à visualiser les données des capteurs, nous vous suggérons les étapes suivantes :

* [Vue d’ensemble d’Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Connecter un appareil DevKit IoT MXChip à votre application Microsoft IoT Central](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
