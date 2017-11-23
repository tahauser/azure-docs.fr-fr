---
title: "Créer un appareil de passerelle avec Azure IoT Edge | Documents Microsoft"
description: "Utilisez Azure IoT Edge pour créer un appareil de passerelle pouvant traiter des informations de plusieurs appareils"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Créer un appareil de passerelle IoT Edge pour traiter des données de processus d’autres appareils IoT – aperçu

Il y a deux manières d’utiliser des appareils IoT Edge comme passerelles :

* Connecter en aval des appareils utilisant [Azure IoT device SDK][lnk-devicesdk] ;
* Connecter des appareils n’utilisant pas le protocole IoT Hub.

Lorsque vous utilisez un appareil IoT Edge comme passerelle, vous bénéficiez des avantages suivants :

* **Multiplexage des connexions**. Tous les appareils se connectant à IoT Hub via un appareil IoT Edge utiliseront la même connexion sous-jacente.
* **Lissage du trafic**. L’appareil IoT Edge implémentera automatiquement une interruption exponentielle en cas de limitation d’IoT Hub, tout en assurant une conservation locale des messages. Votre solution sera ainsi résistante aux pics de trafic.
* **Prise en charge hors connexion limitée**. L’appareil de passerelle stockera localement les messages qui ne peuvent pas être remis à IoT Hub.

Dans cet article, nous appellerons *passerelle IoT Edge* un appareil IoT Edge utilisé comme passerelle.

>[!NOTE]
>Actuellement :
> * Les périphériques en aval ne sont pas en mesure de s’authentifier auprès de la passerelle si cette dernière est déconnectée d’IoT Hub ; et
> * les appareils IoT Edge ne peuvent pas se connecter aux passerelles IoT Edge.

## <a name="use-the-azure-iot-device-sdk"></a>Utiliser Azure IoT device SDK

Le Hub Edge installé dans tous les appareils IoT Edge expose les primitives suivantes aux appareils en aval :

* messages de l’appareil vers le cloud et du cloud vers l’appareil ;
* méthodes directes ; et
* opérations de jumeau d’appareil.

>[!NOTE]
>Actuellement, les appareils en aval ne sont pas en mesure d’utiliser le téléchargement du fichier lors de la connexion via une passerelle IoT Edge.

Lorsque vous vous connectez des appareils à une passerelle IoT Edge à l’aide d’Azure IoT device SDK, vous devez :

* configurer l’appareil en aval avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de la passerelle ; et
* vous assurer que l’appareil en aval approuve le certificat utilisé pour accepter la connexion par l’appareil de passerelle.

