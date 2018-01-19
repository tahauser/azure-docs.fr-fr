---
title: "Gérer les inscriptions d’appareils avec le portail Azure | Microsoft Docs"
description: "Guide pratique pour gérer les inscriptions d’appareil avec votre service Device Provisioning dans le portail Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 06cc215e5c4087c7a38937de10eaa066037ac444
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Guide pratique pour gérer les inscriptions d’appareils avec le portail Azure

Une *inscription d’appareil* crée un enregistrement d’un appareil ou d’un groupe d’appareils susceptibles d’être inscrits au service Azure IoT Hub Device Provisioning à un moment donné. L’enregistrement contient la configuration initiale souhaitée pour le ou les appareils dans le cadre de cette inscription, y compris le hub IoT souhaité. Cet article explique comment gérer les inscriptions d’appareil dans votre service d’approvisionnement.


## <a name="create-a-device-enrollment"></a>Créer une inscription d’appareil

Il existe deux façons de procéder à l’inscription de vos appareils auprès du service d’approvisionnement :

* Un **groupe d’inscriptions** est une entrée pour un groupe d’appareils partageant un mécanisme commun d’attestation de certificats X.509 signés par le certificat de signature, qui peut être le [certificat racine](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) ou le [certificat intermédiaire](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), utilisé pour produire un certificat d’appareil sur un appareil physique. Nous recommandons l’utilisation d’un groupe d’inscriptions pour un grand nombre d’appareils partageant une configuration initiale souhaitée, ou pour des appareils destinés au même locataire. Notez que vous pouvez uniquement inscrire les appareils qui utilisent le mécanisme d’attestation X.509 comme *groupe d’inscriptions*. 

    Vous pouvez créer un groupe d’inscriptions dans le portail pour un groupe d’appareils en effectuant les étapes suivantes :

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service Device Provisioning auquel vous souhaitez inscrire votre appareil.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Groupes d’inscriptions**.
    4. Cliquez sur le bouton **Ajouter** en haut, puis entrez les informations demandées pour l’entrée de liste d’inscriptions. Chargez le certificat racine pour le groupe d’appareils. 
    5. Cliquez sur **Enregistrer**. Dès que votre groupe d’inscriptions est créé, vous voyez son nom s’afficher sous l’onglet **Groupes d’inscriptions**. 

        ![Groupe d’inscriptions dans le portail](./media/how-to-manage-enrollments/group-enrollment.png)

    
* L’**inscription individuelle** est une entrée permettant d’enregistrer un seul appareil. Les inscriptions individuelles peuvent utiliser des certificats x509 ou des jetons SAP (dans un module de plateforme sécurisée réel ou virtuel) comme mécanismes d’attestation. Nous recommandons d’utiliser les inscriptions individuelles pour les appareils qui nécessitent une configuration initiale unique, ou pour les appareils ne pouvant utiliser que des jetons SAP par le biais du module de plateforme sécurisée (TPM) ou du TPM virtuel comme mécanisme d’attestation. Dans le cas d’inscriptions individuelles, vous pouvez spécifier l’ID de l’appareil IoT Hub souhaité.

    Vous pouvez créer une inscription individuelle dans le portail en suivant les étapes décrites ici :

    1. Connectez-vous au portail Azure et cliquez dans le menu de gauche sur **Toutes les ressources**.
    2. Dans la liste des ressources, cliquez sur le service Device Provisioning auquel vous souhaitez inscrire votre appareil.
    3. Dans votre service d’approvisionnement, cliquez sur **Gérer les inscriptions** et sélectionnez l’onglet **Inscriptions individuelles**.
    4. Cliquez sur le bouton **Ajouter** en haut. 
    5. Sélectionnez le mécanisme de sécurité de l’appareil et entrez les informations demandées pour l’entrée de liste d’inscriptions. Chargez un certificat signé si votre appareil implémente X.509. 
    6. Cliquez sur **Enregistrer**. Dès que votre groupe d’inscriptions est créé, vous voyez votre appareil s’afficher sous l’onglet **Inscriptions individuelles**. 

        ![Inscription individuelle dans le portail](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Mettre à jour une entrée d’inscription
Vous pouvez mettre à jour une entrée d’inscription existante dans le portail en effectuant les étapes suivantes :

1. Ouvrez votre service Device Provisioning dans le portail Azure et cliquez sur **Gérer les inscriptions**. 
2. Accédez à l’entrée d’inscription que vous souhaitez modifier. Cliquez sur l’entrée pour afficher des informations récapitulatives sur votre inscription d’appareil. 
3. Dans cette page, vous pouvez modifier d’autres éléments que le type de sécurité ou les informations d’identification, par exemple le hub IoT auquel l’appareil doit être lié ainsi que l’ID de l’appareil. Vous pouvez également modifier l’état initial du jumeau d’appareil. 
4. Lorsque vous avez terminé, cliquez sur **Enregistrer** pour mettre à jour votre inscription d’appareil. 

    ![Mise à jour d’une inscription dans le portail](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Supprimer une inscription d’appareil
Si votre ou vos appareils n’ont pas besoin d’être provisionnés sur un hub IoT, vous pouvez supprimer l’entrée d’inscription correspondante dans le portail en effectuant les étapes suivantes :

1. Ouvrez votre service Device Provisioning dans le portail Azure et cliquez sur **Gérer les inscriptions**. 
2. Accédez à l’entrée d’inscription que vous souhaitez supprimer et sélectionnez-la. 
3. Cliquez sur le bouton **Supprimer** tout en haut et sélectionnez **Oui** lorsque vous êtes invité à confirmer l’action. 
5. Lorsque l’opération est terminée, votre entrée a été supprimée de la liste des inscriptions d’appareil. 
 
    ![Supprimer une inscription dans le portail](./media/how-to-manage-enrollments/remove-enrollment.png)



