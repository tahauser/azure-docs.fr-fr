---
title: "Utiliser l’identité du service administré d’une machine virtuelle Linux pour accéder à Azure Key Vault"
description: "Ce didacticiel vous guide tout au long du processus consistant à utiliser l’identité du service administré (MSI) d’une machine virtuelle Linux pour accéder à Azure Resource Manager."
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
ms.openlocfilehash: b14d1729592b0b97d2616189c6e0ae4727ebaf91
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Utiliser une identité MSI (Managed Service Identity) de machine virtuelle Linux pour accéder à Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer l’identité MSI (Managed Service Identity) sur une machine virtuelle Linux et comment l’utiliser pour accéder à Azure Key Vault. En agissant comme un amorçage, le coffre de clés permet à votre application cliente d’utiliser le secret pour accéder aux ressources non sécurisées par Azure Active Directory (AD). Les identités MSI sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code. 

Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Activer l’identité du service administré sur une machine virtuelle Linux 
> * Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault 
> * Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer un secret de Key Vault 
 
## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans la page des paramètres et cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, l’activation de l’identité du service administré entraîne deux actions : elle installe l’extension de l’identité du service administré de la machine virtuelle et active l’identité du service administré pour la machine virtuelle.  

1. Sélectionnez la **Machine virtuelle** sur laquelle vous souhaitez activer l’identité du service administré.
2. Dans la barre de navigation gauche, cliquez sur **Configuration**.
3. **Identité du service administré** s’affiche. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier les extensions présentes sur cette **Machine virtuelle Linux**, cliquez sur **Extensions**. Si l’identité MSI est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Accorder à votre machine virtuelle l’accès à un secret stocké dans Key Vault  

À l’aide de l’identité de service administré, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources prenant en charge l’authentification Azure AD. Toutefois, tous les services Azure ne prennent pas en charge l’authentification Azure AD. Pour utiliser l’identité MSI avec ces services, stockez les informations d’identification des services dans Azure Key Vault, puis utilisez l’identité MSI pour accéder au coffre de clés pour récupérer les informations d’identification. 

Tout d’abord, nous devons créer un Key Vault et accorder l’accès au Key Vault à l’identité de la machine virtuelle.   

1. En haut de la barre de navigation de gauche, sélectionnez **Créer une ressource** > **Sécurité + Identité** > **Key Vault**.  
2. Entrez un **Nom** pour le nouveau Key Vault. 
3. Recherchez le Key Vault dans le même abonnement et le même groupe de ressources que la machine virtuelle créée précédemment. 
4. Sélectionnez des **Stratégies d’accès** et cliquez sur le bouton **Ajouter un nouveau**. 
5. Dans Configurer à partir du modèle, sélectionnez **Gestion de secret**. 
6. Choisissez **Sélectionner le principal**, et dans la zone de recherche entrez le nom de la machine virtuelle créée précédemment.  Sélectionnez la machine virtuelle dans la liste des résultats, cliquez sur **Sélectionner**. 
7. Cliquez sur **OK** pour terminer l’ajout de la nouvelle stratégie d’accès, puis sur **OK** pour terminer la sélection de stratégie d’accès. 
8. Cliquez sur **Créer** pour terminer la création de Key Vault. 

    ![Texte de remplacement d’image](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Ensuite, ajoutez un secret à Key Vault, afin de pouvoir le retrouver à l’aide du code en cours d’exécution dans votre machine virtuelle : 

1. Sélectionnez **Toutes les ressources**, puis sélectionnez le coffre de clés créé précédemment. 
2. Sélectionnez **Secret**, puis cliquez sur **Ajouter**. 
3. Dans les **Options de chargement**, sélectionnez **Manuel**. 
4. Entrez un nom et une valeur pour le secret.  Vous pouvez choisir la valeur de votre choix. 
5. Laissez les champs pour la date d’activation et la date d’expiration vides, puis pour **Activé**, laissez la valeur **Oui**. 
6. Cliquez sur **Créer** pour créer le secret. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtenir un jeton d’accès à l’aide d’une identité de machine virtuelle et l’utiliser pour récupérer la clé secrète de Key Vault  

Pour effectuer cette procédure, vous avez besoin d’un client SSH.  Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Dans le portail, accédez à votre machine virtuelle Linux et dans **Vue d’ensemble**, cliquez sur **Connexion**. 
2. **Connectez-vous** à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre de terminal, envoyez une requête à l’aide de CURL au point de terminaison MSI local pour obtenir un jeton accès pour Azure Key Vault.  
 
    Vous trouverez la requête CURL pour le jeton d’accès ci-dessous.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    La réponse inclut le jeton d’accès dont vous avez besoin pour accéder au Gestionnaire des ressources. 
    
    Réponse :  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Vous pouvez utiliser ce jeton d’accès pour vous authentifier sur Azure Key Vault.  La requête CURL suivante montre comment lire un secret de Key Vault à l’aide de CURL et de l’API REST de Key Vault.  Vous avez besoin de l’URL de votre Key Vault qui se trouve dans la section **Bases** de la page **Vue d’ensemble** de Key Vault.  Vous avez également besoin du jeton d’accès que vous avez obtenu lors de l’appel précédent. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La réponse aura l’aspect suivant : 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Après avoir récupéré le secret à partir de Key Vault, vous pouvez l’utiliser pour vous authentifier sur un service qui requiert un nom et un mot de passe.


## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.




