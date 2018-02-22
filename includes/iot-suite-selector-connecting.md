> [!div class="op_single_selector"]
> * [C sur Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C sur Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (générique)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js sur Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C sur Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

Dans ce didacticiel, vous allez créer un périphérique **Condenseur** qui envoie les données de télémétrie suivantes à la [solution préconfigurée](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md) de surveillance à distance :

* Température
* Pression
* Humidité

Par souci de simplicité, le code génère des exemples de valeurs de données de télémétrie pour le **Condenseur**. Vous pouviez étendre l’exemple en connectant des capteurs réels sur votre périphérique et envoyer les données de télémétrie réelles.

Le périphérique en exemple également :

* Envoie des métadonnées à la solution pour décrire ses fonctionnalités.
* Répond aux actions déclenchées à partir de la page **Périphériques** dans la solution.
* Répond aux modifications de configuration envoyées à partir de la page **Périphériques** dans la solution.

Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Avant de commencer

Avant d’écrire du code pour votre appareil, vous devez approvisionner votre solution préconfigurée de surveillance à distance et approvisionner un nouvel appareil personnalisé dans cette solution.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Approvisionner la solution préconfigurée de surveillance à distance

Le périphérique **Condenseur** que vous créez dans ce didacticiel envoie des données à une instance de la solution préconfigurée [de surveillance à distance](../articles/iot-suite/iot-suite-remote-monitoring-explore.md). Si vous n’avez pas déjà configuré la solution préconfigurée de surveillance à distance dans votre compte Azure, consultez la rubrique [Déployez la solution préconfigurée de surveillance à distance](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Au terme du processus d’approvisionnement de la solution de surveillance à distance, cliquez sur **Lancer** pour ouvrir le tableau de bord de la solution dans votre navigateur.

![Tableau de bord de la solution](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Configurer votre appareil dans la solution de surveillance à distance

> [!NOTE]
> Si vous avez déjà approvisionné un appareil dans votre solution, vous pouvez ignorer cette étape. Vous avez besoin de la chaîne de connexion de l’appareil, que vous pouvez récupérer depuis le portail Azure lors de la création de l’application cliente.

Pour qu’un appareil puisse se connecter à la solution préconfigurée, il doit s’identifier auprès d’IoT Hub à l’aide d’informations d’identification valides. Vous avez l’opportunité d’enregistrer la chaîne de connexion de l’appareil qui contient les informations d’identification lorsque vous ajoutez la solution à l’appareil. La chaîne de connexion de l’appareil sera ajoutée dans votre application cliente dans la suite de ce didacticiel.

Pour ajouter un périphérique à votre solution de surveillance à distance, procédez comme suit dans la page **Périphériques** de la solution :

1. Choisissez **+ Nouvel appareil**, puis **Physique** comme **Type de périphérique** :

    ![Configurer un périphérique physique](media/iot-suite-selector-connecting/devicesprovision.png)

1. Entrez **Physique-condenseur** comme ID de périphérique. Choisissez les options **Clé symétrique** et **Générer automatiquement des clés** :

    ![Choisissez les options de périphérique](media/iot-suite-selector-connecting/devicesoptions.png)

1. Choisissez **Appliquer**. Notez quelque part les valeurs de l’**ID de l’appareil**, de la **Clé primaire** et de la **Clé primaire de la chaîne de connexion** :

    ![Récupérer les informations d’identification](media/iot-suite-selector-connecting/credentials.png)

Pour trouver les informations d’identification que votre périphérique doit utiliser pour se connecter à la solution préconfigurée, accédez au portail Azure dans votre navigateur. Connectez-vous à votre abonnement.

Vous avez maintenant configuré un périphérique physique dans la solution de surveillance à distance solution préconfigurée. Dans les sections suivantes, vous mettez en œuvre l’application cliente qui utilise les informations d’identification du périphérique pour se connecter à votre solution.

L’application cliente met en œuvre le modèle de périphérique **Condenseur** intégré. Un modèle de périphérique de solution préconfigurée spécifie les éléments suivants concernant un périphérique :

* Les propriétés que le périphérique signale à la solution. Par exemple, un périphérique **Condenseur** fournit des informations sur son microprogramme et son emplacement.
* Les types de données de télémétrie envoyés par le périphérique à la solution. Par exemple, un périphérique **Condenseur** envoie des valeurs sur la température, l’humidité et la pression.
* Les méthodes que vous pouvez planifier à partir de la solution pour s’exécuter sur le périphérique. Par exemple, un périphérique **Condenseur** doit mettre en œuvre des méthodes **Redémarrer**, **FirmwareUpdate**, **EmergencyValveRelease** et  **IncreasePressure**.