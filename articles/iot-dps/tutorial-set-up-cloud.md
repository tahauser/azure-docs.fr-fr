---
title: Configurer le cloud pour le service IoT Hub Device Provisioning dans le portail | Microsoft Docs
description: "Approvisionnement d’appareils automatique IoT Hub dans le portail Azure"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 247c2155943d651c3be7791571522b652cf63483
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Configurer les ressources de cloud pour l’approvisionnement d’appareils avec le service IoT Hub Device Provisioning

Ce didacticiel montre comment configurer le cloud pour l’approvisionnement d’appareils automatique à l’aide du service IoT Hub Device Provisioning. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser le portail Azure pour créer un service IoT Hub Device Provisioning et obtenir l’étendue de l’ID
> * Créer un hub IoT
> * Lier le hub IoT au service Device Provisioning
> * Définir la stratégie d’allocation sur le service Device Provisioning

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Créer une instance de service Device Provisioning et obtenir l’étendue de l’ID

Suivez ces étapes pour créer une instance du service Device Provisioning.

1. En haut à gauche du portail Azure, cliquez sur **Créer une ressource**.
2. Dans la zone de recherche, tapez **device provisioning**. 
3. Cliquez sur **Service IoT Hub Device Provisioning**.
4. Remplissez le formulaire **Service IoT Hub Device Provisioning** avec les informations suivantes :
    
   | Paramètre       | Valeur suggérée | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name** | Tout nom unique | -- | 
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |   

   ![Saisie des informations de base sur votre service DPS dans le portail](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Cliquez sur **Créer**.
6. *L’étendue de l’ID* permet d’identifier les ID d’enregistrement et de garantir leur unicité. Pour obtenir cette valeur, cliquez sur **Vue d’ensemble** pour ouvrir la page **Bases** liée au service Device Provisioning. Copiez la valeur de **l’étendue de l’ID** dans un emplacement temporaire pour une utilisation ultérieure.
7. Notez également la valeur **Point de terminaison de service**, ou copiez-la dans un emplacement temporaire pour une utilisation ultérieure. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre IoT Hub est maintenant créé et vous connaissez le nom d’hôte et la chaîne de connexion à IoT Hub dont vous avez besoin pour terminer ce qu’il reste du didacticiel.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Lier le service Device Provisioning à un hub IoT

L’étape suivante consiste à lier le service Device Provisioning et le hub IoT afin que le service IoT Hub Device Provisioning puisse inscrire des appareils sur ce hub. Le service peut uniquement approvisionner des appareils sur des hubs IoT qui ont été liés au service Device Provisioning. Effectuez les opérations suivantes.

1. Dans la page **Toutes les ressources**, cliquez sur l’instance du service Device Provisioning que vous avez créée.
2. Dans la page Service Device Provisioning, cliquez sur **Hubs IoT liés**.
3. Cliquez sur **Add**.
4. Dans la page **Ajouter un lien au hub IoT**, utilisez les cases d’option pour spécifier si le hub IoT lié se trouve dans l’abonnement actuel ou dans un autre abonnement. Ensuite, choisissez le nom du hub IoT dans la zone **Hub IoT**.
5. Cliquez sur **Enregistrer**.

   ![Lier le nom du hub à lier au service DPS dans le portail](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Définir la stratégie d’allocation sur le service Device Provisioning

La stratégie d’allocation est un paramètre du service IoT Hub Device Provisioning qui détermine la façon dont les appareils sont assignés à un hub IoT. Trois stratégies d’allocation sont prises en charge : 

1. **Latence la plus faible** : les appareils sont configurés pour un hub IoT en fonction du hub ayant la plus faible latence sur l’appareil.
2. **Distribution uniformément pondérée** (par défaut) : les hubs IoT liés sont susceptibles d’avoir des appareils approvisionnés dans des proportions égales. Il s’agit du paramètre par défaut. Si vous approvisionnez des appareils sur un seul hub IoT, vous pouvez conserver ce paramètre. 
3. **Configuration statique par le biais de la liste d’inscriptions** : la spécification du hub IoT souhaité dans la liste d’inscriptions est prioritaire sur la stratégie d’allocation au niveau du service Device Provisioning.

Pour définir la stratégie d’allocation, dans la page Service Device Provisioning, cliquez sur **Gérer la stratégie d’allocation**. Assurez-vous que la stratégie d’allocation est définie sur **Distribution uniformément pondérée** (valeur par défaut). Si vous apportez des modifications, cliquez sur **Enregistrer** quand vous avez terminé.

![Gérer la stratégie d’allocation](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres didacticiels de cette collection reposent sur ce didacticiel. Si vous souhaitez continuer à utiliser d’autres démarrages rapides ou les didacticiels, ne nettoyez pas les ressources créées dans ce didacticiel. Sinon, effectuez les opérations suivantes pour supprimer toutes les ressources créées au cours de ce didacticiel dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre instance du service IoT Hub Device Provisioning. Dans la partie supérieure de la page **Toutes les ressources**, cliquez sur **Supprimer**.  
2. À partir du menu de gauche dans le portail Azure, cliquez sur **Toutes les ressources**, puis sélectionnez votre hub IoT. Dans la partie supérieure de la page **Toutes les ressources**, cliquez sur **Supprimer**.
 
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Utiliser le portail Azure pour créer un service IoT Hub Device Provisioning et obtenir l’étendue de l’ID
> * Créer un hub IoT
> * Lier le hub IoT au service Device Provisioning
> * Définir la stratégie d’allocation sur le service Device Provisioning

Passez au didacticiel suivant pour apprendre à configurer votre appareil pour l’approvisionnement.

> [!div class="nextstepaction"]
> [Configurer l’appareil pour l’approvisionnement](tutorial-set-up-device.md)
