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
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Guide pratique pour déprovisionner des appareils inscrits par votre service de provisionnement

Il peut être nécessaire de déprovisionner des appareils qui ont été provisionnés par le biais du service Device Provisioning. Par exemple, un appareil peut avoir été vendu ou déplacé vers un autre hub IoT, ou encore perdu, volé ou compromis. 

En règle générale, le déprovisionnement d’un appareil implique deux étapes :

1. Révoquer l’accès de l’appareil à votre service de provisionnement. Selon que vous souhaitez révoquer l’accès temporairement ou définitivement ou, dans le cas du mécanisme d’attestation X.509, sur la hiérarchie de vos groupes d’inscription existants, vous souhaiterez peut-être désactiver ou supprimer une entrée d’inscription. 
 
   - Pour découvrir comment révoquer l’accès de l’appareil à l’aide du portail, consultez [Révoquer l’accès des appareils](how-to-revoke-device-access-portal.md).
   - Pour découvrir comment révoquer l’accès des appareils par programmation à l’aide de l’un des SDK de service de provisionnement, consultez [Gérer les inscriptions d’appareils avec les SDK du service](how-to-manage-enrollments-sdks.md).

2. Désactiver ou supprimer l’entrée de l’appareil dans le registre des identités pour le hub IoT où il a été provisionné. Pour plus d’informations, consultez [Gérer les identités d’appareils](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) dans la documentation Azure IoT Hub. 

Les étapes précises à suivre pour déprovisionner un appareil dépendent de son mécanisme d’attestation et de son entrée d’inscription au service d’approvisionnement.

## <a name="individual-enrollments"></a>Inscriptions individuelles
Les appareils qui utilisent l’attestation TPM ou l’attestation X.509 avec un certificat feuille sont approvisionnés par le biais d’une entrée d’inscription individuelle. 

Pour déprovisionner un appareil inscrit individuellement : 
1. Pour un appareil qui utilise l’attestation TPM, supprimez l’entrée d’inscription individuelle pour révoquer définitivement l’accès de l’appareil au service d’approvisionnement, ou désactivez-la pour révoquer l’accès de façon temporaire. Dans le cas des appareils qui utilisent l’attestation X.509, vous pouvez soit supprimer, soit désactiver l’entrée. Sachez toutefois que, si vous supprimez l’inscription individuelle d’un appareil qui utilise l’attestation X.509 alors qu’il existe un groupe d’inscription activé pour un certificat de signature de la chaîne de certificats de l’appareil, celui-ci pourra se réinscrire. Il est peut-être plus sûr dans ce cas de désactiver l’entrée d’inscription, ce qui empêche l’appareil de se réinscrire, qu’il existe ou non un groupe d’inscription activé pour l’un de ses certificats de signature.
2. Désactivez ou supprimez l’appareil dans le registre des identités du hub IoT dans lequel il était approvisionné. 


## <a name="enrollment-groups"></a>Groupes d’inscription
Avec l’attestation X.509, les appareils peuvent également être provisionnés par le biais d’un groupe d’inscription. Les groupes d’inscription sont configurés avec un certificat de signature (certificat intermédiaire ou certificat d’autorité de certification racine) et ils contrôlent l’accès au service de provisionnement pour les appareils dont la chaîne de certificats contient ce certificat. Pour en savoir plus sur les groupes d’inscription et les certificats X.509 avec le service de provisionnement, consultez [Certificats X.509](concepts-security.md#x509-certificates). 

Pour voir la liste des appareils qui ont été provisionnés par le biais d’un groupe d’inscription, vous pouvez afficher les détails du groupe d’inscription. C’est une manière simple de savoir dans quel hub IoT chaque appareil a été provisionné. Pour afficher la liste des appareils 

1. Connectez-vous au portail Azure et cliquez sur **Toutes les ressources** dans le menu de gauche.
2. Cliquez sur votre service de provisionnement dans la liste des ressources.
3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.
4. Cliquez sur le groupe d’inscription pour l’ouvrir.

   ![Afficher l’entrée du groupe d’inscription dans le portail](./media/how-to-unprovision-devices/view-enrollment-group.png)

Avec les groupes d’inscriptions, il existe deux scénarios à prendre en compte :

- Pour déprovisionner tous les appareils qui ont été approvisionnés par le biais d’un groupe d’inscription :
  1. Désactivez le groupe d’inscription pour mettre son certificat de signature sur liste noire. 
  2. Utilisez la liste des appareils approvisionnés pour ce groupe d’inscription afin de désactiver ou de supprimer de son hub IoT chacun des appareils du registre des identités. 
  3. Après avoir désactivé ou supprimé tous les appareils de leurs hubs IoT respectifs, vous pouvez éventuellement supprimer le groupe d’inscription. Sachez toutefois que, si vous supprimez le groupe d’inscription et qu’il existe un groupe d’inscription activé pour un certificat de signature plus haut dans la chaîne de certificats de l’un ou plusieurs des appareils, ces appareils peuvent être réinscrits. 
- Pour déprovisionner un seul appareil d’un groupe d’inscription :
  1. Créez une inscription individuelle désactivée pour son certificat feuille (appareil). Cela révoque l’accès au service de provisionnement pour cet appareil tout en autorisant l’accès pour d’autres appareils dont la chaîne contient le certificat de signature du groupe d’inscription. Ne supprimez pas l’inscription individuelle désactivée pour l’appareil, car cela permettrait à l’appareil d’être réinscrit par le biais du groupe d’inscription. 
  2. Utilisez la liste des appareils approvisionnés de ce groupe d’inscription pour trouver le hub IoT dans lequel l’appareil était approvisionné, et désactivez-le ou supprimez-le du registre des identités de ce hub. 
  
  










