---
title: Concepts d’appareil dans Azure Device Provisioning | Microsoft Docs
description: Décrit les concepts d’approvisionnement d’appareil spécifiques aux appareils avec le service Device Provisioning et IoT Hub
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 5482801461e2afea33d65d559723116f37a35d1f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Concepts d’appareil du service IoT Hub Device Provisioning

Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui vous permet de configurer l’approvisionnement sans contact des appareils dans un hub IoT spécifié. Avec le service Device Provisioning, vous pouvez approvisionner des millions d’appareils de manière sécurisée et scalable.

Cet article donne une vue d’ensemble des concepts d’*appareil* impliqués dans l’approvisionnement d’appareils. Cet article concerne particulièrement les personnes impliquées dans l’[étape de fabrication](about-iot-dps.md#manufacturing-step) qui permet de préparer un appareil pour le déploiement.

## <a name="attestation-mechanism"></a>Mécanisme d’attestation

Le mécanisme d’attestation est la méthode utilisée pour confirmer l’identité d’un appareil. Le mécanisme d’attestation concerne également la liste d’inscriptions, qui indique au service d’approvisionnement la méthode d’attestation à utiliser avec un appareil donné.

> [!NOTE]
> IoT Hub utilise un « schéma d’authentification » pour un concept semblable dans ce service.

Le service Device Provisioning prend en charge deux formes d’attestation :
* **Certificats X.509** basés sur le flux d’authentification de certificat X.509 standard.
* **Module de plateforme sécurisée (TPM)** basé sur un défi nonce, utilisant la norme de module de plateforme sécurisée (TPM) pour les clés afin de présenter un jeton de signature d’accès partagé (SAS) signé. Il n’est pas nécessaire d’avoir un TPM physique sur l’appareil, mais le service utilise pour l’attestation la paire de clés de type EK conformément à la [spécification TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Module de sécurité matériel

Le module de sécurité matériel, ou module HSM, est utilisé pour le stockage matériel sécurisé des secrets de l’appareil. C’est la forme de stockage de secrets la plus sécurisée. Les certificats X.509 et les jetons SAP peuvent être stockés dans le module HSM. Les modules HSM peuvent être utilisés avec les deux mécanismes d’attestation que le service d’approvisionnement prend en charge.

> [!TIP]
> Nous vous recommandons vivement d’utiliser un module HSM avec les appareils pour stocker en toute sécurité des secrets sur vos appareils.

Les secrets de l’appareil peuvent également être stockés sous forme logicielle (mémoire), mais cette forme de stockage est moins sécurisée que le module HSM.

## <a name="registration-id"></a>ID d’enregistrement

L’ID d’enregistrement est utilisé pour identifier de manière unique un appareil dans le service Device Provisioning. L’ID d’appareil doit être unique dans l’[étendue de l’ID](#id-scope) du service d’approvisionnement. Chaque appareil doit avoir un ID d’enregistrement. L’ID d’enregistrement est alphanumérique, en minuscules et peut contenir des traits d’union.

* Dans le cas d’un module TPM, l’ID d’enregistrement est fourni par le TPM lui-même.
* Dans le cas d’une attestation X.509, l’ID d’enregistrement est le nom de sujet du certificat.

## <a name="device-id"></a>ID de périphérique

L’ID d’appareil est l’ID tel qu’il apparaît dans IoT Hub. L’ID d’appareil de votre choix peut être défini dans l’entrée d’inscription, mais ce n’est pas obligatoire. Si aucun ID d’appareil souhaité n’est spécifié dans la liste d’inscriptions, l’ID d’inscription est utilisé comme ID d’appareil durant l’enregistrement de l’appareil. Découvrez plus d’informations sur les [ID d’appareil dans IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Étendue de l’ID

L’étendue de l’ID est affectée à un service Device Provisioning quand celui-ci est créé par l’utilisateur et utilisé pour identifier de façon unique le service d’approvisionnement spécifique qui permet d’enregistrer l’appareil. L’étendue de l’ID est générée par le service et est immuable, ce qui garantit l’unicité.

> [!NOTE]
> L’unicité est importante pour les opérations de déploiement longues et les scénarios de fusion et acquisition.
