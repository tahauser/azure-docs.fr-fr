---
title: "Inscrire un appareil TPM auprès du service Azure Device Provisioning avec Python | Microsoft Docs"
description: "Démarrage rapide d’Azure : Inscrire un appareil TPM auprès du service Azure IoT Hub Device Provisioning à l’aide du Kit de développement logiciel (SDK) du service d’approvisionnement en Python"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 01/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 941e6d53b136a3cfef368e436b31a3022f5223fa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Inscrire un appareil TPM auprès du service IoT Hub Device Provisioning à l’aide du Kit de développement logiciel (SDK) du service d’approvisionnement en Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ces étapes montrent comment créer par programme une inscription individuelle pour un appareil TPM dans le service Azure IoT Hub Device Provisioning à l’aide du [Kit de développement logiciel (SDK) du service d’approvisionnement en Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) avec un exemple d’application Python. Même si ce Kit de développement logiciel (SDK) fonctionne à la fois sur Linux et Windows, cet article utilise un ordinateur de développement sur Windows afin de présenter le processus d’inscription.

Veillez à [configurer le service IoT Hub Device Provisioning avec le portail Azure](./quick-setup-auto-provision.md) avant de continuer.


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Préparer l’environnement 

1. Téléchargez et installez [Python 2.x ou 3.x](https://www.python.org/downloads/). Veillez à utiliser l’installation 32 bits ou 64 bits comme requis par votre programme d’installation. Lorsque vous y êtes invité pendant l’installation, veillez à ajouter Python à votre variable d’environnement propre à la plateforme. 

1. Choisissez l’une des options suivantes :

    - Générer et compiler le **Kit de développement logiciel (SDK) Python Azure IoT**. Suivez [ces instructions](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) pour générer les packages Python. Si vous utilisez le système d’exploitation Windows, installez également [Visual C++ Redistributable Package](http://www.microsoft.com/download/confirmation.aspx?id=48145) pour autoriser l’utilisation de DLL natives de Python.

    - [Installer ou mettre à niveau *pip*, le système de gestion des packages Python](https://pip.pypa.io/en/stable/installing/) et installer le package via la commande suivante :

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Vous avez besoin de la paire de clés de type EK (Endorsement Key) de votre appareil. Si vous avez suivi le démarrage rapide [Créer et configurer un appareil simulé](quick-create-simulated-device.md) pour créer un appareil TPM simulé, utilisez la clé créée pour cet appareil. Sinon, vous pouvez utiliser la paire de clés de type EK (Endorsement Key) suivante fournie avec le Kit de développement logiciel (SDK) :

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Modifier l’exemple de code Python

Cette section montre comment ajouter à l’exemple de code les détails de l’approvisionnement de votre appareil TPM. 

1. Dans un éditeur de texte, créez un fichier **TpmEnrollment.py**.

1. Ajoutez les variables et instructions `import` ci-dessous au début du fichier **TpmEnrollment.py**. Remplacez ensuite `dpsConnectionString` par la chaîne de connexion trouvée dans **Stratégies d’accès partagé** dans votre **Service Device Provisioning** sur le **portail Azure**. Remplacez `endorsementKey` par la valeur notée précédemment dans [Préparer l’environnement](quick-enroll-device-tpm-python.md#prepareenvironment). Pour finir, créez un ID `registrationid` unique et veillez à ce qu’il se compose uniquement de caractères alphanumériques minuscules et de traits d’union.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Ajoutez la fonction et l’appel de fonction suivants pour implémenter la création de l’inscription du groupe :
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Enregistrez et fermez le fichier **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Exécuter l’exemple d’inscription de module TPM

1. Ouvrez une invite de commandes, puis exécutez le script.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Observez la sortie pour vérifier si l’inscription a abouti.

1. Accédez au service d’approvisionnement dans le portail Azure. Cliquez sur **Gérer les inscriptions**. Notez que l’appareil TPM s’affiche dans l’onglet **Inscriptions individuelles**, sous le nom `registrationid` créé précédemment. 

    ![Comment vérifier la réussite de l’inscription du TPM dans le portail](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous prévoyez d’aller plus loin dans l’étude de l’exemple de service Java, ne supprimez pas les ressources créées dans ce démarrage rapide. Sinon, procédez aux étapes suivantes pour supprimer toutes les ressources créées lors de ce démarrage rapide.

1. Fermez la fenêtre de sortie de l’exemple Python sur votre ordinateur.
1. Si vous avez créé un appareil TPM simulé, fermez la fenêtre du simulateur TPM.
1. Accédez au service Device Provisioning dans le portail Azure, cliquez sur **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**. Sélectionnez *l’ID d’inscription* correspondant à l’entrée d’inscription créée à l’aide de ce démarrage rapide, puis cliquez sur le bouton **Supprimer** dans la partie supérieure du panneau.  


## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez créé par programmation une entrée d’inscription individuelle pour un appareil TPM et vous avez éventuellement créé un appareil simulé TPM sur votre machine et l’avez approvisionné vers votre IoT Hub à l’aide du service d’approvisionnement d’appareil Azure IoT Hub. Pour en savoir plus sur l’approvisionnement de l’appareil en profondeur, référez-vous au didacticiel relatif à l’installation du service d’approvisionnement d’appareil dans le portail Azure.

> [!div class="nextstepaction"]
> [Didacticiels relatifs au service d’approvisionnement d’appareil Azure IoT Hub](./tutorial-set-up-cloud.md)
