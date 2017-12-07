## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. 

1. Une fois votre IoT Hub créé, cliquez dessus dans le tableau de bord. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.

   ![Obtention du nom d’hôte de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. Dans le volet **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez et notez la **chaîne de connexion** de votre IoT Hub. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Vous n’aurez pas besoin de cette chaîne de connexion iothubowner pour ce didacticiel d’installation. Toutefois, elle vous sera peut-être nécessaire pour certains didacticiels présentant différents scénarios IoT une fois cette installation terminée.

   ![Obtention de la chaîne de connexion de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Inscrire un appareil dans l’IoT Hub pour votre appareil

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez votre instance IoT Hub.

2. Cliquez sur **Explorateur d’appareils**.
3. Dans le volet Explorateur d’appareils, cliquez sur **Ajouter** pour ajouter un appareil à votre IoT Hub. Faites ensuite ce qui suit :

   **ID d’appareil** : entrez l’ID du nouvel appareil. Les ID d’appareil respectent la casse.

   **Type d’authentification** : sélectionnez **Clé symétrique**.

   **Générer automatiquement les clés** : cochez cette case.

   **Connecter l’appareil à IoT Hub** : cliquez sur **Activer**.

   ![Ajouter un appareil dans l’outil Device Explorer de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Cliquez sur **Save**.
5. Une fois que l’appareil est créé, ouvrez-le dans le volet **Explorateur d’appareils**.
6. Notez la clé primaire de la chaîne de connexion.

   ![Obtention de la chaîne de connexion de l’appareil](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
