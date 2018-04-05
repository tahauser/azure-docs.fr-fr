---
title: Service Device Provisioning IoT Hub - Concepts de provisionnement automatique
description: Cet article fournit une vue d’ensemble conceptuelle des phases de provisionnement automatique d’appareils à l’aide du service Device Provisioning IoT, d’IoT Hub et des kits SDK clients.
services: iot-dps
keywords: ''
author: BryanLa
ms.author: bryanla
ms.date: 03/27/2018
ms.topic: conceptual
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: cd458b1f6d26fbd5f5821a04cd01be5c3a4e4514
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="auto-provisioning-concepts"></a>Concepts de provisionnement automatique

Comme décrit dans la [Vue d’ensemble](about-iot-dps.md), le service Device Provisioning est un service d’assistance qui autorise le provisionnement juste-à-temps d’appareils dans un hub IoT sans nécessiter d’intervention humaine. Une fois le provisionnement réussi, les appareils se connectent directement avec leur hub IoT désigné. Ce processus porte le nom de provisionnement automatique et offre une expérience de configuration initiale et d’inscription prête à l’emploi pour les appareils.

## <a name="overview"></a>Vue d'ensemble

Le provisionnement automatique Azure IoT peut être divisé en trois phases :

1. **Configuration du service** : configuration unique des instances d’Azure IoT Hub et du service Device Provisioning IoT Hub, qui les établit et crée une liaison entre eux.

   > [!NOTE]
   > Quelle que soit la taille de votre solution IoT, même si vous envisagez de prendre en charge des millions d’appareils, il s’agit d’une **configuration unique**.

