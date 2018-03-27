---
title: Dans ce didacticiel, vous allez apprendre à vous abonner à une offre Azure Stack | Microsoft Docs
description: Ce didacticiel explique comment créer un nouvel abonnement dans les services Azure Stack et comment tester l’offre en créant une machine virtuelle de test.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>Didacticiel : créer et tester un abonnement
Ce didacticiel vous montre comment créer un abonnement qui contient une offre et comment la tester. Pour le test, vous allez vous connecter au [portail utilisateur](https://portal.local.azurestack.external) en tant qu’administrateur cloud, vous abonner à l’offre, puis créer une machine virtuelle.

> [!TIP]
> Pour une expérience d’évaluation d’expérience avancée, vous pouvez [créer un abonnement pour un utilisateur particulier](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) puis vous connecter en tant que cet utilisateur au portail utilisateur. 

Ce didacticiel vous montre comment vous abonner à l’offre créée dans le [didacticiel précédent](asdk-offer-services.md).

Contenu :

> [!div class="checklist"]
> * S’abonner à une offre 
> * Tester l’offre

## <a name="subscribe-to-an-offer"></a>S’abonner à une offre
Pour vous abonner à une offre en tant qu’utilisateur, vous devez vous connecter au portail utilisateur Azure Stack pour découvrir les services offerts par l’opérateur Azure Stack.

1. Connectez-vous au [portail utilisateur](https://portal.local.azurestack.external) et cliquez sur **Obtenir un abonnement**.

   ![Prendre un abonnement](media/asdk-subscribe-services/get-subscription.png)

2. Dans le champ **Nom d’affichage** , tapez un nom pour votre abonnement. Ensuite, cliquez sur **Offre** pour sélectionner l’une des offres disponibles dans la section **Choisir une offre**, puis cliquez sur **Créer**.

   ![Créer une offre](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > Vous devez maintenant actualiser le portail utilisateur pour commencer à utiliser votre abonnement.

3. Pour afficher l’abonnement que vous venez de créer, cliquez sur **Autres services**, sur **Abonnements**, puis sur votre nouvel abonnement. Une fois que vous êtes abonné à une offre, actualisez le portail pour voir si les nouveaux services ont été ajoutés à votre nouvel abonnement. Dan cet exemple, **Machines virtuelles** a été ajouté.

   ![Afficher l’abonnement](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>Tester l’offre
En étant connecté au [portail utilisateur](https://portal.local.azurestack.external), vous pouvez tester l’offre en approvisionnant une machine virtuelle à l’aide des fonctionnalités du nouvel abonnement. 

> [!NOTE]
> Ce test nécessite l’image de machine virtuelle Windows Server 2016 Datacenter, ajoutée à la place de marché Azure Stack lors d’un [didacticiel précédent](asdk-marketplace-item.md). 

1. Connectez-vous au [portail utilisateur](https://portal.local.azurestack.external).

2. Dans le portail utilisateur, cliquez sur **Machines virtuelles** > **Ajouter** > **Windows Server 2016 Datacenter**, puis sur **Créer**.

3. Dans la section **Bases**, tapez un **Nom**, un **Nom d’utilisateur** et un **Mot de passe**, choisissez un **Abonnement**, créez un **Groupe de ressources** (ou sélectionnez un groupe existant), puis cliquez sur **OK**.

4. Dans la section **Choisir une taille**, cliquez sur **A1 Standard**, puis cliquez sur **Sélectionner**.  

5. Dans le panneau Paramètres, acceptez les valeurs par défaut et cliquez sur **OK**.

6. Dans la section **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.  

7. Pour voir votre nouvelle machine virtuelle, cliquez sur **Machines virtuelles**, puis recherchez la nouvelle machine virtuelle et cliquez sur son nom.

    ![Toutes les ressources](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> Le déploiement de la machine virtuelle prend quelques minutes.


## <a name="next-steps"></a>Étapes suivantes

Ce que vous avez appris dans ce didacticiel :

> [!div class="checklist"]
> * S’abonner à une offre 
> * Tester l’offre

Passez au didacticiel suivant pour découvrir comment créer uen machine virtuelle à l’aide d’un modèle.

> [!div class="nextstepaction"]
> [Créer une machine virtuelle à partir d’un modèle](asdk-create-vm-template.md)