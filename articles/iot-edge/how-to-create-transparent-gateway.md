---
title: "Créer un appareil de passerelle transparent avec Azure IoT Edge | Microsoft Docs"
description: "Utilisez Azure IoT Edge pour créer un appareil de passerelle transparent pouvant traiter des informations de plusieurs appareils"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0ea4d8ec51211f1208083d3f93c3c100dc54e6b0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Créer un appareil IoT Edge servant de passerelle transparente - Aperçu

Cet article fournit des instructions détaillées pour utiliser un appareil IoT Edge en tant que passerelle transparente. Dans le cadre de cet article, le terme *passerelle IoT Edge* fait référence à un appareil IoT Edge utilisé en tant que passerelle transparente. Pour plus d’informations, consultez la rubrique [Comment un appareil IoT Edge peut être utilisé en tant que passerelle][lnk-edge-as-gateway], qui propose une vue d’ensemble conceptuelle. 

>[!NOTE]
>Actuellement :
> * Si la passerelle est déconnectée d’IoT Hub, les appareils en aval ne peuvent pas s’authentifier auprès de la passerelle.
> * les appareils IoT Edge ne peuvent pas se connecter aux passerelles IoT Edge.

## <a name="understand-the-azure-iot-device-sdk"></a>Comprendre le kit de développement logiciel de l’appareil Azure IoT


Le Hub Edge installé dans tous les appareils IoT Edge expose les primitives suivantes aux appareils en aval :

* messages de l’appareil vers le cloud et du cloud vers l’appareil
* méthodes directes
* opérations de jumeau d’appareil

Actuellement, les appareils en aval ne sont pas en mesure d’utiliser le téléchargement du fichier lors de la connexion via une passerelle IoT Edge.

Quand vous connectez des appareils à une passerelle IoT Edge à l’aide d’Azure IoT device SDK, vous devez :

* configurer l’appareil en aval avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de la passerelle ; et
* vous assurer que l’appareil en aval approuve le certificat utilisé pour accepter la connexion par l’appareil de passerelle.

Lorsque vous installez le runtime Azure IoT Edge à l’aide du script de contrôle, un certificat est créé pour le Hub Edge, comme vous le faisiez dans le didacticiel [Installer IoT Edge sur un appareil simulé sur Windows][lnk-tutorial1-win] et [Linux][lnk-tutorial1-lin]. Ce certificat est utilisé par le Hub Edge pour accepter des connexions TLS entrantes et doit être approuvé par l’appareil en aval lors de la connexion à l’appareil de passerelle.

Vous pouvez créer n’importe quelle infrastructure de certificat permettant l’approbation requise pour votre topologie de l’appareil à la passerelle. Dans cet article, nous nous basons sur la même configuration de certificat que vous utiliseriez pour activer [la sécurité AC X.509][lnk-iothub-x509] dans IoT Hub, ce qui implique un certificat AC X.509 associé à un Hub IoT spécifique (*l’autorité de certification propriétaire du Hub IoT*), ainsi qu’une série de certificats signés par cette autorité de certification et installés dans les appareils IoT Edge.

>[!IMPORTANT]
>Actuellement, les appareils IoT Edge et les appareils en aval peuvent uniquement utiliser des [jetons SAS][lnk-iothub-tokens] pour s’authentifier avec IoT Hub. Les certificats seront utilisés uniquement pour valider la connexion TLS entre le nœud terminal et l’appareil de passerelle.

Notre configuration utilise **l’autorité de certification du propriétaire du Hub IoT** en tant que :
* certificat de signature pour la configuration du runtime IoT Edge sur tous les appareils IoT Edge ; et
* certificat de clé publique installé dans les appareils en aval.

La solution obtenue permet à tous les appareils d’utiliser n’importe quel appareil IoT Edge comme passerelle tant qu’ils sont connectés au même Hub IoT.

## <a name="create-the-certificates-for-test-scenarios"></a>Créer les certificats pour des scénarios de test

Vous pouvez utiliser les exemples de scripts Powershell et Bash décrits dans [Gestion des exemples de certificats d’autorités de certification] [ lnk-ca-scripts] pour générer un **certificat d’autorité de certification de propriétaire de Hub IoT** et les certificats d’appareils signées avec celui-ci.

>[!IMPORTANT]
>Cet exemple est uniquement destiné à des fins de test. Concernant les scénarios de production, reportez-vous à [Sécuriser votre déploiement IoT] [ lnk-iothub-secure-deployment] pour consulter les instructions de sécurisation de votre solution IoT et approvisionner votre certificat en conséquence.


