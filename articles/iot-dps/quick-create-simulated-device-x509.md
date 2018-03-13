---
title: "Approvisionner un appareil X.509 simulé vers Azure IoT Hub à l’aide de C | Microsoft Docs"
description: "Guide de démarrage rapide d’Azure - Créer et approvisionner un appareil X.509 simulé à l’aide du C Device SDK pour le service Azure IoT Hub Device Provisioning"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4d723a3b78a43d3b609d5a884591a92606ca11cc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Créer et approvisionner un appareil X.509 simulé à l’aide du C Device SDK pour le service IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ces étapes indiquent comment simuler un appareil X.509 sur votre ordinateur de développement exécutant le système d’exploitation Windows et comment utiliser l’exemple de code pour connecter cet appareil simulé au service Device Provisioning et à votre IoT hub. 

Veillez à compléter les étapes décrites dans la section relative à la [configuration du service d’approvisionnement d’appareil Azure IoT Hub avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer.

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement 

1. Assurez-vous que Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) est installé sur votre ordinateur. La charge de travail [« Développement Desktop en C++ »](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) doit être activée pour l’installation de Visual Studio.

2. Téléchargez et installez le [système de génération de CMake](https://cmake.org/download/). Il est important que le Visual Studio avec la charge de travail « Développement Desktop en C++ » soit installé sur votre machine, **avant** l’installation de l’élément `cmake`. 

3. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 

4. Ouvrez une invite de commandes ou Git Bash. Clonez le référentiel GitHub pour l’exemple de code de simulation d’appareil :
    
    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Créez un dossier dans votre copie locale de ce référentiel GitHub pour le processus de génération de CMake. 

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. Exécutez la commande suivante pour créer la solution Visual Studio pour le client d’approvisionnement.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Si `cmake` ne trouve pas votre compilateur C++, vous obtiendrez peut-être des erreurs de build lors de l’exécution de la commande ci-dessus. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 


<a id="portalenroll"></a>

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Créer une entrée d’inscription d’appareil dans le service d’approvisionnement d’appareil

1. Ouvrez la solution générée dans le dossier *cmake* nommé `azure_iot_sdks.sln` générez-la dans Visual Studio.

2. Cliquez avec le bouton droit de la souris sur le projet **dice\_device\_enrollment** dans le dossier **Provision\_Tools**, puis sélectionnez **Définir comme projet de démarrage**. Exécutez la solution. Dans la fenêtre Sortie, entrez **i** pour l’inscription individuelle lorsque vous y êtes invité. La fenêtre Sortie affiche un certificat X.509 généré localement pour votre appareil simulé. Copiez dans le Presse-papiers la sortie débutant par *-----BEGIN CERTIFICATE-----* et se terminant par *-----END CERTIFICATE-----*, en faisant bien attention à inclure également ces deux lignes. Notez que n’avez besoin que du premier certificat dans la fenêtre Sortie.
 
3. Créez un fichier nommé **_X509testcert.pem_** sur votre machine Windows, ouvrez-le dans l’éditeur de votre choix et copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier . 

4. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service d'approvisionnement.

4. Ouvrez le panneau **Gérer les inscriptions** pour votre service. Sélectionnez l’onglet **Inscriptions individuelles**, puis cliquez sur le bouton **Ajouter** dans la partie supérieure. 

5. Sous l’**entrée Ajouter la liste d’inscription**, entrez les informations suivantes :
    - Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
    - Sous le *fichier .pem ou .cer du certificat*, sélectionnez le fichier de certificat **_X509testcert.pem_** créé au cours des étapes précédentes à l’aide du widget *Explorateur de fichiers*.
    - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
        - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
        - Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil. 
        - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
    - Cela fait, cliquez sur le bouton **Enregistrer**. 

    ![Saisir les informations d’inscription pour l’appareil X.509 dans le panneau du portail](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   Lorsque l’inscription aboutit, votre appareil X.509 apparaît en tant que **riot-device-cert** sous la colonne *ID d’inscription* dans l’onglet *Inscriptions individuelles*. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simuler la première séquence de démarrage de l’appareil

1. Dans le portail Azure, sélectionnez le panneau **Vue d’ensemble** de votre service Device Provisioning et notez les valeurs de **_Étendue de l’ID_**.

    ![Extraire des informations du point de terminaison DPS à partir du panneau du portail](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Dans Visual Studio sur votre ordinateur, accédez à l’exemple de projet nommé **prov\_dev\_client\_sample** sous le dossier **Provision\_Samples** et ouvrez le fichier **prov\_dev\_client\_sample.c**.

3. Assignez la valeur _d’étendue de l’ID_ à la variable `id_scope`. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. Dans la fonction **main()** du même fichier, assurez-vous que le **SECURE_DEVICE_TYPE** est défini sur X.509.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

   Commentez ou supprimez l’instruction `hsm_type = SECURE_DEVICE_TYPE_TPM;` qui peut être présente. 

5. Cliquez avec le bouton droit sur le projet **prov\_dev\_client\_sample** et sélectionnez **Définir comme projet de démarrage**. Exécutez l’exemple. Notez les messages qui simulent le démarrage et la connexion de l’appareil au service d’approvisionnement d’appareil pour obtenir des informations concernant votre IoT Hub. Recherchez le message indiquant que l’inscription avec votre Hub a réussi : *Informations relatives à l'inscription reçues de la part du service : yourhuburl !*. Fermez la fenêtre lorsque vous y êtes invité.

6. Dans le portail, accédez au IoT Hub lié à votre service d’approvisionnement et ouvrez le panneau **IoT Device**. En cas de réussite de l’approvisionnement de l’appareil X.509 simulé sur le Hub, son ID d’appareil s’affiche sur le panneau **IoT Device**, avec un *ÉTAT* **activé**. Notez que vous devrez peut-être cliquer sur le bouton **Actualiser** en haut, si vous avez déjà ouvert le panneau avant d’exécuter l’exemple d’application de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).


> [!IMPORTANT]
> Vous pouvez également effectuer une *Inscription de groupe* d’appareils X.509, en modifiant comme suit les étapes de ce démarrage rapide :
>    1. Configurez votre machine Windows pour qu’elle utilise la bibliothèque **OpenSSL** au lieu de la valeur **SChannel** par défaut en suivant la section sur **WebSockets** dans le guide [Configurer un environnement de développement Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#windows). Notez que les machines Linux utilisent OpenSSL par défaut. 
>    2. À l’étape 2 de la section [Créer une entrée d’inscription d’appareil dans le service d’approvisionnement d’appareil](#portalenroll) ci-dessus, entrez **g** pour l’inscription de groupe.
>    3. Aux étapes 4 et 5 de la [même section](#portalenroll), sélectionnez **Groupes d’inscriptions** et entrez les informations requises pour l’entrée de groupe.  
>

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à manipuler et explorer l’exemple de client d’appareil, ne nettoyez pas les ressources créées lors de ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple de client d’appareil sur votre machine.
1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre service Device Provisioning. Ouvrez le panneau **Gérer les inscriptions** pour votre service, puis cliquez sur l’onglet **Inscriptions individuelles**. Sélectionnez l’*ID D’INSCRIPTION* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure. 
1. À partir du menu de gauche, dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre IoT Hub. Ouvrez le panneau **IoT Devices** (Appareils IoT) pour votre hub, sélectionnez *l’ID D’APPAREIL* de l’appareil inscrit dans ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un appareil X.509 simulé sur un ordinateur Windows. Vous l’avez également approvisionné vers votre hub IoT à l’aide du service Azure IoT Hub Device Provisioning figurant sur le portail. Pour savoir comment inscrire un appareil X.509 au moyen d’un programme, poursuivez avec le démarrage rapide correspondant. 

> [!div class="nextstepaction"]
> [Démarrage rapide d’Azure : Inscrire des appareils X.509 auprès du service Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