2. **Inscription des appareils** : processus permettant à l’instance du service Device Provisioning d’avoir connaissance des appareils qui tenteront de s’inscrire ultérieurement. L’inscription s’effectue en configurant les informations d’identités d’appareils dans le service de provisionnement, soit comme inscription « individuelle » pour un seul appareil, soit comme « inscription de groupe » pour plusieurs appareils. L’identité est basée sur le [mécanisme d’attestation](concepts-security.md#attestation-mechanism) que l’appareil est censé utiliser, ce qui permet au service de provisionnement d’attester de l’authenticité de l’appareil pendant l’inscription :

   - **Module de plateforme sécurisée (TPM)** : configurée comme « inscription individuelle », l’identité de l’appareil est basée sur l’ID d’inscription du module TPM et la paire de clés de type EK (Endorsement Key) publique. TPM étant une [spécification]((https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)), le service s’attend uniquement à attester conformément à la spécification, quelle que soit l’implémentation de TPM (matérielle ou logicielle). Pour plus d’informations sur l’attestation basée sur TPM, consultez [Device provisioning: Identity attestation with TPM](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/). 

   - **X509** : configurée comme « inscription individuelle » ou « inscription de groupe », l’identité de l’appareil est basée sur un certificat numérique X.509, qui est chargé sur l’inscription sous forme de fichier .pem ou .cer.

   > [!IMPORTANT]  
   > Bien que non prérequis pour utiliser le service Device Provisioning, il est vivement recommandé que votre appareil utilise un module de sécurité matériel (HSM) pour stocker les informations d’identités d’appareils sensibles, telles que les clés et les certificats X.509.

3. **Inscription et configuration des appareils** : lancées au démarrage par le logiciel d’inscription, qui est généré à l’aide d’un kit SDK client de service Device Provisioning adapté à l’appareil et au mécanisme d’attestation. Le logiciel établit une connexion au service de provisionnement pour l’authentification de l’appareil et l’inscription ultérieure dans le hub IoT. Une fois l’inscription effectuée, l’appareil reçoit son ID d’appareil unique IoT Hub et ses informations et de connexion, ce qui lui permet d’extraire sa configuration initiale et de commencer le processus de télémétrie. Dans les environnements de production, cette phase peut se produire plusieurs semaines ou mois après les deux phases précédentes.

## <a name="roles-and-operations"></a>Rôles et opérations

Les phases décrites dans la section précédente peuvent s’étendre sur plusieurs semaines ou mois en raison de facteurs tels que les délais de fabrication, l’expédition, les procédures douanières et ainsi de suite. De plus, elles peuvent englober les activités de plusieurs rôles, étant donné les diverses entités impliquées. Cette section examine plus en détail les différents rôles et opérations liés à chaque phase, puis montre le flux dans un diagramme de séquence. 

Le provisionnement automatique impose également des conditions au fabricant de l’appareil, qui sont propres à l’activation du mécanisme d’attestation. Les opérations de fabrication peuvent également se produire indépendamment du planning des phases de provisionnement automatique, en particulier dans les cas où de nouveaux appareils sont fournis une fois que le provisionnement automatique a déjà été établi.

Dans la table des matières à gauche, vous trouverez une série de guides de démarrage rapide qui expliquent le provisionnement automatique par le biais d’une expérience pratique. Afin de simplifier le processus d’apprentissage, un logiciel est utilisé pour simuler un appareil physique pour l’inscription et l’enregistrement. Certains guides de démarrage rapide vous obligent à effectuer des opérations pour plusieurs rôles, notamment des opérations pour des rôles inexistants, en raison de la nature simulée de ces guides.

| Rôle | Opération | Description | 
|------| --------- | ------------| 
| Fabricant | Encoder l’URL d’inscription et l’identité | En fonction du mécanisme d’attestation utilisé, le fabricant est responsable de l’encodage des informations d’identités d’appareils et de l’URL d’inscription auprès du service Device Provisioning.<br><br>**Guides de démarrage rapide** : l’appareil étant simulé, il n’existe aucun rôle Fabricant. Pour plus d’informations sur la façon d’obtenir ces informations, qui sont utilisées pour le codage d’un exemple d’application d’inscription, consultez le rôle Développeur. |  
| | Fournir l’identité d’appareil | Étant à l’origine des informations d’identités d’appareils, le fabricant est chargé de les transmettre à l’opérateur (ou à un agent désigné), ou de les inscrire directement auprès du service Device Provisioning par le biais des API.<br><br>**Guides de démarrage rapide** : l’appareil étant simulé, il n’existe aucun rôle Fabricant. Pour plus d’informations sur la façon d’obtenir l’identité de l’appareil, qui est utilisée pour inscrire un appareil simulé dans votre instance du service Device Provisioning, consultez le rôle Opérateur. | 
| Opérateur | Configurer le provisionnement automatique | Cette opération correspond à la première phase de provisionnement automatique.<br><br>**Guides de démarrage rapide** : vous endossez le rôle d’opérateur et vous configurez le service Device Provisioning et les instances IoT Hub dans votre abonnement Azure. | Configurer le provisionnement automatique |
|  | Inscrire l’identité de l’appareil | Cette opération correspond à la deuxième phase de provisionnement automatique.<br><br>**Guides de démarrage rapide** : vous endossez le rôle d’opérateur et vous inscrivez votre appareil simulé auprès de votre instance du service Device Provisioning. L’identité de l’appareil est déterminée par la méthode d’attestation simulée dans le guide de démarrage rapide (TPM ou X.509). Pour plus d’informations sur l’attestation, consultez le rôle Développeur. | 
| Service Device Provisioning,<br>IoT Hub | \<toutes les opérations\> | Pour une implémentation en production avec des appareils physiques et pour les guides de démarrage rapide avec des appareils simulés, ces rôles sont endossés par le biais des services IoT que vous configurez dans votre abonnement Azure. Les rôles/opérations fonctionnent exactement de la même manière, car les services IoT sont indifférents au fait qu’il s’agisse d’un provisionnement d’appareils physiques ou simulés. | 
| Développeur | Générer/déployer le logiciel d’inscription | Cette opération correspond à la troisième phase de provisionnement automatique. Le développeur est chargé de générer et de déployer le logiciel d’inscription sur l’appareil à l’aide du kit SDK approprié.<br><br>**Guides de démarrage rapide** : l’exemple d’application d’inscription que vous générez simule un appareil réel, pour la plateforme/le langage de votre choix, qui s’exécute sur votre station de travail (au lieu de le déployer sur un appareil physique). L’application d’inscription effectue les mêmes opérations qu’en cas de déploiement sur un appareil physique. Vous spécifiez la méthode d’attestation (certificat TPM ou X.509), ainsi que l’URL d’inscription et « l’étendue de l’ID » de votre instance du service Device Provisioning. L’identité de l’appareil est déterminée par la logique d’attestation du SDK au moment de l’exécution, en fonction de la méthode que vous spécifiez : <ul><li>**Attestation TPM** : votre station de travail de développement exécute une [application de simulation de module de plateforme sécurisée](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Une fois celle-ci en cours d’exécution, une application distincte est utilisée pour extraire la « paire de clés de type EK (Endorsement Key) » et l’« ID d’inscription » du module TPM en vue de les utiliser pour l’inscription de l’identité de l’appareil. La logique d’attestation du SDK utilise également le simulateur pendant l’inscription, afin de présenter un jeton SAP signé pour l’authentification et la vérification de l’inscription.</li><li>**Attestation X509** : vous utilisez un outil pour [générer un certificat](how-to-use-sdk-tools.md#x509-certificate-generator). Une fois le certificat généré, vous créez le fichier de certificat qui doit être utilisé lors de l’inscription. La logique d’attestation du SDK utilise également le certificat pendant l’inscription, afin de le présenter pour l’authentification et la vérification de l’inscription.</li></ul> | 
| Appareil | Démarrer et inscrire | Cette opération correspond à la troisième phase de provisionnement automatique, assurée par le logiciel d’inscription d’appareil généré par le développeur. Pour plus d’informations, consultez le rôle Développeur. Lors du premier démarrage : <ol><li>L’application se connecte avec l’instance du service Device Provisioning, conformément à l’URL globale et à « l’étendue de l’ID » du service spécifiés pendant le développement.</li><li>Une fois connecté, l’appareil est authentifié par rapport à la méthode d’attestation et à l’identité spécifiées lors de l’inscription.</li><li>Une fois authentifié, l’appareil est inscrit auprès de l’instance IoT Hub spécifiée par l’instance du service de provisionnement.</li><li>Une fois l’inscription réussie, un ID d’appareil et un point de terminaison IoT Hub uniques sont retournés à l’application d’inscription pour communiquer avec IoT Hub.</li><li> À partir de là, l’appareil peut extraire son état de [jumeau d’appareil](~/articles/iot-hub/iot-hub-devguide-device-twins.md) initial pour la configuration, et lancer le processus de création de rapports des données de télémétrie.</li></ol>**Guides de démarrage rapide** : l’appareil étant simulé, le logiciel d’inscription s’exécute sur votre station de travail de développement.| 

Le diagramme suivant récapitule les rôles et l’ordre des opérations lors du provisionnement automatique d’un appareil :
<br><br>
![Séquence de provisionnement automatique d’un appareil](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png) 

> [!NOTE]
> Éventuellement, le fabricant peut aussi effectuer l’opération « Inscrire l’identité de l’appareil » à l’aide des API du service Device Provisioning (plutôt que par le biais de l’Opérateur). Pour une discussion détaillée de cette mise en séquence et des informations supplémentaires, consultez la [vidéo sur l’inscription d’appareils sans intervention avec Azure IoT](https://myignite.microsoft.com/sessions/55087) (à partir du marqueur 41:00).

## <a name="next-steps"></a>Étapes suivantes

Nous vous conseillons de mettre cet article dans vos Favoris afin de pouvoir le consulter facilement à mesure que vous parcourez les guides de démarrage rapide de provisionnement automatique correspondants. 

Commencez par suivre le guide de démarrage rapide « Configuration du provisionnement automatique » qui convient le mieux à vos préférences d’outil de gestion. Il vous guidera tout au long de la phase de « Configuration du service » :

- [Configurer le provisionnement automatique avec Azure CLI](quick-setup-auto-provision-cli.md)
- [Configurer le provisionnement automatique à l’aide du portail Azure](quick-setup-auto-provision.md)
- [Configurer le provisionnement automatique à l’aide d’un modèle Resource Manager](quick-setup-auto-provision-rm.md)

Ensuite, continuez avec le guide de démarrage rapide « Provisionner automatiquement un appareil simulé » qui convient le mieux à votre mécanisme d’attestation d’appareil et votre préférence en matière de langage/SDK de service Device Provisioning. Ce guide de démarrage rapide concerne les phases « Inscription d’appareils » et « Configuration et inscription d’appareils » : 

|  | Mécanisme d’attestation d’appareil simulé | SDK/langage de guide de démarrage rapide |  |
|--|--|--|--|
|  | Module de plateforme sécurisée (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | Certificat X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.JS](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




