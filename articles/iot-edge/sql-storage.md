---
title: Module SQL d’Azure IoT Edge | Microsoft Docs
description: Stocker des données en périphérie à l’aide des modules Microsoft SQL, avec Azure Functions pour mettre les données en forme.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban, ebertrams
ms.date: 02/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ce3c3abd00dba23887b5f811af6cab8d2c83323d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Stocker des données en périphérie avec les bases de données SQL Server

Utilisez les appareils Azure IoT Edge pour stocker les données générées en périphérie. Les appareils qui n’ont accès à Internet que de façon intermittente peuvent gérer leurs propres bases de données et signaler les modifications dans le cloud chaque fois qu’ils sont connectés. Les appareils qui ont été programmés pour envoyer uniquement les données critiques dans le cloud peuvent enregistrer le reste des données pour les charger en bloc régulièrement. Une fois dans le cloud, les données structurées peuvent être partagées avec d’autres services Azure, par exemple pour créer un modèle d’apprentissage automatique. 

Cet article fournit des instructions pour le déploiement d’une base de données SQL Server sur un appareil IoT Edge. Azure Functions, exécuté sur l’appareil IoT Edge, structure les données entrantes, puis les envoie vers la base de données. Les étapes décrites dans cet article peuvent également être appliquées aux autres bases de données qui fonctionnent dans des conteneurs, telles que MySQL ou PostgreSQL. 

## <a name="prerequisites"></a>Prérequis
 

Avant de suivre les instructions de cet article, nous vous conseillons de prendre connaissance des didacticiels suivants :
* Déployer Azure IoT Edge sur un appareil simulé dans [Windows](tutorial-simulate-device-windows.md) ou [Linux](tutorial-simulate-device-linux.md)
* [Déploiement d’Azure Function en tant que module IoT Edge](tutorial-deploy-function.md)

Les articles suivants ne sont pas obligatoires pour suivre ce didacticiel, mais ils peuvent fournir un contexte utile :
* [Exécuter l’image du conteneur de SQL Server 2017 avec Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Utiliser Visual Studio Code pour développer et déployer des fonctions Azure Functions sur Azure IoT Edge | Microsoft Docs](how-to-vscode-develop-azure-function.md)