Lorsque vous installez le runtime Azure IoT Edge à l’aide du script de contrôle, un certificat est créé pour le Hub Edge, comme vous le faisiez dans le didacticiel Installer IoT Edge sur un appareil simulé sur [Windows] [ lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce certificat est utilisé par le Hub Edge pour accepter des connexions TLS entrantes et doit être approuvé par l’appareil en aval lors de la connexion à l’appareil de passerelle.

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer [la sécurité X.509] [ lnk-iothub-x509] dans IoT Hub, ce qui implique un certificat d’autorité de certification X.509 associé à un Hub IoT spécifique (l’*autorité de certification du Hub IoT*), ainsi qu’une série de certificats signés par cette autorité de certification et installés dans les appareils IoT Edge.

>[!IMPORTANT]
>Actuellement, les appareils IoT Edge et les appareils en aval peuvent uniquement utiliser des [jetons SAS][lnk-iothub-tokens] pour s’authentifier avec IoT Hub. Les certificats seront utilisés uniquement pour valider la connexion TLS entre le nœud terminal et l’appareil de passerelle.

Notre configuration utilise **l’autorité de certification du propriétaire du Hub IoT** en tant que :
* certificat de signature pour la configuration du runtime IoT Edge sur tous les appareils IoT Edge et en tant que
* certificat de clé publique installé dans les appareils en aval.

La solution obtenue permettra à tous les appareils d’utiliser n’importe quel appareil IoT Edge comme passerelle tant qu’ils seront connectés au même Hub IoT.

### <a name="create-the-certificates-for-test-scenarios"></a>Créer les certificats pour des scénarios de test

Vous pouvez utiliser les exemples de scripts Powershell et Bash décrits dans [Gestion des exemples de certificats d’autorités de certification] [ lnk-ca-scripts] pour générer un **certificat d’autorité de certification de propriétaire de Hub IoT** et les certificats d’appareils signées avec celui-ci.

1. Suivez l’étape 1 de l’[Exemple de gestion du certificat d’autorité de certification][lnk-ca-scripts] pour installer les scripts.
2. Suivez l’étape 2 pour générer le **certificat de l’autorité de certification propriétaire du Hub IoT**. Ce fichier sera utilisé par les appareils en aval pour valider la connexion.

Suivez les instructions suivantes pour générer un certificat pour votre appareil de passerelle.

#### <a name="bash"></a>Bash

* Exécutez `./certGen.sh create_edge_device_certificate myGateway` pour créer le nouveau certificat de l’appareil.  
  Cette opération créera les fichiers .\certs\new-edge-device.*, qui contiennent la clé publique et le PFX, et le fichier .\private\new-edge-device.key.pem, qui contient la clé privée de l’appareil.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem` pour obtenir la clé publique.
* `./private/new-edge-device.cert.pem`contient la clé privée de l’appareil.

#### <a name="powershell"></a>PowerShell

* Exécutez `New-CACertsEdgeDevice myGateway` pour créer le nouveau certificat de l’appareil.
  Cette opération créera les fichiers myEdgeDevice*, qui contiennent la clé publique, la clé privée et le PFX de ce certificat.  Lorsque vous êtes invité à entrer un mot de passe pendant le processus de signature, entrez « 1234 ».


>[!IMPORTANT]
>Cet exemple est uniquement destiné à des fins de test. Concernant les scénarios de production, reportez-vous à [Sécuriser votre déploiement IoT] [ lnk-iothub-secure-deployment] pour consulter les instructions de sécurisation de votre solution IoT et approvisionner votre certificat en conséquence.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Configurer un appareil IoT Edge en tant que passerelle

Pour configurer votre appareil IoT Edge en tant que passerelle, vous devez simplement le configurer pour utiliser le certificat d’appareil créé dans la section précédente.

Nous attribuons les noms de fichiers suivants par défaut sur la base des exemples de scripts ci-dessus :

| Sortie | Script Bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificat d’appareil | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Clé d’appareil privée | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Chaîne de certificat d’appareil | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Autorité de certification du propriétaire du Hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Comme pour l’installation décrite dans le déploiement d’Azure IoT Edge sur un appareil simulé dans [Windows] [ lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin], vous devez fournir les informations ci-dessus au runtime IoT Edge. 
 
 Dans Linux :

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

Dans Windows :

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Par défaut, les exemples de scripts ne définissent pas de phrase secrète pour la clé d’appareil privée. Si vous définissez une phrase secrète, ajoutez le paramètre suivant :

        --device-ca-passphrase {passphrase}

Le script vous invitera à définir une phrase secrète pour le certificat de l’agent Edge.
Vous devez redémarrer le runtime IoT Edge après cette commande.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Configurer une application d’appareil IoT Hub en tant qu’appareil en aval

Un appareil en aval peut être n’importe quelle application utilisant [Azure IoT device SDK][lnk-devicesdk], telle que l’application simple décrite dans [Connecter votre appareil à votre IoT Hub à l’aide de .NET] [ lnk-iothub-getstarted].

Tout d’abord, une application d’appareil en aval doit approuver le certificat **de l’autorité de certification du propriétaire de Hub IoT** afin de valider les connexions TLS aux appareils de la passerelle. Cette étape peut généralement être franchie de deux façons : au niveau du système d’exploitation, ou (pour certaines langues) au niveau de l’application.

Par exemple, pour les applications .NET, vous pouvez ajouter l’extrait de code suivant afin d’approuver un certificat au format PEM stocké dans le chemin d’accès `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Notez que les exemples de scripts mentionnés ci-dessus génèrent la clé publique dans le fichier `certs/azure-iot-test-only.root.ca.cert.pem` (Bash), ou `RootCA.pem` (Powershell).

Cette étape au niveau du système d’exploitation n’est pas la même dans Windows et dans les distributions de Linux.

La seconde étape consiste à initialiser IoT Hub device SDK avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de passerelle.
Pour ce faire, il faut ajouter la propriété `GatewayHostName` à votre chaîne de connexion d’appareil. Voici un exemple de chaîne de connexion d’un périphérique à laquelle nous avons ajouté la propriété `GatewayHostName` :

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Ces deux étapes permettront à votre application d’appareil de se connecter à l’appareil de passerelle.

## <a name="use-other-protocols"></a>Utiliser d’autres protocoles

Une des principales fonctions d’un appareil de passerelle est d’interpréter les protocoles d’appareils en aval. Pour connecter des périphériques qui n’utilisent pas le protocole IoT Hub, vous pouvez développer un module IoT Edge qui effectue cette translation et se connecte à IoT Hub au nom de l’appareil en aval.

Vous pouvez décider de créer une passerelle *transparente* ou *opaque* :

| &nbsp; | Passerelle transparente | Passerelle opaque|
|--------|-------------|--------|
| Identités stockées dans le registre des identités IoT Hub | Identités de tous les appareils connectés | Uniquement l’identité de l’appareil de passerelle |
| Jumeau d’appareil | Chaque appareil connecté possède son propre jumeau d’appareil | Seule la passerelle a un appareil et des jumeaux de module |
| Méthodes directes et messages du cloud vers l’appareil | Le cloud peut porter individuellement sur chaque appareil connecté | Le cloud ne peut porter que sur l’appareil de passerelle |
| [Limitations et quotas d’IoT Hub][lnk-iothub-throttles-quotas] | Appliquer à chaque appareil | Appliquer à l’appareil de passerelle |

Lorsque vous utilisez un modèle de passerelle opaque, tous les appareils qui se connectent via cette passerelle partagent la même file d’attente cloud sur l’appareil, qui peut contenir au maximum 50 messages. De ce fait, le modèle de passerelle opaque ne doit être utilisé que lorsque très peu d’appareils se connectent via la passerelle de chaque champ et que le trafic entre le cloud et l’appareil est faible.

Lorsque vous implémentez une passerelle opaque, votre module de translation de protocole utilise la chaîne de connexion du module.

Lorsque vous implémentez une passerelle transparente, le module crée plusieurs instances du client de l’appareil IoT Hub à l’aide des chaînes de connexion pour les appareils en aval.

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les exigences et outils de développement de modules IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md