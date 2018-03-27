---
title: Ajouter un élément de la Place de marché Azure Stack à partir d’Azure | Microsoft Docs
description: Décrit comment ajouter une image de machine virtuelle Windows Server basée sur Azure dans la Place de marché Azure Stack.
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
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Didacticiel : Ajouter un élément de la Place de marché Azure Stack à partir d’Azure

En tant qu’opérateur Azure Stack, la première chose que vous devez faire pour permettre aux utilisateurs de déployer une machine virtuelle est d’ajouter une image de machine virtuelle sur la Place de marché Azure Stack. Par défaut, rien n’est publié dans la Place de marché Azure Stack, mais vous pouvez télécharger des éléments à partir d’une [liste claire d’éléments Azure](.\.\azure-stack-marketplace-azure-items.md) prétestés pour s’exécuter sur Azure Stack. Choisissez cette méthode si vous êtes dans un scénario connecté et si vous avez inscrit votre instance Azure Stack auprès d’Azure.

Dans ce didacticiel, vous allez ajouter l’image de machine virtuelle de Windows Server 2016 à partir de la Place de marché Azure vers la Place de marché Azure Stack.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un élément de la Place de marché Azure Stack de machine virtuelle Windows Server
> * Vérifier la disponibilité de l’image de machine virtuelle 
> * Tester l’image de machine virtuelle

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

- Installer les [modules Azure PowerShell compatibles avec Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell)
- Télécharger les outils [Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Inscrire l’ASDK](asdk-register.md) auprès de votre abonnement Azure

## <a name="add-a-windows-server-vm-image"></a>Ajouter une image de machine virtuelle Windows Server
Vous pouvez ajouter une image Windows Server 2016 dans la Place de marché Azure Stack en la téléchargeant à partir de la Place de marché Azure. Choisissez cette méthode si vous êtes dans un scénario connecté et si vous avez déjà [inscrit votre instance Azure Stack](asdk-register.md) auprès d’Azure.

1. Connectez-vous au [portail d’administration ASDK](https://adminportal.local.azurestack.external).

2. Sélectionnez **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir de Azure**. 

   ![Ajout à partir d’Azure](media/asdk-marketplace-item/azs-marketplace.png)

3. Localisez ou recherchez l’image **Windows Server Datacenter 2016**.

4. Sélectionnez l’image **Windows Server Datacenter 2016**, puis cliquez sur **Télécharger**.

   ![Télécharger l’image à partir d’Azure](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> Le téléchargement et la publication de l’image de machine virtuelle sur la Place de marché Azure Stack prend environ une heure. 

Lorsque le téléchargement est terminé, l’image est publiée et disponible sous **Gestion de la Place de Marché**. L’image est également disponible sous **Calcul** pour créer des machines virtuelles.

## <a name="verify-the-marketplace-item-is-available"></a>Vérifier la disponibilité de l’élément de la Place de marché
Suivez ces étapes pour vérifier que la nouvelle image de machine virtuelle est disponible dans la Place de marché Azure Stack.

1. Connectez-vous au [portail d’administration ASDK](https://adminportal.local.azurestack.external).

2. Sélectionnez **Autres services** > **Gestion de la Place de marché**. 

3. Vérifiez que l’image de machine virtuelle de Windows Server Datacenter 2016 a été ajoutée avec succès.

   ![Image téléchargée à partir d’Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Tester l’image de machine virtuelle
En tant qu’opérateur Azure Stack, vous pouvez utiliser le [portail de l’administrateur](https://adminportal.local.azurestack.external) pour créer une machine virtuelle de test afin de vérifier la disponibilité effective de l’image. 

1. Connectez-vous au [portail d’administration ASDK](https://adminportal.local.azurestack.external).

2. Cliquez sur **Nouveau** > **Calcul** > **Windows Server 2016 Datacenter** > **Créer**.  
 ![Créer une machine virtuelle à partir d’une image de la Place de marché](media/asdk-marketplace-item/new-compute.png)

3. Dans le panneau **De base**, entrez les informations suivantes et cliquez sur **OK** :
  - **Nom** : test-vm-1
  - **Nom d’utilisateur** : AdminTestUser
  - **Mot de passe** : AzS-TestVM01
  - **Abonnement** : accepter l’abonnement du fournisseur par défaut
  - **Groupe de ressources** : test-vm-rg
  - **Emplacement** : local

4. Dans le panneau **Choisir une taille**, cliquez sur **A1 Standard**, puis cliquez sur **Sélectionner**.  

5. Dans le panneau **Paramètres**, acceptez les valeurs par défaut et cliquez sur **OK**.

6. Dans le panneau **Résumé**, cliquez sur **OK** pour créer la machine virtuelle.  
> [!NOTE]
> Le déploiement de la machine virtuelle prend quelques minutes.

7. Pour afficher la nouvelle machine virtuelle, cliquez sur **Machines virtuelles**, puis recherchez **test-vm-1** et cliquez sur son nom.
    ![Première image de machine virtuelle de test affichée](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Une fois connecté à la machine virtuelle et après avoir vérifié que l’image de test fonctionne correctement, vous devez supprimer le groupe de ressources de test. Cela permettra de libérer les ressources limitées disponibles pour les installations ASDK à nœud unique.

Quand vous n’en avez plus besoin, supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées en suivant ces étapes :

1. Connectez-vous au [portail d’administration ASDK](https://adminportal.local.azurestack.external).
2. Cliquez sur **Groupes de ressources** > **test-vm-rg** > **Supprimer le groupe de ressources**.
3. Entrez **test-vm-rg** comme nom du groupe de ressources, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter un élément de la Place de marché Azure Stack de machine virtuelle Windows Server
> * Vérifier la disponibilité de l’image de machine virtuelle 
> * Tester l’image de machine virtuelle

Passez au didacticiel suivant pour apprendre à créer une offre et un plan Azure Stack.

> [!div class="nextstepaction"]
> [Offrir des services PaaS Azure Stack](asdk-offer-services.md)