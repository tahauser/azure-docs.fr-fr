---
title: Concepts de sécurité du service Azure IoT Hub Device Provisioning | Microsoft Docs
description: Décrit les concepts d’approvisionnement de sécurité pour les appareils avec le service Device Provisioning et IoT Hub
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/27/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 5e35a802349bd85b50a13a3d9a7e0c78945937bd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Concepts de sécurité du service IoT Hub Device Provisioning 

Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui vous permet de configurer l’approvisionnement sans contact des appareils dans un hub IoT spécifié. Avec le service Device Provisioning, vous pouvez approvisionner des millions d’appareils de manière sécurisée et scalable. Cet article donne une vue d’ensemble des concepts de *sécurité* impliqués dans l’approvisionnement d’appareils. Cet article concerne toutes les personnes impliquées dans la préparation d’un appareil pour le déploiement.

## <a name="attestation-mechanism"></a>Mécanisme d’attestation

Le mécanisme d’attestation est la méthode utilisée pour confirmer l’identité d’un appareil. Le mécanisme d’attestation concerne également la liste d’inscriptions, qui indique au service d’approvisionnement la méthode d’attestation à utiliser avec un appareil donné.

> [!NOTE]
> IoT Hub utilise un « schéma d’authentification » pour un concept semblable dans ce service.

Le service Device Provisioning prend en charge deux formes d’attestation :
* **Certificats X.509** basés sur le flux d’authentification de certificat X.509 standard.
* **Module de plateforme sécurisée (TPM)** basé sur un défi nonce, utilisant la norme TPM pour les clés afin de présenter un jeton de signature d'accès partagé (SAS) signé. Il n’est pas nécessaire d’avoir un TPM physique sur l’appareil, mais le service utilise pour l’attestation la paire de clés de type EK conformément à la [spécification TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Module de sécurité matériel

Le module de sécurité matériel, ou module HSM, est utilisé pour le stockage matériel sécurisé des secrets de l’appareil. C’est la forme de stockage de secrets la plus sécurisée. Les certificats X.509 et les jetons SAP peuvent être stockés dans le module HSM. Les modules HSM peuvent être utilisés avec les deux mécanismes d’attestation que l’approvisionnement prend en charge.

> [!TIP]
> Nous vous recommandons vivement d’utiliser un module HSM avec les appareils pour stocker en toute sécurité des secrets sur vos appareils.

Les secrets de l’appareil peuvent également être stockés sous forme logicielle (mémoire), mais cette forme de stockage est moins sécurisée que le module HSM.

## <a name="trusted-platform-module"></a>Module de plateforme sécurisée (TPM)

