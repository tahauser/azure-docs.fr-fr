## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous allez créer une application console .NET qui crée une identité d’appareil dans le registre d’identités de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Reportez-vous à la section Registre d’identité du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. En exécutant cette application console, une clé et un ID d’appareil uniques sont générés. Votre appareil utilise ces valeurs pour s’identifier lorsqu’il envoie des messages d’appareil-à-cloud à IoT Hub. Les ID d’appareil respectent la casse.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à une nouvelle solution en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **CreateDeviceIdentity** et nommez la solution **IoTHubGetStarted**.

    ![Nouveau projet Visual C# Bureau classique Windows][10]

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **CreateDeviceIdentity**, puis cliquez sur **Gérer les packages Nuget**.

1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet][11]

1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Cette méthode crée une identité d’appareil avec l’ID **myFirstDevice**. (si cet ID d’appareil existe déjà dans le registre d’identité, le code récupère simplement les informations d’appareil existantes.) L’application affiche ensuite la clé primaire pour cette identité. Vous utilisez cette clé dans l’application de périphérique simulé pour vous connecter à votre IoT Hub.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Exécutez cette application et notez la clé de l’appareil.

    ![Clé d’appareil générée par l’application][12]

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Le registre des identités stocke les ID et les clés d’appareil à utiliser en tant qu’informations d’identification de sécurité. Il stocke également un indicateur activé/désactivé pour chaque appareil pouvant être utilisé pour désactiver l’accès de cet appareil. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
