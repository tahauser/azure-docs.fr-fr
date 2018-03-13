---
title: Guide pratique pour utiliser MXChip IoT DevKit pour se connecter au service de provisionnement des appareils Azure IoT Hub | Microsoft Docs
description: Guide pratique pour utiliser MXChip IoT DevKit pour se connecter au service de provisionnement des appareils Azure IoT Hub
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Connecter MXChip IoT DevKit au service de provisionnement des appareils Azure IoT Hub

Cet article explique comment configurer DevKit pour faire en sorte qu’il s’inscrive automatiquement auprès d’IoT Hub via le service de provisionnement des appareils. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* Configurer le point de terminaison global du service de provisionnement des appareils (DPS) sur l’appareil
* Utiliser un secret d’appareil unique (UDS) pour générer un certificat X.509
* Inscrire individuellement un appareil
* Vérifier que l’appareil est enregistré

[IoT MXChip DevKit](https://aka.ms/iot-devkit) est une carte tout-en-un compatible Arduino qui est équipée de périphériques et de capteurs élaborés. Vous pouvez développer pour ce kit à l’aide de l’[extension Visual Studio Code pour Arduino](https://aka.ms/arduino). Par ailleurs, il s’accompagne d’un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) en plein développement pour vous guider dans la réalisation de prototypes de solutions IoT (Internet des objets) qui exploitent les services Microsoft Azure.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de ce didacticiel, vous devez au préalable :

* Préparer votre DevKit à l’aide du [Guide de prise en main](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Mettre à niveau le microprogramme vers la dernière version (>= 1.3.0) à l’aide du didacticiel [Mise à niveau du microprogramme](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Créer un hub IoT et le lier à l’instance du service de provisionnement des appareils en suivant la procédure indiquée dans [Configurer le provisionnement automatique](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Paramétrer la configuration du service de provisionnement des appareils sur l’appareil

Pour permettre à DevKit de se connecter à l’instance du service de provisionnement des appareils que vous avez créée :

1. Dans le portail Azure, sélectionnez la **Vue d’ensemble** de votre instance du service de provisionnement des appareils et notez la valeur des paramètres **Point de terminaison d’appareil global** et **Étendue de l’ID**.
  ![Paramètres Point de terminaison global et Étendue de l’ID du service DPS](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez [Outils clients Git de Software Freedom Conservancy](https://git-scm.com/download/) pour installer la dernière version.

3. Ouvrez une invite de commandes. Clonez le dépôt GitHub pour l’exemple de code du service DPS :
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Lancez VS Code, raccordez DevKit à l’ordinateur, puis ouvrez le dossier qui contient le code que vous avez cloné.

5. Ouvrez **DevKitDPS.ino**, puis recherchez et remplacez `[Global Device Endpoint]` et `[ID Scope]` par les valeurs que vous venez de noter.
  ![Point de terminaison DPS](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) Vous pouvez laisser **registrationId** vide, car l’application génère automatiquement un ID à partir de l’adresse MAC et de la version du microprogramme. Si vous voulez personnaliser l’ID d’inscription, celui-ci doit contenir une combinaison de caractères alphanumériques, de minuscules et de traits d’union et ne pas dépasser 128 caractères. Pour plus d’informations, consultez [Gérer les inscriptions d’appareils avec le portail Azure](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Utilisez **Quick Open** dans VS Code (Windows : `Ctrl+P`, macOS : `Cmd+P`) et tapez **task device-upload** pour générer et charger le code dans DevKit.

7. Vérifiez que la tâche a bien abouti dans la fenêtre de sortie.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Enregistrer le secret d’appareil unique dans un processeur de sécurité STSAFE

Le service de provisionnement des appareils peut être configuré sur appareil en fonction de son [module de sécurité matériel (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). DevKit utilise le moteur [DICE (Device Identity Composition Engine)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) de [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Le **secret d’appareil unique (UDS)** enregistré dans le processeur de sécurité STSAFE de DevKit sert à générer le certificat [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) unique de l’appareil. Ce certificat peut par la suite être utilisé dans le processus d’inscription du service de provisionnement des appareils.

En règle générale, un **secret d’appareil unique (UDS)** est une chaîne de 64 caractères. Voici un exemple d’UDS :

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Chacun des deux caractères est utilisé comme valeur hexadécimale dans le calcul de sécurité. Voici donc comment l’exemple d’UDS ci-dessus est résolu : « `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae` ».

Pour enregistrer le secret d’appareil unique sur DevKit :

1. Ouvrez le moniteur de série à l’aide d’un outil tel que Putty. Pour plus d’informations, consultez [Use Configuration Mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/).

2. Une fois DevKit raccordé à l’ordinateur, maintenez le bouton A enfoncé, puis appuyez brièvement sur le bouton de réinitialisation pour passer en mode configuration. L’écran affiche l’ID DevKit et **« Configuration »**.

3. Prenez la longue chaîne UDS d’exemple précédente et remplacez-en un ou plusieurs caractères par d’autres valeurs comprises entre `0` et `f`. Il s’agit de votre propre UDS.

4. Dans la fenêtre du moniteur de série, tapez **set_dps_uds [votre_propre_valeur_uds]** et appuyez sur la touche Entrée pour l’enregistrer.
  > [!NOTE]
  > Par exemple, si vous définissez votre propre UDS en remplaçant les deux derniers caractères par `f`, vous devez entrer la commande **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. Sans fermer la fenêtre du moniteur de série, appuyez sur bouton de réinitialisation de DevKit.

6. Notez les valeurs d’**adresse MAC de DevKit** et de **version du microprogramme de DevKit**.
  ![Version du microprogramme](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>Générer un certificat X.509

### <a name="windows"></a>Windows

1. Ouvrez l’Explorateur de fichiers et accédez au dossier qui contient l’exemple de code DSP que vous avez cloné. Vous constatez la présence d’un dossier **.build** ; recherchez les fichiers **DPS.ino.bin** et **DPS.ino.map** et copiez-les dans ce dossier.
  ![Fichiers générés](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Si vous avez placé la configuration `built.path` pour Arduino dans un autre dossier, ces fichiers doivent se trouver dans le dossier que vous avez configuré.

2. Collez ces deux fichiers dans le dossier **tools** au même niveau que le dossier **.build**.

3. Exécutez **dps_cert_gen.exe**. Vous êtes alors invité à entrer votre **UDS**, l’**adresse MAC** de DevKit et la **version du microprogramme** pour générer le certificat X.509.
  ![Exécution de dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Vérifiez que la génération a bien abouti : un certificat **.pem** est enregistré dans le même dossier.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Créer une entrée d’inscription d’appareil dans le service de provisionnement d’appareil

1. Dans le portail Azure, accédez au service de provisionnement. Cliquez sur **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**. ![Inscriptions individuelles](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Cliquez sur **Ajouter**.

3. Dans **Mécanisme**, choisissez **X.509**.
  ![Chargement du certificat](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. Dans **Fichier .pem ou .cer de certificat**, chargez le certificat **.pem** que vous venez de créer.

5. Conservez les autres valeurs par défaut, puis cliquez sur **Enregistrer**.

## <a name="start-the-devkit"></a>Démarrer DevKit

1. Lancez VS Code et ouvrez le moniteur de série.

2. Appuyez sur le bouton de **réinitialisation** de DevKit.

DevKit lance l’inscription auprès du service de provisionnement des appareils.

![Sortie VS Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Vérifier que DevKit est inscrit dans IoT Hub

Une fois que votre appareil démarre, voici les actions qui doivent se produire :

1. L’appareil envoie une demande d’inscription au service de provisionnement des appareils.
2. Le service de provisionnement des appareils renvoie une vérification d’inscription à laquelle votre appareil répond.
3. Une fois l’inscription réussie, le service de provisionnement des appareils envoie l’URI du hub IoT, l’ID de l’appareil et la clé chiffrée à l’appareil.
4. L’application cliente IoT Hub sur l’appareil peut alors se connecter à votre hub.
5. Une fois la connexion au hub établie, l’appareil doit apparaître dans Device Explorer, dans IoT Hub.
  ![Appareil inscrit](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Modifier l’ID de l’appareil

L’ID d’appareil par défaut inscrit dans Azure IoT Hub est **AZ3166**. Si vous voulez le modifier, suivez les [instructions ici](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, consultez les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) si ou contactez-nous via les canaux ci-dessous :

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à préparer DevKit en vue d’inscrire un appareil en toute sécurité auprès du service DPS via DICE, il peut s’inscrire automatiquement auprès d’IoT Hub sans aucune intervention de votre part. Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le point de terminaison global du service de provisionnement des appareils (DPS) sur l’appareil
> * Utiliser un secret d’appareil unique (UDS) pour générer un certificat X.509
> * Inscrire individuellement un appareil
> * Vérifier que l’appareil est enregistré

Passez aux autres tutoriels pour apprendre à :

> [!div class="nextstepaction"]
> [Créer et provisionner un appareil simulé](./quick-create-simulated-device.md)

