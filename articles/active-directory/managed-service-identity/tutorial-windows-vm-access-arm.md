---
title: "Utiliser une MSI de machine virtuelle Windows pour accéder à Azure Resource Manager"
description: "Ce didacticiel vous guide tout au long du processus consistant à utiliser l’identité du service administré (MSI) de machine virtuelle Windows pour accéder à Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 616bbc9c657d5d6afba962c676d44ac0baa6841e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>Utiliser une identité du service administré (MSI) de machine virtuelle Windows pour accéder au Gestionnaire des ressources

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer une identité du service administré (MSI) pour une machine virtuelle Windows. Vous pouvez ensuite utiliser cette identité pour accéder à l’API d’Azure Resource Manager. Les identités du service administré sont gérées automatiquement par Azure et vous permettent de vous authentifier sur les services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer MSI sur une machine virtuelle Windows 
> * Accorder à votre machine virtuelle l’accès à un groupe de ressources dans Azure Resource Manager 
> * Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle et l’utiliser pour appeler Azure Resource Manager

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Création d'une machine virtuelle Windows dans un nouveau groupe de ressources

Pour ce didacticiel, nous allons créer une machine virtuelle Windows.  Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1.  Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2.  Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 
3.  Saisissez les informations de la machine virtuelle. Le **Nom d’utilisateur** et le **Mot de passe** créés ici sont les informations d’identification nécessaires pour vous connecter à la machine virtuelle.
4.  Choisissez un **Abonnement** approprié pour la machine virtuelle dans la liste déroulante.
5.  Pour sélectionner un nouveau **Groupe de ressources** dans lequel créer votre machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6.  Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Conservez les valeurs par défaut dans la page des paramètres et cliquez sur **OK**.

    ![Texte de remplacement d’image](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle 

Une MSI de machine virtuelle permet d’obtenir des jetons d’accès émanant d’Azure AD sans avoir à insérer d’informations d’identification dans votre code. À l’activation de la MSI, Azure crée une identité administrée pour votre machine virtuelle. En arrière plan, l’activation de la MSI effectue deux opérations : cela installe l’extension de machine virtuelle de la MSI sur votre machine virtuelle et cela active la MSI pour Azure Resource Manager.

1.  Sélectionnez la **Machine virtuelle** sur laquelle vous souhaitez activer l’identité du service administré.  
2.  Dans la barre de navigation gauche, cliquez sur **Configuration**. 
3.  **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non. 
4.  Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.  
    ![Texte de remplacement d’image](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier et confirmer les extensions sur cette machine virtuelle, cliquez sur **Extensions**. Si MSI est activée, l’extension **ManagedIdentityExtensionforWindows** s’affiche dans la liste.

    ![Texte de remplacement d’image](../media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Accorder l’accès à un groupe de ressources dans Azure Resource Manager à votre machine virtuelle
À l’aide de l’identité de service administré, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD.  Azure Resource Manager prend en charge l’authentification Azure AD.  Tout d’abord, nous devons accorder à cette identité de machine virtuelle l’accès à une ressource dans le Gestionnaire des ressources, dans ce cas le groupe de ressources dans lequel est contenue la machine virtuelle.  

1.  Accédez à l’onglet de **Groupes de ressources**. 
2.  Sélectionnez le **Groupe de ressources** spécifique que vous avez créé pour la **machine virtuelle Windows**. 
3.  Accédez au **Contrôle d’accès (IAM)** dans le volet de gauche. 
4.  Cliquez pour **Ajouter** une nouvelle attribution de rôle pour votre **machine virtuelle Windows**.  Choisissez **Rôle** en tant que **Lecteur**. 
5.  Dans la liste déroulante suivante, sélectionnez **Attribuer un accès** à la ressource **Machine virtuelle**. 
6.  Ensuite, assurez-vous que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**. Et pour **Groupe de ressources**, sélectionnez **Tous les groupes de ressources**. 
7.  Enfin, choisissez votre machine virtuelle Windows dans la liste déroulante dans **Sélectionner** et cliquez sur **Enregistrer**.

    ![Texte de remplacement d’image](../media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de machine virtuelle et l’utiliser pour appeler Azure Resource Manager 

Vous devez utiliser **PowerShell** dans cette partie.  Si vous ne l’avez pas installé, vous pouvez le [télécharger ici](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1). 

1.  Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows. Puis, dans **Vue d’ensemble**, cliquez sur **Connecter**. 
2.  Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la machine virtuelle Windows. 
3.  Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez **PowerShell** dans la session à distance. 
4.  À l’aide de Invoke-WebRequest de Powershell, envoyez une requête au point de terminaison MSI local pour obtenir un jeton d’accès pour Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > La valeur du paramètre « ressource » doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    
    Ensuite, extrayez la réponse complète qui est stockée sous forme de chaîne au format JavaScript Objet Notation (JSON) dans l’objet $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ensuite, extrayez le jeton d’accès de la réponse.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Enfin, appelez Azure Resource Manager à l’aide du jeton d’accès. Dans cet exemple, nous utilisons également le Invoke-WebRequest de PowerShell pour appeler Azure Resource Manager et inclure le jeton d’accès dans l’en-tête d’autorisation.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > L’URL respecte la casse, par conséquent, assurez-vous que la casse est identique à celle utilisée précédemment lorsque vous avez nommé le groupe de ressources, et que la majuscule « G » dans « resourceGroups » est correcte.
        
    La commande suivante renvoie les détails du groupe de ressources :

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](overview.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

