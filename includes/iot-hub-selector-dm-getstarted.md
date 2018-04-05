> [!div class="op_single_selector"]
> * [Device: Node.js Service: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Device: Node.js Service: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [Appareil : Service C# : C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Appareil : service Java : Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Appareil : service Python : Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Les applications principales peuvent utiliser des primitives Azure IoT Hub, comme [jumeau d’appareil][lnk-devtwin] et [méthodes directes][lnk-c2dmethod], afin de démarrer et surveiller à distance les actions de gestion d’appareil sur les appareils. Ce didacticiel illustre la manière dont une application principale et une application pour périphérique fonctionnent conjointement afin de lancer et de surveiller le redémarrage à distance d’un appareil à l’aide de IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Utilisez une méthode directe pour lancer des actions de gestion d’appareils (redémarrage, réinitialisation des paramètres d’usine et mise à jour du microprogramme) à partir d’une application principale dans le cloud. L’appareil est chargé de :

* Gérer la requête de méthode envoyée à partir d’IoT Hub.
* Démarrer l’action correspondante spécifique à l’appareil sur l’appareil.
* Fournir à IoT Hub des mises à jour de l’état via des *propriétés signalées*.

Vous pouvez utiliser une application principale dans le cloud pour exécuter des requêtes sur la représentation d’appareil afin d’indiquer la progression des actions de gestion de votre appareil.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