Un module TPM peut faire référence à un standard de stockage sécurisé pour les clés utilisées dans l’authentification de la plateforme ou à l’interface d’E/S utilisée pour interagir avec les modules qui implémentent le standard. Les modules TPM peuvent exister sous forme de matériel distinct, de matériel intégré, basés sur un microprogramme ou basés sur un logiciel. Découvrez plus d’informations sur [Modules TPM et attestation TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Le service Device Provisioning prend en charge uniquement TPM 2.0.

### <a name="endorsement-key"></a>Paire de clés de type EK (Endorsement Key)

La paire de clés de type EK (Endorsement Key) est une clé asymétrique contenue dans un module TPM qui a été générée en interne ou injectée au moment de la fabrication et est unique pour chaque module TPM. La paire de clés de type EK ne peut pas être changée ou supprimée. La partie privée de la paire de clés de type EK n’est jamais publiée en dehors du module TPM, tandis que sa partie publique est utilisée pour reconnaître un module TPM authentique. Découvrez plus d’informations sur la [paire de clés de type EK (Endorsement Key)](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Clé racine de stockage

La clé racine de stockage est stockée dans le module TPM et est utilisée pour protéger les clés TPM créées par des applications, afin que ces clés ne soient pas utilisables sans le module TPM. La clé racine de stockage est générée quand vous prenez possession du module TPM. Quand vous effacez le module TPM pour transférer la propriété a un autre utilisateur, une nouvelle clé racine de stockage est générée. Découvrez plus d’informations sur la [clé racine de stockage](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificats X.509

Utiliser des certificats X.509 comme mécanisme d’attestation est un excellent moyen de mettre à l’échelle la production et de simplifier le provisionnement des appareils. Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance dans laquelle chaque certificat est signé par la clé privée du certificat plus élevé suivant, et ainsi de suite, pour aboutir à un certificat racine auto-signé. Il en résulte une chaîne déléguée de confiance allant du certificat racine généré par une autorité de certification (CA) racine approuvée jusqu’au certificat d’entité finale installé sur un appareil, en passant par chaque autorité de certification intermédiaire. Pour plus d’informations, consultez [Authentification des appareils à l’aide de certificats d’autorité de certification X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

La chaîne d’approbation représente souvent une hiérarchie logique ou physique associée aux appareils. Par exemple, un fabricant peut émettre un certificat d’autorité de certification racine auto-signé, utiliser ce certificat pour générer un certificat d’autorité de certification intermédiaire unique pour chaque usine, utiliser le certificat de chaque usine pour générer un certificat d’autorité de certification intermédiaire unique pour chaque ligne de production dans l’usine et enfin utiliser le certificat de la ligne de production pour générer un certificat d’appareil unique (entité finale) pour chaque appareil fabriqué sur la ligne. Pour plus d’informations, consultez [Informations conceptuelles sur les certificats de l’autorité de certification X.509 dans l’industrie IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificat racine

Un certificat racine est un certificat X.509 auto-signé représentant une autorité de certification (CA). Il constitue la dernière étape, ou ancre d’approbation, de la chaîne d’approbation. Les certificats racine peuvent être émis automatiquement par une organisation ou achetés à partir d’une autorité de certification racine. Pour plus d’informations, consultez [Obtenir des certificats d’autorité de certification X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Le certificat racine peut également être appelé « certificat d’autorité de certification racine ».

### <a name="intermediate-certificate"></a>Certificat intermédiaire

Un certificat intermédiaire est un certificat X.509 qui a été signé par le certificat racine (ou par un autre certificat intermédiaire contenant le certificat racine dans sa chaîne). Le dernier certificat intermédiaire dans une chaîne est utilisé pour signer le certificat feuille. Un certificat intermédiaire peut également être appelé « certificat d’autorité de certification intermédiaire ».

### <a name="leaf-certificate"></a>Certificat feuille

Le certificat feuille, ou certificat d’entité finale, identifie le détenteur du certificat. Il possède le certificat racine dans sa chaîne d’approbation, ainsi que zéro ou plusieurs certificats intermédiaires. Le certificat feuille n’est pas utilisé pour signer d’autres certificats. Il identifie de façon unique l’appareil auprès du service de provisionnement et est parfois appelé « certificat d’appareil ». Pendant l’authentification, l’appareil utilise la clé privée associée à ce certificat pour répondre à une preuve de vérification de possession émise par le service. Pour plus d’informations, consultez [Authentification d’appareils signés avec des certificats d’autorité de certification X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Contrôle de l’accès des appareils au service de provisionnement avec des certificats X.509

Le service de provisionnement expose deux types d’entrée d’inscription que vous pouvez utiliser pour contrôler l’accès des appareils qui recourent au mécanisme d’attestation X.509 :  

- Les entrées [d’inscription individuelle](./concepts-service.md#individual-enrollment) sont configurées avec le certificat d’appareil associé à un appareil spécifique. Ces entrées contrôlent l’inscription d’appareils spécifiques.
- Les entrées de [groupe d’inscriptions](./concepts-service.md#enrollment-group) sont associées à un certificat d’autorité de certification intermédiaire ou racine spécifique. Ces entrées contrôlent les inscriptions de tous les appareils dont la chaîne de chaîne d'approbation contient ce certificat racine ou intermédiaire. 

Quand un appareil se connecte au service de provisionnement, le service privilégie les entrées d’inscription plus spécifiques par rapport aux entrées d’inscription moins spécifiques. Autrement dit, si une inscription individuelle pour l’appareil existe, le service de provisionnement applique cette entrée. S’il n’existe aucune inscription individuelle pour l’appareil et qu’il existe un groupe d’inscriptions pour le premier certificat intermédiaire dans la chaîne d’approbation de l’appareil, le service applique cette entrée et ainsi de suite le long de la chaîne jusqu’à la racine. Le service applique la première entrée applicable qu’il trouve, comme suit :

- Si la première entrée d’inscription trouvée est activée, le service provisionne l’appareil.
- Si la première entrée d’inscription trouvée est désactivée, le service ne provisionne pas l’appareil.  
- Si aucune entrée d’inscription n’est trouvée pour aucun des certificats dans la chaîne d’approbation de l’appareil, le service ne provisionne pas l’appareil. 

Ce mécanisme et la structure hiérarchique des chaînes d’approbation vous permettent de contrôler en toute souplesse l’accès des appareils individuels, ainsi que des groupes d’appareils. Par exemple, imaginez cinq appareils avec les chaînes d’approbation suivantes : 

- *Appareil 1* : certificat racine -> certificat A -> certificat de l’appareil 1
- *Appareil 2* : certificat racine -> certificat A -> certificat de l’appareil 2
- *Appareil 3* : certificat racine -> certificat A -> certificat de l’appareil 3
- *Appareil 4* : certificat racine -> certificat B -> certificat de l’appareil 4
- *Appareil 5* : certificat racine -> certificat B -> certificat de l’appareil 5

Au départ, vous pouvez créer une entrée de groupe d’inscriptions activée unique pour le certificat racine afin de permettre l’accès pour les cinq appareils. Si, par la suite, le certificat B est compromis, vous pouvez créer une entrée de groupe d’inscriptions désactivée pour le certificat B afin d’empêcher l’inscription de *l’appareil 4* et de *l’appareil 5*. Si, un peu plus tard, *l’appareil 3* est compromis, vous pouvez créer une entrée d’inscription individuelle désactivée pour son certificat. Cette opération révoque l’accès de *l’appareil 3*, mais permet quand même à *l’appareil 1* et à *l’appareil 2* de s’inscrire.