1. Cloner les Kits de développement logiciel (SDK) et bibliothèques Microsoft Azure IoT pour C depuis GitHub :

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Pour installer les scripts de certificat, suivez les instructions dans **Étape 1 - configuration initiale** de [Gestion de l’exemple de certificat AC][lnk-ca-scripts]. 
3. Pour générer **l’AC propriétaire IoT hub**, suivez les instructions dans **Étape 2 - créer la chaîne de certificat**. Ce fichier est utilisé par des appareils en aval pour valider la connexion.
4. Pour générer un certificat pour votre appareil de passerelle, utilisez les instructions Bash ou PowerShell :

### <a name="bash"></a>Bash

Créer le nouveau certificat de l’appareil :

   ```bash
   ./certGen.sh create_edge_device_certificate myGateway
   ```

De nouveaux fichiers sont créés : .\certs\new-edge-device.* , qui contient la clé publique et le PFX, et .\private\new-edge-device.key.pem, qui contient la clé privée de l’appareil.
 
Dans le répertoire `certs`, exécutez la commande suivante pour obtenir la chaîne complète de la clé publique de l’appareil :

   ```bash
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Créer le nouveau certificat de l’appareil : 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

Les nouveaux fichiers myEdgeDevice* sont créés, qui contiennent la clé publique, la clé privée et le PFX de ce certificat. 

Lorsque vous êtes invité à entrer un mot de passe pendant le processus de signature, entrez « 1234 ».

## <a name="configure-a-gateway-device"></a>Configurer un appareil de passerelle

Pour configurer votre appareil IoT Edge en tant que passerelle, vous devez simplement le configurer pour utiliser le certificat d’appareil créé dans la section précédente.

Nous attribuons les noms de fichiers suivants par défaut sur la base des exemples de scripts ci-dessus :

| Sortie | Nom de fichier |
| ------ | --------- |
| Certificat d’appareil | `certs/new-edge-device.cert.pem` |
| Clé d’appareil privée | `private/new-edge-device.cert.pem` |
| Chaîne de certificat d’appareil | `certs/new-edge-device-full-chain.cert.pem` |
| Autorité de certification du propriétaire du Hub IoT | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Fournissez les informations sur l’appareil et le certificat pour le runtime IoT Edge. 
 
Dans Linux, à l’aide de la sortie Bash :

   ```bash
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

Dans Windows, à l’aide de la sortie PowerShell :

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Par défaut, les exemples de scripts ne définissent pas de phrase secrète pour la clé d’appareil privée. Si vous définissez une phrase secrète, ajoutez le paramètre suivant : `--device-ca-passphrase {passphrase}`.

Le script vous invite à définir une phrase secrète pour le certificat de l’agent Edge. Redémarrez le runtime IoT Edge après cette commande :

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Configurer un appareil en aval

Un appareil en aval peut être n’importe quelle application utilisant [Azure IoT device SDK][lnk-devicesdk], telle que l’application simple décrite dans [Connecter votre appareil à votre IoT Hub à l’aide de .NET] [ lnk-iothub-getstarted].

Tout d’abord, une application d’appareil en aval doit approuver le certificat **de l’autorité de certification du propriétaire de Hub IoT** afin de valider les connexions TLS aux appareils de la passerelle. Cette étape peut généralement être franchie de deux façons : au niveau du système d’exploitation, ou (pour certaines langues) au niveau de l’application.

Par exemple, pour les applications .NET, vous pouvez ajouter l’extrait de code suivant afin d’approuver un certificat au format PEM stocké dans le chemin d’accès `certPath`. En fonction de la version et du script utilisés, le chemin fait référence à `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) ou `RootCA.pem` (Powershell).

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Cette étape au niveau du système d’exploitation n’est pas la même dans Windows et dans les distributions de Linux.

La seconde étape consiste à initialiser IoT Hub device SDK avec une chaîne de connexion faisant référence au nom d’hôte de l’appareil de passerelle.
Pour ce faire, il faut ajouter la propriété `GatewayHostName` à votre chaîne de connexion d’appareil. Voici un exemple de chaîne de connexion d’un périphérique à laquelle nous avons ajouté la propriété `GatewayHostName` :

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Ces deux étapes permettent à votre application d’appareil de se connecter à l’appareil de passerelle.

## <a name="next-steps"></a>Étapes suivantes
[Comprendre les exigences et outils de développement de modules IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
