## <a name="create-an-iot-hub"></a>Créer un hub IoT
Créez un IoT Hub pour que votre application de périphérique simulé puisse se connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][lnk-portal].

1. Cliquez sur **Nouveau** > **Internet des objets** > **IoT Hub**.
   
    ![Barre de lancement du portail Azure][1]

1. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre IoT Hub :

   * **Nom** : créer un nom de votre IoT Hub. Si le nom saisi est valide, une coche verte s’affiche.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Niveau de tarification et de mise à l’échelle** : pour ce didacticiel, sélectionnez le niveau **F1 gratuit**. Pour plus d’informations, consultez [Niveau de tarification et de mise à l’échelle][lnk-pricing].

   * **Groupe de ressources** : créez un groupe de ressources pour héberger l’IoT Hub ou utilisez-en un existant. Pour plus d’informations, consulter [Utilisation des groupes de ressources pour gérer vos ressources Azure][lnk-resource-groups]

   * **Emplacement**: sélectionnez l’emplacement le plus proche de vous.

   * **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

    ![Fenêtre IoT Hub][2]

1. Cliquez sur **Créer**. La création de votre IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.

1. Lorsque votre IoT Hub est prêt, cliquez sur sa vignette dans le portail Azure pour ouvrir la fenêtre de ses propriétés. Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.
   
    ![Nouvelle fenêtre IoT Hub][4]

1. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis notez la chaîne de connexion IoT Hub dans la fenêtre **iothubowner**. Consultez la rubrique [Access Control][lnk-access-control] du « Guide du développeur IoT Hub » pour plus d’informations.
   
    ![Stratégies d’accès partagé][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
