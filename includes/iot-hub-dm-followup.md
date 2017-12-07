## <a name="customize-and-extend-the-device-management-actions"></a>Personnaliser et étendre les actions de gestion d’appareils

Vos solutions IoT peuvent étendre l’ensemble défini de modèles de gestion d’appareils ou activer des modèles personnalisés en utilisant les primitives de la méthode cloud-à-appareil et du jumeau d’appareil. La réinitialisation des paramètres d’usine, la mise à jour du microprogramme, la mise à jour logicielle, la gestion de l’alimentation, la gestion du réseau et de la connectivité, et le chiffrement des données sont d’autres exemples d’actions de gestion des appareils.

## <a name="device-maintenance-windows"></a>Fenêtres de maintenance d’appareil

En règle générale, vous configurez des appareils pour effectuer des actions à un moment qui minimise les interruptions et les temps d’arrêt. Les fenêtres de maintenance d’appareil constituent un modèle couramment utilisé pour définir l’heure à laquelle un appareil doit mettre à jour sa configuration. Vos solutions principales peuvent utiliser les propriétés souhaitées de la représentation d’appareil pour définir et activer une stratégie sur votre appareil qui permet d’obtenir une fenêtre de maintenance. Lorsqu’un appareil reçoit la stratégie de fenêtre de maintenance, il peut utiliser la propriété signalée de la représentation d’appareil pour indiquer l’état de la stratégie. L’application principale peut ensuite utiliser des requêtes de représentation d’appareil pour certifier la conformité des appareils et de chaque stratégie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher un redémarrage à distance sur un appareil. Vous avez utilisé les propriétés signalées pour indiquer le moment du dernier redémarrage de l’appareil et vous avez interrogé le jumeau d’appareil pour découvrir l’heure du dernier redémarrage de l’appareil à partir du cloud.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez :

[Didacticiel : Mettre à jour un microprogramme][lnk-fwupdate]

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

Afin d’approfondir l’apprentissage de IoT Hub, consultez [Getting started with IoT Edge][lnk-iot-edge] (Bien démarrer avec IoT Edge).

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-edge/tutorial-simulate-device-linux.md