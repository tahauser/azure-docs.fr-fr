---
title: "Guide pratique pour gérer l’accès des appareils pour le service de provisionnement des appareils IoT Hub | Microsoft Docs"
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
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Guide pratique pour révoquer l’accès des appareils à votre service de provisionnement dans le portail Azure

La gestion adéquate des informations d’identification des appareils est essentielle pour les systèmes de grande envergure tels que les solutions IoT. Pour ces systèmes, une bonne pratique consiste à disposer d’un plan indiquant clairement comment révoquer l’accès pour les appareils au cas où leurs informations d’identification, qu’il s’agisse d’un jeton SAP ou d’un certificat X.509, seraient compromises. Cet article décrit comment révoquer l’accès des appareils à l’étape de provisionnement.

Pour en savoir plus sur la révocation de l’accès d’un appareil à un hub IoT une fois l’appareil provisionné, consultez [Désactivation d’appareils](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenez compte de la stratégie de nouvelle tentative applicable aux appareils pour lesquels vous révoquer l’accès. Par exemple, un appareil auquel est associée une stratégie de nouvelle tentative infinie peut essayer de s’inscrire indéfiniment auprès du service de provisionnement, consommant des ressources de service et affectant éventuellement le niveau de performance.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Mettre sur liste rouge les appareils avec une entrée d’inscription individuelle

Les inscriptions individuelles s’appliquent à un appareil unique et peuvent utiliser des certificats x509 ou des jetons SAP (dans un module TPM réel ou virtuel) comme mécanisme d’attestation. (Les appareils qui utilisent des jetons SAP comme mécanisme d’attestation ne peuvent être provisionnés que par le biais d’une inscription individuelle.) Pour mettre sur liste rouge un appareil qui a une inscription individuelle, vous pouvez désactiver ou supprimer son entrée d’inscription : 

- Pour mettre provisoirement l’appareil sur liste rouge, vous pouvez désactiver son entrée d’inscription. 

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Inscriptions individuelles**.
    4. Cliquez sur l’entrée d’inscription de l’appareil à mettre sur liste rouge pour l’ouvrir. 
    5. Cliquez sur **Désactiver** au bas de l’entrée de liste d’inscriptions, puis cliquez sur **Enregistrer**.  

        ![Désactiver une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Pour mettre définitivement l’appareil sur liste rouge, vous pouvez supprimer son entrée d’inscription.

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Inscriptions individuelles**.
    4. Cochez la case en regard de l’entrée d’inscription de l’appareil à mettre sur liste rouge. 
    5. Cliquez sur **Supprimer** en haut de la fenêtre, puis cliquez sur **Oui** pour confirmer que vous voulez supprimer l’inscription. 

        ![Supprimer une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Quand l’opération est terminée, votre entrée a été supprimée de la liste des inscriptions individuelles.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Mettre sur liste rouge un certificat d’autorité de certification racine ou intermédiaire X.509 à l’aide d’un groupe d’inscriptions

Les certificats X.509 sont généralement organisés en une chaîne d’approbation de confiance. Si un certificat à n’importe quel niveau d’une chaîne est compromis, la confiance est rompue ; le certificat doit alors être mis sur liste rouge pour éviter que les appareils situés en aval dans toute chaîne contenant ce certificat soient provisionnés par le service de provisionnement des appareils. Pour en savoir plus sur les certificats X.509 et sur leur utilisation avec le service de provisionnement, consultez [Certificats X.509](./concepts-security.md#x509-certificates). 

Un groupe d’inscriptions est une entrée pour des appareils partageant un mécanisme commun d’attestation de certificats X.509 signés par la même autorité de certification racine ou intermédiaire. L’entrée du groupe d’inscriptions est configurée avec le certificat X.509 associé à l’autorité de certification racine ou intermédiaire, ainsi que toutes les valeurs de configuration, telles que l’état du jumeau et la connexion du hub IoT, qui sont partagées par les appareils dont la chaîne d’approbation contient ce certificat. Pour mettre le certificat sur liste rouge, vous pouvez désactiver ou supprimer son groupe d’inscriptions :

- Pour mettre provisoirement le certificat sur liste rouge, vous pouvez désactiver son groupe d’inscriptions. 

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service de provisionnement pour lequel vous souhaitez mettre le certificat de signature sur liste rouge.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.
    4. Cliquez sur le groupe d’inscriptions du certificat à mettre sur liste rouge pour l’ouvrir.
    5. Cliquez sur **Modifier un groupe** dans le coin supérieur gauche de l’entrée du groupe d’inscriptions.
    6. Pour désactiver l’entrée d’inscription, sélectionnez **Désactiver** sous **Activer l’entrée**, puis cliquez sur **Enregistrer**.  

        ![Désactiver l’entrée du groupe d’inscriptions dans le portail](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Pour mettre définitivement le certificat sur liste rouge, vous pouvez supprimer son groupe d’inscriptions.

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service de provisionnement pour lequel vous souhaitez mettre votre appareil sur liste rouge.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.
    4. Cochez la case en regard du groupe d’inscriptions du certificat à mettre sur liste rouge. 
    5. Cliquez sur **Supprimer** en haut de la fenêtre, puis cliquez sur **Oui** pour confirmer que vous voulez supprimer le groupe d’inscriptions. 

        ![Supprimer l’entrée du groupe d’inscriptions dans le portail](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Quand l’opération est terminée, votre entrée a été supprimée de la liste des groupes d’inscriptions.  

> [!NOTE]
> Si vous supprimez un groupe d’inscriptions pour un certificat, les appareils dont la chaîne d’approbation contient ce certificat peuvent quand même s’inscrire si celle-ci comporte plus en amont un groupe d’inscriptions activé pour le certificat racine ou un autre certificat intermédiaire.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Mettre sur liste rouge des appareils spécifiques dans un groupe d’inscriptions

Les appareils qui implémentent le mécanisme d’attestation X.509 utilisent leur chaîne d’approbation et leur clé privée pour s’authentifier. Quand un appareil se connecte et s’authentifie auprès du service de provisionnement des appareils, celui-ci recherche d’abord une inscription individuelle correspondant aux informations d’identification de l’appareil avant de parcourir les groupes d’inscriptions pour déterminer si l’appareil peut être provisionné. Si le service détecte une inscription individuelle désactivée pour l’appareil, il empêche celui-ci de se connecter, même si la chaîne d’approbation de l’appareil contient un groupe d’inscriptions activé pour une autorité de certification racine ou intermédiaire. Pour mettre sur liste rouge un appareil individuel dans un groupe d’inscriptions, effectuez les étapes suivantes :

1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
2. Dans la liste des ressources, cliquez sur le service de provisionnement qui contient le groupe d’inscriptions de l’appareil à mettre sur liste rouge.
3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Inscriptions individuelles**.
4. Cliquez sur le bouton **Ajouter** en haut. 
5. Sélectionnez **X.509** comme mécanisme de sécurité pour l’appareil et chargez le certificat de l’appareil. Il s’agit du certificat d’entité finale signé installé sur l’appareil, qu’il utilise dans le but de générer des certificats pour l’authentification.
6. Entrez **l’ID d’appareil IoT Hub** de l’appareil. 
7. Pour désactiver l’entrée d’inscription, sélectionnez **Désactiver** sous **Activer l’entrée**. 
8. Cliquez sur **Enregistrer**. Dès que votre groupe inscription est créée, vous voyez votre appareil s’afficher sous l’onglet **Inscriptions individuelles**. 

    ![Désactiver une entrée d’inscription individuelle dans le portail](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




