---
title: Ajouter un élément de Place de marché Azure Stack à partir d’une source locale | Microsoft Docs
description: Décrit comment ajouter une image de système d’exploitation locale à la Place de marché Azure Stack.
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
ms.openlocfilehash: 296719ddd23fb9ee717455420906e9a634a71a8d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Didacticiel : Ajouter un élément de Place de marché Azure Stack à partir d’une source locale

En tant qu’opérateur Azure Stack, la première chose que vous devez faire pour permettre aux utilisateurs de déployer une machine virtuelle est d’ajouter une image de machine virtuelle sur la Place de marché Azure Stack. Par défaut, rien n’est publié sur la Place de marché Azure Stack, mais vous pouvez charger des images ISO de machine virtuelle pour les mettre à disposition des utilisateurs. Choisissez cette méthode si vous avez déployé Azure Stack dans un scénario déconnecté ou avec une connectivité limitée.

Dans ce didacticiel, vous allez télécharger une image de machine virtuelle Windows Server 2016 à partir de la page Windows Server Evaluations page et la charger dans la Place de marché Azure Stack.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter une image de machine virtuelle Windows Server 2016
> * Vérifier la disponibilité de l’image de machine virtuelle 
> * Tester l’image de machine virtuelle

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

- Installer les [modules Azure PowerShell compatibles avec Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell)
- Télécharger les [outils Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- Télécharger l’[image ISO de machine virtuelle Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) à partir de la page Windows Server Evaluations

## <a name="add-a-windows-server-vm-image"></a>Ajouter une image de machine virtuelle Windows Server
Vous pouvez publier l’image Windows Server 2016 sur la Place de marché Azure Stack en ajoutant une image ISO téléchargée précédemment à l’aide de PowerShell. 

Choisissez cette méthode si vous avez déployé Azure Stack dans un scénario déconnecté ou avec une connectivité limitée.

1. Importez les modules Azure Stack `Connect` et `ComputeAdmin` PowerShell inclus dans le répertoire tools d’Azure Stack à l’aide des commandes suivantes :

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Exécutez l’un des scripts suivants sur l’ordinateur hôte ASDK, en fonction du déploiement de votre environnement Azure Stack (à l’aide de Azure Active Directory ou bien avec Active Directory Federation Services) :

  - Commandes pour les **déploiements Azure AD** : 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Commandes pour les **déploiements AD FS** :
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Ajoutez l’image Windows Server 2016 sur la Place de marché Azure Stack. (Remplacez *fully_qualified_path_to_ISO* avec le chemin d’accès vers l’image ISO de Windows Server 2016 que vous avez téléchargé.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

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
> * Ajouter une image de machine virtuelle Windows Server 2016
> * Vérifier la disponibilité de l’image de machine virtuelle 
> * Tester l’image de machine virtuelle

Passez au didacticiel suivant pour apprendre à créer une offre et un plan Azure Stack.

> [!div class="nextstepaction"]
> [Offrir des services PaaS Azure Stack](asdk-offer-services.md)