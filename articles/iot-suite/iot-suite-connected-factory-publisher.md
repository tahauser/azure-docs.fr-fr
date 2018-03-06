---
title: "Utiliser le serveur de publication OPC de l’usine connectée Azure IoT Suite | Microsoft Docs"
description: "Guide pratique pour créer et déployer l’implémentation de référence du serveur de publication OPC de l’usine connectée."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Serveur de publication OPC pour Azure IoT Edge

Cet article décrit comment utiliser l’implémentation de référence du serveur de publication OPC. L’implémentation de référence montre comment utiliser Azure IoT Edge pour :

- Se connecter à des serveurs OPC UA existants.
- Publier les données de télémétrie encodées JSON de ces serveurs au format *Pub/Sub* OPC UA sur Azure IoT Hub, à l’aide d’une charge utile JSON. Vous pouvez utiliser n’importe lequel des protocoles de transport pris en charge par Azure IoT Edge.

Cette application de référence comprend :

- Un *client* OPC UA pour se connecter aux serveurs OPC UA existants sur votre réseau.
- Un *serveur* OPC UA à l’écoute sur le port 62222, que vous pouvez utiliser pour gérer le contenu publié.

L’application est implémentée avec .NET Core et peut s’exécuter sur les plateformes prises en charge par .NET Core.

Le serveur de publication implémente la logique de nouvelle tentative quand il établit des connexions à des points de terminaison. Le serveur de publication s’attend à ce que les points de terminaison répondent avant un nombre maximal spécifié de demandes de connexion persistante. Cette logique de nouvelle tentative permet au serveur de publication de détecter des conditions telles qu’une panne d’alimentation d’un serveur OPC UA.

Pour chaque intervalle de publication sur un serveur OPC UA, est créé un abonnement distinct sur lequel tous les nœuds avec cet intervalle de publication sont mis à jour.

Pour réduire la charge réseau, le serveur de publication prend en charge le traitement par lot des données envoyées à IoT Hub. Un lot est envoyé à IoT Hub uniquement quand la taille de paquet configurée est atteinte.

Comme cette application utilise la pile de référence OPC UA d’OPC Foundations, des restrictions de licences s’appliquent. Pour obtenir la documentation OPC UA et connaître les termes du contrat de licence, accédez à http://opcfoundation.github.io/UA-.NETStandardLibrary/.

Le code source du serveur de publication OPC se trouve dans le dépôt GitHub du [Serveur de publication OPC pour Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher).

## <a name="prerequisites"></a>Composants requis

