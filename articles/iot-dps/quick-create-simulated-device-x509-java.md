---
title: Approvisionner un appareil X.509 simulé auprès du service Azure IoT Hub à l’aide de Java | Microsoft Docs
description: 'Démarrage rapide d’Azure : Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Java Device SDK'
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: f9eb572897854cfb2927b0282d25d5c272abd0e8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Créer et approvisionner un appareil X.509 simulé auprès du service IoT Hub Device Provisioning à l’aide du Java Device SDK
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Ces étapes indiquent comment simuler un appareil X.509 sur votre ordinateur de développement exécutant le système d’exploitation Windows et comment utiliser l’exemple de code pour connecter cet appareil simulé au service Device Provisioning et à votre IoT hub. 

Avant de continuer, veillez à réaliser les étapes décrites dans la section [Configuration du service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md).

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Assurez-vous que le [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) est bien installé sur votre ordinateur.

1. Téléchargez et installez [Maven](https://maven.apache.org/install.html).

1. Assurez-vous que l’élément `git` est installé sur votre machine et est ajouté aux variables d’environnement accessibles à la fenêtre de commande. Consultez la section relative aux [outils clients de Software Freedom Conservancy](https://git-scm.com/download/) pour accéder à la dernière version des outils `git` à installer, qui inclut **Git Bash**, l’application de ligne de commande que vous pouvez utiliser pour interagir avec votre référentiel Git local. 

1. Ouvrez une invite de commandes. Clonez le référentiel GitHub pour l’exemple de code de simulation d’appareil :
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
1. Accédez au répertoire azure-iot-sdk-java racine et créez le projet pour télécharger tous les packages nécessaires.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
1. Accédez au projet de générateur de certificats et générez le projet. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Accédez au dossier cible et exécutez le fichier jar créé.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Créez les informations d’inscription de l’une des façons suivantes, selon votre configuration :

    - **Inscription individuelle** :

        1. Entrez **N** pour _Do you want to input common name_ (Voulez-vous saisir le nom commun ?). Copiez dans le Presse-papiers la sortie de `Client Cert`, de *-----BEGIN CERTIFICATE-----* à *-----END CERTIFICATE-----*.

            ![Générateur de certificats individuels](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Créez un fichier nommé **_X509individual.pem_** sur votre ordinateur Windows, ouvrez-le dans l’éditeur de votre choix, puis copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier .

        1. Entrez **N** pour _Do you want to input Verification Code_ (Voulez-vous saisir le code de validation ?), puis gardez la sortie du programme ouverte pour vous servir plus tard dans ce démarrage rapide. Notez les valeurs _Client Cert_ (Certificat client) et _Client Cert Private Key_ (Clé privée certificat client).
    
    - **Groupes d'inscription** :

        1. Entrez **N** pour _Do you want to input common name_ (Voulez-vous saisir le nom commun ?). Copiez dans le Presse-papiers la sortie de `Root Cert`, de *-----BEGIN CERTIFICATE-----* à *-----END CERTIFICATE-----*.

            ![Générateur de certificats de groupe](./media/java-quick-create-simulated-device-x509/group.png)

        1. Créez un fichier nommé **_X509group.pem_** sur votre ordinateur Windows, ouvrez-le dans l’éditeur de votre choix, puis copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier .

        1. Entrez **Y** pour _Do you want to input Verification Code_ (Voulez-vous saisir le code de vérification ?), puis gardez le programme ouvert pour vous servir plus tard dans ce démarrage rapide. Notez les valeurs _Client Cert_ (Certificat client), _Client Cert Private Key_ (Clé privée certificat client), _Signer Cert_ (Signataire certificat) et _Root Cert_ (Certificat racine).

        > [!NOTE]
        > Le `Root Cert` ci-dessus s’applique uniquement aux certificats créés dans la sortie de console et ne peut pas être utilisé pour signer des certificats clients supplémentaires. Si vous avez besoin d’un ensemble plus robuste de certificats de test, consultez l’[exemple de gestion de certificats d’autorité de certification](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).
        >

## <a name="create-a-device-enrollment-entry"></a>Créer une entrée d’inscription d’appareil

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service d'approvisionnement.

1. Saisissez les informations d’inscription de l’une des façons suivantes, selon votre configuration :

    - **Inscription individuelle** : 

        1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Inscriptions individuelles** et cliquez sur le bouton **Ajouter** dans la partie supérieure. 

        1. Sous l’**entrée Ajouter la liste d’inscription**, entrez les informations suivantes :
            - Sélectionnez **X.509** comme *mécanisme* d’attestation d’identité.
            - Sous le *fichier .pem ou .cer du certificat*, sélectionnez le fichier de certificat **_X509individual.pem_** créé au cours des étapes précédentes à l’aide du widget *Explorateur de fichiers*.
            - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
                - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
                - Entrez un ID d’appareil unique. Veillez à éviter les données sensibles lorsque vous affectez un nom à votre appareil. 
                - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.
            - Cela fait, cliquez sur le bouton **Enregistrer**. 

        ![Saisir les informations d’inscription pour l’appareil X.509 dans le panneau du portail](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Lorsque l’inscription aboutit, votre appareil X.509 apparaît en tant que **microsoftriotcore** sous la colonne *ID d’inscription* de l’onglet *Inscriptions individuelles*. 

    - **Groupes d’inscription** : 

        1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Certificats**, puis cliquez sur le bouton **Ajouter** dans la partie supérieure.

        1. Sous **Ajouter un certificat**, entrez les informations suivantes :
            - Entrez un nom de certificat unique.
            - Sélectionnez le fichier **_X509group.pem_** que vous avez créé précédemment.
            - Cela fait, cliquez sur le bouton **Enregistrer**.

        ![Ajouter un certificat](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Sélectionnez le certificat que vous venez de créer :
            - Cliquez sur **Générer le code de vérification**. Copiez le code généré.
            - Entrez le _code de vérification_ ou cliquez avec le bouton droit de la souris et collez-le dans la fenêtre _provisioning-x509-cert-generator_ en cours d’exécution.  Appuyez sur **Entrée**.
            - Copiez dans le Presse-papiers la sortie de `Verification Cert`, de *-----BEGIN CERTIFICATE-----* à *-----END CERTIFICATE-----*.
            
                ![Générateur de validation](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Créez un fichier nommé **_X509validation.pem_** sur votre ordinateur Windows, ouvrez-le dans l’éditeur de votre choix, puis copiez le contenu du Presse-papiers dans ce fichier. Enregistrez le fichier .
            - Sélectionnez le fichier  **_X509validation.pem_** dans le portail Azure. Cliquez sur **Vérifier**.

            ![Valider le certificat](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Sélectionnez **Gérer les inscriptions**. Sélectionnez l’onglet **Groupe d’inscriptions**, puis cliquez sur le bouton **Ajouter** dans la partie supérieure.
            - Entrez un nom de groupe unique.
            - Sélectionnez le nom de certificat unique créé précédemment
            - Si vous le souhaitez, vous pouvez fournir les informations suivantes :
                - Sélectionnez un hub IoT lié à votre service d’approvisionnement.
                - Mettez à jour l’**état du jumeau d’appareil initial** à l’aide de la configuration initiale de votre choix pour l’appareil.

        ![Comment saisir les informations d’inscription de groupe X.509 dans le panneau du portail](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Une fois que l’inscription a abouti, votre groupe d’appareils X.509 apparaît sous la colonne *Nom de groupe*, dans l’onglet *Groupes d’inscription*.


## <a name="simulate-the-device"></a>Simuler l’appareil

1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Vue d’ensemble**, puis notez les valeurs _ID Scope_ (Étendue d’ID) et _Provisioning Service Global Endpoint_ (Point de terminaison global du service d’approvisionnement).

    ![Informations sur le service](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Ouvrez une invite de commandes. Accédez à l’exemple de dossier du projet.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Saisissez les informations d’inscription de l’une des façons suivantes, selon votre configuration :

    - **Inscription individuelle** : 

        1. Modifiez `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` pour inclure les valeurs _ID scope_ (Étendue de l’ID) et _Provisioning Service Global Endpoint_ (Point de terminaison global du service d’approvisionnement) notées précédemment. Insérez également les valeurs _Client Cert_ (Certificat client) et _Client Cert Private Key_ (Clé privée certificat client) déjà utilisées.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Pour inclure votre certificat et la clé, utilisez le format suivant :
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Groupes d’inscription** : 

        1. Suivez les instructions pour **l’inscription individuelle** ci-dessus.

        1. Ajoutez les lignes de code suivantes au début de la fonction `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - Insérez vos certificats en utilisant le format suivant :
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Générez l’exemple. Accédez au dossier cible et exécutez le fichier jar créé.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. Dans le portail, accédez au IoT Hub lié à votre service d’approvisionnement, ouvrez le panneau **Device Explorer**. En cas de réussite de l’approvisionnement de l’appareil simulé X.509 sur le Hub, son ID de périphérique s’affiche sur le panneau **Device Explorer**, avec un *ÉTAT* **activé**. Notez que vous devrez peut-être cliquer sur le bouton **Actualiser** en haut, si vous avez déjà ouvert le panneau avant d’exécuter l’exemple d’application de l’appareil. 

    ![L’appareil est inscrit avec le hub IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Si vous avez modifié la valeur par défaut de l’*état du jumeau d’appareil initial* dans l’entrée d’inscription de votre appareil, l’état du jumeau souhaité peut être extrait du hub et agir en conséquence. Pour en savoir plus, consultez [Comprendre et utiliser les jumeaux d’appareil IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).
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
