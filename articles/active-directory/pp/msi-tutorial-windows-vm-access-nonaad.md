---
title: "Utiliser une MSI de machine virtuelle Windows pour accéder à Azure Key Vault"
description: "Ce didacticiel vous guide tout au long de l’utilisation d’une identité du service administré (MSI) de machine virtuelle Windows pour accéder à Azure Key Vault."
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 151a0a1fee72c7bd7adcda8e23ebb06d96e822ae
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Utiliser une identité MSI (Managed Service Identity) de machine virtuelle Windows pour accéder à Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce didacticiel vous montre comment activer l’identité MSI (Managed Service Identity) sur une machine virtuelle Windows et comment l’utiliser pour accéder à l’API d’Azure Key Vault. En agissant comme un amorçage, le coffre de clés permet à votre application cliente d’utiliser le secret pour accéder aux ressources non sécurisées par Azure Active Directory (AD). Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. 

Vous allez apprendre à effectuer les actions suivantes :


> [!div class="checklist"]
> * Activer l’identité du service administré sur une machine virtuelle Windows 
> * Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault 
> * Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer un secret de Key Vault 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Création d'une machine virtuelle Windows dans un nouveau groupe de ressources

Pour ce didacticiel, nous allons créer une machine virtuelle Windows. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1.  Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2.  Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 
3.  Saisissez les informations de la machine virtuelle. Le **Nom d’utilisateur** et le **Mot de passe** créés ici sont les informations d’identification nécessaires pour vous connecter à la machine virtuelle.
4.  Choisissez un **Abonnement** approprié pour la machine virtuelle dans la liste déroulante.
5.  Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer une machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6.  Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

    ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle 

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. À l’activation de la MSI, Azure crée une identité administrée pour votre machine virtuelle. En arrière plan, l’activation de la MSI effectue deux opérations : cela installe l’extension de machine virtuelle de la MSI sur votre machine virtuelle et cela active la MSI pour Azure Resource Manager.

1.  Sélectionnez la **Machine virtuelle** sur laquelle vous souhaitez activer l’identité du service administré.  
2.  Dans la barre de navigation gauche, cliquez sur **Configuration**. 
3.  **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non. 
4.  Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.  

    ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier et confirmer les extensions sur cette machine virtuelle, cliquez sur **Extensions**. Si MSI est activée, l’extension **ManagedIdentityExtensionforWindows** s’affiche dans la liste.

    ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault 
 
À l’aide de l’identité de service administré, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD.  Toutefois, tous les services Azure ne prennent pas en charge l’authentification Azure AD. Pour utiliser l’identité MSI avec ces services, stockez les informations d’identification des services dans Azure Key Vault, puis utilisez l’identité MSI pour accéder au coffre de clés pour récupérer les informations d’identification. 

Tout d’abord, nous devons créer un Key Vault et accorder l’accès au Key Vault à l’identité de la machine virtuelle.   

1. En haut de la barre de navigation gauche, sélectionnez **+ Nouveau**, **Sécurité + Identité**, puis **Key Vault**.  
2. Entrez un **Nom** pour le nouveau Key Vault. 
3. Recherchez le Key Vault dans le même abonnement et le même groupe de ressources que la machine virtuelle créée précédemment. 
4. Sélectionnez des **Stratégies d’accès** et cliquez sur le bouton **Ajouter un nouveau**. 
5. Dans Configurer à partir du modèle, sélectionnez **Gestion de secret**. 
6. Choisissez **Sélectionner le principal**, et dans la zone de recherche entrez le nom de la machine virtuelle créée précédemment.  Sélectionnez la machine virtuelle dans la liste des résultats, cliquez sur **Sélectionner**. 
7. Cliquez sur **OK** pour terminer l’ajout de la nouvelle stratégie d’accès, puis sur **OK** pour terminer la sélection de stratégie d’accès. 
8. Cliquez sur **Créer** pour terminer la création de Key Vault. 

    ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Ensuite, ajoutez un secret à Key Vault, afin de pouvoir le retrouver à l’aide du code en cours d’exécution dans votre machine virtuelle : 

1. Sélectionnez **Toutes les ressources**, puis sélectionnez le coffre de clés créé précédemment. 
2. Sélectionnez **Secret**, puis cliquez sur **Ajouter**. 
3. Dans les **Options de chargement**, sélectionnez **Manuel**. 
4. Entrez un nom et une valeur pour le secret.  Vous pouvez choisir la valeur de votre choix. 
5. Laissez les champs pour la date d’activation et la date d’expiration vides, puis pour **Activé**, laissez la valeur **Oui**. 
6. Cliquez sur **Créer** pour créer le secret. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer un secret de Key Vault  

Si PowerShell 4.3.1 ou version ultérieure n’est pas installé, vous devez [télécharger et installer la dernière version](https://docs.microsoft.com/powershell/azure/overview).

Tout d’abord, nous utilisons l’identité MSI de machine virtuelle pour obtenir un jeton d’accès permettant de s’authentifier auprès de Key Vault :
 
1. Dans le portail, accédez à **Machines virtuelles** et accédez à votre machine virtuelle Windows. Puis, dans **Vue d’ensemble**, cliquez sur **Connecter**.
2. Entrez le **Nom d’utilisateur** et le **Mot de passe** que vous avez ajoutés lorsque vous avez créé la **machine virtuelle Windows**.  
3. Maintenant que vous avez créé une **Connexion au Bureau à distance** avec la machine virtuelle, ouvrez PowerShell dans la session à distance.  
4. Dans PowerShell, appelez la requête web sur le client pour obtenir le jeton de l’hôte local dans le port spécifique pour la machine virtuelle.  

    La requête PowerShell :
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Ensuite, extrayez la réponse complète qui est stockée sous forme de chaîne au format JavaScript Objet Notation (JSON) dans l’objet $response.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Ensuite, extrayez le jeton d’accès de la réponse.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Enfin, utilisez la commande Invoke-WebRequest de PowerShell pour récupérer le secret que vous avez créé précédemment dans Key Vault et transmettre le jeton d’accès vers l’en-tête d’autorisation.  Vous avez besoin de l’URL de votre Key Vault qui se trouve dans la section **Bases** de la page **Vue d’ensemble** de Key Vault.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    La réponse aura l’aspect suivant : 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Après avoir récupéré le secret à partir de Key Vault, vous pouvez l’utiliser pour vous authentifier sur un service qui requiert un nom et un mot de passe. 

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