Pour générer l’application, vous avez besoin du kit [.NET Core SDK 1.1](https://docs.microsoft.com/dotnet/core/sdk) correspondant à votre système d’exploitation.

## <a name="build-the-application"></a>Création de l'application

### <a name="as-native-windows-application"></a>En tant qu’application Windows native

Ouvrez le projet `OpcPublisher.sln` avec Visual Studio 2017 et générez la solution en appuyant sur F7.

### <a name="as-docker-container"></a>En tant que conteneur Docker

Pour générer l’application en tant que conteneur Windows Docker, utilisez le fichier de configuration `Dockerfile.Windows`.

Pour générer l’application en tant que conteneur Linux Docker, utilisez le fichier de configuration `Dockerfile`.

À partir de la racine du dépôt sur votre ordinateur de développement, tapez la commande suivante dans une fenêtre de console :

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

L’option `-f` pour `docker build` est facultative et le comportement par défaut consiste à utiliser le fichier de configuration `Dockerfile`.

Docker vous permet également de générer directement à partir d’un dépôt Git. Vous pouvez générer un conteneur Linux Docker avec la commande suivante :

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Configurer les nœuds OPC UA à publier

Pour configurer les nœuds OPC UA qui doivent avoir leurs valeurs publiées sur Azure IoT Hub, créez un fichier de configuration au format JSON. Le nom par défaut de ce fichier de configuration est `publishednodes.json`. L’application met à jour et enregistre ce fichier de configuration quand elle utilise les méthodes de serveur OPC UA **PublishNode** ou **UnpublishNode**.

La syntaxe du fichier de configuration est la suivante :

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Exécution de l'application

### <a name="command-line-options"></a>Options de ligne de commande

Pour afficher l’utilisation complète de l’application, utilisez l’option de ligne de commande `--help`. L’exemple suivant illustre la structure d’une commande :

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` est le nom de l’application OPC UA à utiliser. Ce paramètre est obligatoire. Le nom de l’application sert également à inscrire le serveur de publication auprès du Registre des appareils IoT Hub.

`IoT Hubconnectionstring` est la chaîne de connexion du propriétaire IoT Hub. Ce paramètre est facultatif.

Les options suivantes sont prises en charge :

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Vous pouvez utiliser les variables d’environnement suivantes pour contrôler l’application :
- `_HUB_CS` : définit la chaîne de connexion du propriétaire IoT Hub
- `_GW_LOGP` : définit le nom de fichier du fichier journal à utiliser
- `_TPC_SP` : définit le chemin où stocker les certificats des stations approuvées
- `_GW_PNFP` : définit le nom du fichier de configuration de publication

Les arguments de ligne de commande remplacent les paramètres de variable d’environnement.

En règle générale, vous spécifiez la chaîne de connexion du propriétaire IoT Hub uniquement lors du premier démarrage de l’application. La chaîne de connexion est chiffrée et stockée dans le magasin de certificats de la plateforme.

Pour les appels suivants, la chaîne de connexion est lue à partir du magasin de certificats de la plateforme et réutilisée. Si vous spécifiez la chaîne de connexion à chaque démarrage, l’appareil dans le Registre des appareils IoT Hub est supprimé et recréé à chaque fois.

### <a name="native-on-windows"></a>Mode natif sur Windows

Pour exécuter l’application en mode natif sur Windows, ouvrez le projet `OpcPublisher.sln` avec Visual Studio 2017, générez la solution et publiez-la. Vous pouvez démarrer l’application dans le **répertoire cible** sur lequel vous avez publié avec la commande suivante :

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Utiliser un conteneur généré automatiquement

Pour exécuter l’application dans un conteneur généré automatiquement, générez puis démarrez votre propre conteneur :

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Utiliser un conteneur à partir de hub.docker.com

Un conteneur prédéfini est disponible sur DockerHub. Pour le démarrer, exécutez la commande suivante :

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Important lors de l’utilisation d’un conteneur

#### <a name="access-to-the-publisher-opc-ua-server"></a>Accès au serveur de publication OPC UA

Le serveur de publication OPC UA par défaut est à l’écoute sur le port 62222. Pour exposer ce port d’entrée dans un conteneur, vous devez utiliser l’option `docker run` `-p` :

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Activer la résolution de noms interconteneurs

Pour activer la résolution de noms à partir du conteneur vers d’autres conteneurs, vous devez :

- Créer un réseau de pont docker défini par l’utilisateur.
- Connecter le conteneur au réseau à l’aide de l’option `--network`.
- Affecter un nom au conteneur à l’aide de l’option `--name`.

L’exemple suivant montre ces options de configuration :

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Le conteneur est maintenant accessible par d’autres conteneurs sur le réseau en utilisant le nom `publisher`.

#### <a name="assign-a-hostname"></a>Attribuer un nom d’hôte

Le serveur de publication utilise le nom d’hôte de l’ordinateur sur lequel il s’exécute pour la génération de certificat et de point de terminaison. Docker choisit un nom d’hôte aléatoire, sauf si vous en définissez un avec l’option `-h`. Voici un exemple qui montre comment affecter `publisher` comme nom d’hôte interne du conteneur :

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Utilisation de montages de liaisons (système de fichiers partagé)

Dans certains scénarios, vous souhaitez lire des informations de configuration à partir de, ou écrire des fichiers journaux vers, des emplacements sur l’hôte au lieu d’utiliser le système de fichiers du conteneur. Pour configurer ce comportement, utilisez l’option `-v` de `docker run` en mode de montage de liaison. Par exemple :

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Magasin pour les certificats X509

Le stockage de certificats X509 ne fonctionne pas avec les montages de liaisons, car les autorisations du chemin vers le magasin doivent être `rw` pour le propriétaire. Au lieu de cela, vous devez utiliser l’option `-v` de `docker run` en mode volume.

## <a name="debug-the-application"></a>Déboguer l’application

### <a name="native-on-windows"></a>Mode natif sur Windows

Ouvrez le projet `OpcPublisher.sln` avec Visual Studio 2017 et démarrez le débogage de l’application en appuyant sur F5.

### <a name="in-a-docker-container"></a>Dans un conteneur Docker

Visual Studio 2017 prend en charge le débogage des applications dans un conteneur Docker avec `docker-compose`. Toutefois, cette méthode ne vous permet pas de transmettre des paramètres de ligne de commande.

Une autre option de débogage prise en charge par VS2017 consiste à déboguer sur `ssh`. Vous pouvez utiliser le fichier de configuration de build Docker `Dockerfile.ssh` à la racine du dépôt pour créer un conteneur compatible avec SSH :

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Vous pouvez maintenant démarrer le conteneur pour déboguer le serveur de publication :

```cmd/sh
docker run -it publisherssh
```

Dans le conteneur, vous devez démarrer le démon **ssh** manuellement :

```cmd/sh
service ssh start
```

À ce stade, vous pouvez créer une session **ssh** en tant qu’utilisateur `root` avec le mot de passe `Passw0rd`.

Pour préparer le débogage de l’application dans le conteneur, effectuez les étapes supplémentaires suivantes :

1. Côté hôte, créez un fichier `launch.json` :

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Générez votre projet et publiez-le dans un répertoire de votre choix.

1. Utilisez un outil tel que `WinSCP` pour copier les fichiers publiés dans le répertoire `/root/publisher` du conteneur. Si vous choisissez d’utiliser un autre répertoire, mettez à jour la propriété `cdw` dans le fichier `launch.json`.

Vous pouvez maintenant démarrer le débogage en exécutant la commande suivante dans la fenêtre Commande de Visual Studio :

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Étapes suivantes

Comme étape suivante, nous vous suggérons de découvrir comment [Déployer une passerelle sur Windows ou Linux pour la solution préconfigurée d’usine connectée](iot-suite-connected-factory-gateway-deployment.md).