Une fois que vous aurez suivi tous les didacticiels requis, vous disposerez de la configuration requise sur votre ordinateur : 
* Une installation Azure IoT Hub active.
* Un appareil IoT Edge avec au moins 2 Go de RAM et un disque dur de 2 Go.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extension Azure IoT Edge pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extension C# pour Visual Studio Code (développée par OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/)
* [Kit de développement logiciel (SDK) .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Script de contrôle IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Modèle AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Un IoT Hub actif avec au moins un appareil IoT Edge.

Pour ce didacticiel, vous pouvez utiliser des conteneurs Windows et Linux sur des architectures de processeur x64. SQL Server ne prend pas en charge les processeurs ARM.

## <a name="deploy-a-sql-server-container"></a>Déployer un conteneur SQL Server

Dans cette section, vous allez ajouter une base de données MS-SQL à votre appareil IoT Edge simulé. Utilisez l’image conteneur docker SQL Server 2017, disponible sous forme de conteneur [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) et de conteneur [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Déployer SQL Server 2017

Par défaut, le code de cette section crée un conteneur avec l’édition Développeur gratuite de SQL Server 2017. Si vous souhaitez plutôt exécuter des éditions de production, consultez [Exécuter des images de conteneur de production](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) pour plus d’informations. 

À l’étape 3, vous allez ajouter des options de création au conteneur SQL Server, qui sont importantes pour établir des variables d’environnement et un stockage persistant. Les [variables d’environnement](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) configurées acceptent le contrat de licence utilisateur final et définissent un mot de passe. Le [stockage persistant](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) est configuré à l’aide de [montages](https://docs.docker.com/storage/volumes/). Les montages créent le conteneur SQL Server 2017 avec un conteneur de volume *sqlvolume* attaché pour que les données persistent même si le conteneur est supprimé. 

1. Ouvrez le fichier `deployment.json` dans Visual Studio Code. 
2. Remplacez la section **modules** du fichier par le code suivant : 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "localhost:5000/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
   ```

3. Selon le système d’exploitation que vous exécutez, mettez à jour les paramètres du module SQL avec le code suivant : 

   * Windows :

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"C:\\\\mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}"
      ```

   * Linux :

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\"Env\": [\"ACCEPT_EULA=Y\",\"MSSQL_SA_PASSWORD=Strong!Passw0rd\"],\"HostConfig\": {\"Mounts\": [{\"Target\": \"/var/opt/mssql\",\"Source\": \"sqlVolume\",\"Type\": \"volume\"}],\"PortBindings\": {\"1433/tcp\": [{\"HostPort\": \"1401\"}]}}}"
      ```

4. Enregistrez le fichier . 
5. Dans la palette de commandes VS Code, sélectionnez **Edge: Create deployment for Edge device** (Edge : Créer un déploiement pour l’appareil Edge). 
6. Sélectionnez votre ID d’appareil IoT Edge.
7. Sélectionnez le fichier `deployment.json` mis à jour. Dans la fenêtre de sortie, vous pouvez voir les sorties correspondantes pour votre déploiement. 
8. Pour démarrer votre runtime Edge, sélectionnez **Edge: Start Edge** (Edge : Démarrer Edge).

>[!TIP]
>Chaque fois que vous créez un conteneur SQL Server dans un environnement de production, vous devez [modifier le mot de passe administrateur par défaut du système](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

## <a name="create-the-sql-database"></a>Créer la base de données SQL

Cette section vous guide tout au long de la configuration de la base de données SQL pour stocker les données de température provenant des capteurs connectés à l’appareil IoT Edge. Si vous utilisez un appareil simulé, ces données proviennent du connecteur *tempSensor*. 

Dans un outil de ligne de commande, connectez-vous à votre base de données : 

* Conteneur Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Conteneur Linux
   ```bash
   docker exec -it sql bash
   ```

Ouvrez l’outil de commande SQL : 

* Conteneur Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Conteneur Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Créez votre base de données : 

* Conteneur Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Conteneur Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Définissez votre table : 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Vous pouvez personnaliser votre fichier Docker SQL Server pour configurer automatiquement le déploiement de votre serveur SQL Server sur plusieurs appareils IoT Edge. Pour plus d’informations, consultez le [projet de démonstration du conteneur Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Comprendre la connexion SQL

Dans d’autres didacticiels, nous utilisons des itinéraires pour permettre aux conteneurs de communiquer tout en restant isolés les uns des autres. Cependant, lorsque vous travaillez avec une base de données SQL Server, une relation plus étroite est nécessaire. 

IoT Edge génère automatiquement un réseau de pont (Linux) ou NAT (Windows) au démarrage. Le réseau s’appelle **azure-iot-edge**. Si vous devez déboguer cette connexion, vous pouvez rechercher ses propriétés dans la ligne de commande :

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```bash
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge peut également résoudre le DNS d’un nom de conteneur via Docker, de sorte qu’il est inutile de faire référence à la base de données SQL Server en utilisant son adresse IP. 

Par exemple, voici la chaîne de connexion que nous utilisons dans la section suivante : 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Vous pouvez voir que la chaîne de connexion fait référence au conteneur par son nom, **sql**. Si vous avez modifié le nom du module, mettez aussi à jour cette chaîne de connexion. Sinon, passez à la section suivante. 

## <a name="update-your-azure-function"></a>Mettre à jour votre fonction Azure
Pour envoyer les données vers votre base de données, mettez à jour la fonction Azure FilterFunction que vous avez créée dans le didacticiel précédent. Modifiez ce fichier afin qu’il structure les de données provenant des capteurs, puis les stockent dans une table SQL. 

1. Dans Visual Studio Code, ouvrez votre dossier FilterFunction. 
2. Remplacez le fichier run.csx par le code suivant qui inclut la chaîne de connexion SQL de la section précédente : 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>Mettre à jour votre image de conteneur

Pour appliquer les modifications apportées, mettez à jour votre image de conteneur, publiez-la et redémarrez IoT Edge.

1. Dans Visual Studio Code, développez le dossier **Docker**. 
2. En fonction de la plateforme que vous utilisez, développez le dossier **windows-nano** ou **linux-x64**. 
3. Cliquez avec le bouton droit sur le fichier **Dockerfile** et sélectionnez **Build IoT Edge module Docker image** (Créer une image Docker du module IoT Edge).
4. Accédez au dossier du projet **FilterFunction**, puis cliquez sur **Sélectionner un dossier en tant que EXE_DIR**.
5. Dans la zone de texte contextuelle en haut de la fenêtre de Visual Studio Code, entrez le nom de l’image. Par exemple : `<your container registry address>/filterfunction:latest`. Si vous effectuez un déploiement dans le registre local, le nom doit être `<localhost:5000/filterfunction:latest>`.
6. Dans la palette de commandes VS Code, sélectionnez **Edge: Push IoT Edge module Docker image** (Edge : transmettre l’image Docker du module IoT Edge). 
7. Dans la zone de texte contextuelle, entrez le même nom d’image. 
8. Dans la palette de commandes VS Code, sélectionnez **Edge: Restart Edge** (Edge : redémarrer Edge).

## <a name="view-the-local-data"></a>Consulter les données locales

Une fois les conteneurs redémarrés, les données provenant des capteurs de température sont stockées dans une base de données SQL Server 2017 locale sur votre appareil IoT Edge. 

Dans un outil de ligne de commande, connectez-vous à votre base de données : 

* Conteneur Windows
   ```cmd
   docker exec -it sql cmd
   ```

* Conteneur Linux
   ```bash
   docker exec -it sql bash
   ```

Ouvrez l’outil de commande SQL : 

* Conteneur Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Conteneur Linux
   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Consultez les données : 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [configurer les images de conteneur SQL Server 2017 sur Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Pour consulter les ressources, les commentaires et les problèmes connus, visitez le [référentiel GitHub mssql-docker](https://github.com/Microsoft/mssql-docker).
