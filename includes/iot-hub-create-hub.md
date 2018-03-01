1. Connectez-vous au [portail Azure][lnk-portal].
1. Sélectionnez **Créer une ressource** > **Internet des objets** > **IoT Hub**.
   
    ![Capture d’écran de l’accès à IoT Hub dans le portail Azure][1]

1. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre hub IoT :

   * **Nom** : créer un nom de votre hub IoT. Si le nom saisi est valide, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Niveau de tarification et de mise à l’échelle** : pour ce didacticiel, sélectionnez le niveau **F1 gratuit**. Pour plus d’informations, consultez [Niveau de tarification et de mise à l’échelle][lnk-pricing].

   * **Groupe de ressources** : créez un groupe de ressources pour héberger le hub IoT ou utilisez-en un existant. Pour plus d’informations, consulter [Utilisation des groupes de ressources pour gérer vos ressources Azure][lnk-resource-groups]

   * **Emplacement**: sélectionnez l’emplacement le plus proche de vous.

   * **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

    ![Fenêtre IoT Hub][2]

1. Cliquez sur **Créer**. La création de votre IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md