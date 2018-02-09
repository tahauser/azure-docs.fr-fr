---
title: "Gérer l’accès des appareils pour Azure IoT Hub Device Provisioning Service | Microsoft Docs"
description: "Comment révoquer l’accès des appareils à votre service DPS dans le portail Azure"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 12aebf3a56aa7469a765ab6fc67aa65b254db71a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Révoquer l’accès des appareils à votre service de provisionnement dans le portail Azure

La gestion adéquate des informations d’identification des appareils est essentielle pour les systèmes de grande envergure tels que les solutions IoT. Pour ces systèmes, une bonne pratique consiste à disposer d’un plan indiquant clairement comment révoquer l’accès pour les appareils quand leurs informations d’identification, qu’il s’agisse d’un jeton de signature d’accès partagé (SAP) ou d’un certificat X.509, peuvent être compromises. Cet article décrit comment révoquer l’accès des appareils à l’étape de provisionnement.

Pour en savoir plus sur la révocation de l’accès d’un appareil à un hub IoT une fois l’appareil provisionné, consultez [Désactivation d’appareils](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenez compte de la stratégie de nouvelle tentative applicable aux appareils pour lesquels vous révoquer l’accès. Par exemple, un appareil qui utilise une stratégie de nouvelles tentatives infinies peut tenter de s’inscrire de manière continue auprès du service de provisionnement. Cette situation consomme des ressources de service et peut affecter les performances.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Mettre sur liste rouge les appareils à l’aide d’une entrée d’inscription individuelle

Les inscriptions individuelles s’appliquent à un appareil unique et peuvent utiliser des certificats X.509 ou des jetons SAP (dans un module TPM réel ou virtuel) comme mécanisme d’attestation. (Les appareils qui utilisent des jetons SAP comme mécanisme d’attestation ne peuvent être provisionnés que par le biais d’une inscription individuelle.) Pour mettre sur liste rouge un appareil qui a une inscription individuelle, vous pouvez désactiver ou supprimer son entrée d’inscription. 

Pour mettre provisoirement l’appareil sur liste rouge en désactivant son entrée d’inscription 

1. Connectez-vous au portail Azure et sélectionnez **Toutes les ressources** dans le menu de gauche.
2. Dans la liste des ressources, sélectionnez le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
3. Dans votre service de provisionnement, sélectionnez **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**.
4. Sélectionnez l’entrée d’inscription de l’appareil à mettre sur liste rouge. 
5. Sélectionnez **Désactiver** sur le commutateur **Activer l’entrée**, puis sélectionnez **Enregistrer**.  

   ![Désactiver une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
Pour mettre définitivement l’appareil sur liste rouge en supprimant son entrée d’inscription

1. Connectez-vous au portail Azure et sélectionnez **Toutes les ressources** dans le menu de gauche.
2. Dans la liste des ressources, sélectionnez le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
3. Dans votre service de provisionnement, sélectionnez **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**.
4. Cochez la case en regard de l’entrée d’inscription de l’appareil à mettre sur liste rouge. 
5. Sélectionnez **Supprimer** en haut de la fenêtre, puis sélectionnez **Oui** pour confirmer que vous voulez supprimer l’inscription. 

   ![Supprimer une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Une fois la procédure terminée, vous devriez constater que votre entrée a été supprimée de la liste des inscriptions individuelles.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Mettre sur liste rouge un certificat d’autorité de certification racine ou intermédiaire X.509 à l’aide d’un groupe d’inscription

Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. Si un certificat à un endroit quelconque d’une chaîne est compromis, l’approbation est rompue. Le certificat doit être mis sur liste rouge pour empêcher que le service Device Provisioning ne provisionne des appareils en aval dans la chaîne qui contient ce certificat. Pour en savoir plus sur les certificats X.509 et sur leur utilisation avec le service de provisionnement, consultez [Certificats X.509](./concepts-security.md#x509-certificates). 

Un groupe d’inscription est une entrée pour des appareils partageant un mécanisme commun d’attestation de certificats X.509 signés par la même autorité de certification racine ou intermédiaire. L’entrée du groupe d’inscription est configurée avec le certificat X.509 associé à l’autorité de certification racine ou intermédiaire. L’entrée est également configurée avec toutes les valeurs de configuration, telles que l’état du jumeau et la connexion du hub IoT, qui sont partagées par les appareils dont la chaîne de certificats contient ce certificat. Pour mettre le certificat sur liste rouge, vous pouvez désactiver ou supprimer son groupe d’inscription.

Pour mettre provisoirement le certificat sur liste rouge en désactivant son groupe d’inscription 

1. Connectez-vous au portail Azure et sélectionnez **Toutes les ressources** dans le menu de gauche.
2. Dans la liste des ressources, sélectionnez le service de provisionnement pour lequel vous souhaitez mettre le certificat de signature sur liste rouge.
3. Dans votre service de provisionnement, sélectionnez **Gérer les inscriptions**, puis sélectionnez l’onglet **Groupes d’inscription**.
4. Sélectionnez le groupe d’inscription du certificat à mettre sur liste rouge.
5. Dans l’entrée du groupe d’inscription, sélectionnez **Modifier le groupe**.
6. Sélectionnez **Désactiver** sur le commutateur **Activer l’entrée**, puis sélectionnez **Enregistrer**.  

   ![Désactiver l’entrée du groupe d’inscription dans le portail](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Pour mettre définitivement le certificat sur liste rouge en supprimant son groupe d’inscription

1. Connectez-vous au portail Azure et sélectionnez **Toutes les ressources** dans le menu de gauche.
2. Dans la liste des ressources, sélectionnez le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
3. Dans votre service de provisionnement, sélectionnez **Gérer les inscriptions**, puis sélectionnez l’onglet **Groupes d’inscription**.
4. Cochez la case en regard du groupe d’inscription du certificat à mettre sur liste rouge. 
5. Sélectionnez **Supprimer** en haut de la fenêtre, puis sélectionnez **Oui** pour confirmer que vous voulez supprimer le groupe d’inscription. 

   ![Supprimer l’entrée du groupe d’inscription dans le portail](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Une fois la procédure terminée, vous devriez constater que votre entrée a été supprimée de la liste des groupes d’inscription.  

> [!NOTE]
> Si vous supprimez un groupe d’inscription pour un certificat, les appareils dont la chaîne de certificats contient ce certificat peuvent quand même s’inscrire si celle-ci comporte plus en amont un groupe d’inscription activé pour le certificat racine ou un autre certificat intermédiaire.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Mettre sur liste rouge des appareils spécifiques dans un groupe d’inscription

Les appareils qui implémentent le mécanisme d’attestation X.509 utilisent leur chaîne d’approbation et leur clé privée pour s’authentifier. Quand un appareil se connecte et s’authentifie auprès du service Device Provisioning, le service recherche d’abord une inscription individuelle qui correspond aux informations d’identification de l’appareil. Le service recherche ensuite dans les groupes d’inscription pour déterminer si l’appareil peut être provisionné. Si le service trouve une inscription individuelle désactivée pour l’appareil, il empêche l’appareil de se connecter. Le service empêche la connexion même s’il existe un groupe d’inscription activé pour une autorité de certification racine ou intermédiaire dans la chaîne de certificats de l’appareil. 

Pour mettre sur liste rouge un appareil individuel dans un groupe d’inscription, effectuez les étapes suivantes :

1. Connectez-vous au portail Azure et sélectionnez **Toutes les ressources** dans le menu de gauche.
2. Dans la liste des ressources, sélectionnez le service de provisionnement qui contient le groupe d’inscription de l’appareil à mettre sur liste rouge.
3. Dans votre service de provisionnement, sélectionnez **Gérer les inscriptions**, puis sélectionnez l’onglet **Inscriptions individuelles**.
4. Sélectionnez le bouton **Ajouter** en haut. 
5. Sélectionnez **X.509** comme mécanisme de sécurité pour l’appareil et chargez le certificat de l’appareil. Il s’agit du certificat d’entité finale signé installé sur l’appareil. L’appareil l’utilise pour générer des certificats pour l’authentification.
6. Dans **ID de l’appareil IoT Hub** entrez l’ID de l’appareil. 
7. Sélectionnez **Désactiver** sur le commutateur **Activer l’entrée**, puis sélectionnez **Enregistrer**. 

   ![Désactiver une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Une fois votre inscription créée, votre appareil doit apparaître sous l’onglet **Inscriptions individuelles**.


