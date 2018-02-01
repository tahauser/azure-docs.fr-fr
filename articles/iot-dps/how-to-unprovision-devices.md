---
title: "Guide pratique pour déprovisionner des appareils inscrits auprès du service Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Guide pratique pour déprovisionner des appareils inscrits par votre service Device Provisioning dans le portail Azure"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 83ed72c0f2eb342c372ef97e5443d60eab1e2174
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Guide pratique pour déprovisionner des appareils inscrits par votre service de provisionnement

Il peut être nécessaire de déprovisionner des appareils qui ont été provisionnés par le biais du service Device Provisioning. Par exemple, un appareil peut avoir été vendu ou déplacé vers un autre hub IoT, ou encore perdu, volé ou compromis. 

En règle générale, le déprovisionnement d’un appareil implique deux étapes :

1. Révoquer l’accès de l’appareil à votre service de provisionnement. Selon que vous souhaitez révoquer l’accès temporairement ou définitivement ou, dans le cas du mécanisme d’attestation X.509, sur la hiérarchie de vos groupes d’inscription existants, vous souhaiterez peut-être désactiver ou supprimer une entrée d’inscription. 
 
   - Pour découvrir comment révoquer l’accès de l’appareil à l’aide du portail, consultez [Révoquer l’accès des appareils](how-to-revoke-device-access-portal.md).
   - Pour découvrir comment révoquer l’accès des appareils par programmation à l’aide de l’un des SDK de service de provisionnement, consultez [Gérer les inscriptions d’appareils avec les SDK du service](how-to-manage-enrollments-sdks.md).

2. Désactiver ou supprimer l’entrée de l’appareil dans le registre des identités pour le hub IoT où il a été provisionné. Pour plus d’informations, consultez [Gérer les identités d’appareils](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) dans la documentation Azure IoT Hub. 

Les étapes exactes nécessaires pour déprovisionner un appareil dépendent du mécanisme d’attestation qu’il utilise.

Les appareils qui utilisent l’attestation du module de plateforme sécurisée (TPM) ou l’attestation X.509 avec un certificat feuille auto-signé (aucune chaîne de certificats) sont provisionnés par le biais d’une entrée d’inscription individuelle. Pour ces appareils, vous pouvez supprimer l’entrée pour révoquer définitivement l’accès de l’appareil au service de provisionnement, ou désactiver l’entrée pour révoquer temporairement son accès puis supprimer ou désactiver l’appareil dans le registre des identités avec le hub IoT pour lequel il était provisionné.

Avec l’attestation X.509, les appareils peuvent également être provisionnés par le biais d’un groupe d’inscription. Les groupes d’inscription sont configurés avec un certificat de signature (certificat intermédiaire ou certificat d’autorité de certification racine) et ils contrôlent l’accès au service de provisionnement pour les appareils dont la chaîne de certificats contient ce certificat. Pour en savoir plus sur les groupes d’inscription et les certificats X.509 avec le service de provisionnement, consultez [Certificats X.509](concepts-security.md#x509-certificates). 

Pour voir la liste des appareils qui ont été provisionnés par le biais d’un groupe d’inscription, vous pouvez afficher les détails du groupe d’inscription. C’est une manière simple de savoir dans quel hub IoT chaque appareil a été provisionné. Pour afficher la liste des appareils 

1. Connectez-vous au portail Azure et cliquez sur **Toutes les ressources** dans le menu de gauche.
2. Cliquez sur votre service de provisionnement dans la liste des ressources.
3. Dans votre service de provisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.
4. Cliquez sur le groupe d’inscription pour l’ouvrir.

   ![Afficher l’entrée du groupe d’inscription dans le portail](./media/how-to-unprovision-devices/view-enrollment-group.png)

Avec les groupes d’inscriptions, il existe deux scénarios à prendre en compte :

- Pour déprovisionner tous les appareils qui ont été provisionnés par le biais d’un groupe d’inscription, vous devez d’abord désactiver le groupe d’inscription pour mettre en liste rouge son certificat de signature. Ensuite, vous pouvez utiliser la liste des appareils provisionnés pour ce groupe d’inscription afin de désactiver ou de supprimer chaque appareil du registre des identités de son hub IoT respectif. Après avoir désactivé ou supprimé tous les appareils de leurs hubs IoT respectifs, vous pouvez éventuellement supprimer le groupe d’inscription. Sachez toutefois que, si vous supprimez le groupe d’inscription et qu’il existe un groupe d’inscription activé pour un certificat de signature plus haut dans la chaîne de certificats de l’un ou plusieurs des appareils, ces appareils peuvent être réinscrits. 
- Pour déprovisionner un seul appareil d’un groupe d’inscription, vous devez d’abord créer une inscription individuelle désactivée pour son certificat feuille (d’appareil). Cela révoque l’accès au service de provisionnement pour cet appareil tout en autorisant l’accès pour d’autres appareils dont la chaîne contient le certificat de signature du groupe d’inscription. Ensuite, vous pouvez utiliser la liste des appareils provisionnés dans les détails du groupe d’inscription pour rechercher le hub IoT pour lequel l’appareil était provisionné, et le désactiver ou le supprimer du registre des identités de ce hub. Ne supprimez pas l’inscription individuelle désactivée pour l’appareil, car cela permettrait à l’appareil d’être réinscrit par le biais du groupe d’inscription. 










