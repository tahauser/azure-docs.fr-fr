---
title: "Se connecter à Azure Stack | Microsoft Docs"
description: "Découvrez comment vous connecter à Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

*S’applique à : Kit de développement Azure Stack*

Pour pouvoir gérer des ressources, vous devez d’abord vous connecter au Kit de développement Azure Stack. Dans cet article, nous décrivons les étapes à suivre pour vous connecter au Kit de développement. Vous pouvez choisir l’une de ces deux options de connexion :

* [Connexion Bureau à distance](#connect-with-remote-desktop). Lorsque vous vous connectez à l’aide de la connexion Bureau à distance, un seul utilisateur peut se connecter rapidement à partir du kit de développement.
* [Réseau privé virtuel (VPN)](#connect-with-vpn). Lorsque vous vous connectez à l’aide d’un VPN, plusieurs utilisateurs peuvent se connecter simultanément à partir de clients en dehors de l’infrastructure Azure Stack (nécessite une configuration).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Se connecter à Azure Stack à l’aide de la connexion Bureau à distance
Un seul utilisateur simultané peut gérer les ressources depuis le portail de l’opérateur ou le portail de l’utilisateur via une connexion Bureau à distance.

1. Ouvrez une connexion Bureau à distance et connectez-vous au kit de développement. Pour le nom d’utilisateur, saisissez **AzureStack\AzureStackAdmin**. Utilisez le mot de passe d’opérateur que vous avez spécifié lors de la configuration de Azure Stack.  

2. Sur l’ordinateur du kit de développement, ouvrez le Gestionnaire de serveur. Sélectionnez **Serveur Local**, désactivez la case **Sécurité renforcée d’Internet Explorer**, puis fermez le Gestionnaire de serveur.

3. Pour ouvrir le [portail utilisateur](azure-stack-key-features.md#portal), accédez à https://portal.local.azurestack.external. Connectez-vous à l’aide des informations d’identification d’utilisateur. Pour ouvrir le [portail d’opérateur](azure-stack-key-features.md#portal) de Azure Stack, accédez à https://adminportal.local.azurestack.external. Connectez-vous en utilisant les informations d’identification de Azure Active Directory (Azure AD) que vous avez spécifié pendant l’installation.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Se connecter à Azure Stack en utilisant l’option VPN

Vous pouvez établir une connexion VPN avec tunneling fractionné à un Kit de développement Azure Stack. Vous pouvez utiliser une connexion VPN pour accéder au portail de l’opérateur Azure Stack, au portail de l’utilisateur et à des outils installés localement tels que Visual Studio et PowerShell, pour gérer des ressources Azure Stack. La connectivité VPN est prise en charge dans les déploiements Azure AD et Active Directory Federation Services (AD FS). Les connexions VPN permettent à plusieurs clients de se connecter à Azure Stack en même temps. 

> [!NOTE] 
> Une connexion VPN ne fournit pas de connectivité aux machines virtuelles de l’infrastructure Azure Stack. 

### <a name="prerequisites"></a>Composants requis

1. Installez [Azure PowerShell pour Azure Stack](azure-stack-powershell-install.md) sur votre ordinateur local.  
2. Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Configurer une connectivité VPN

Pour créer une connexion VPN au kit de développement, ouvrez Windows PowerShell en tant qu’administrateur sur votre ordinateur local Windows. Ensuite, exécutez le script suivant (mise à jour des valeurs de l’adresse IP et du mot de passe pour votre environnement) :

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la configuration réussit, **azurestack** apparaît dans votre liste des connexions VPN.

![Connexions réseau](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Connectez-vous à l’instance Azure Stack à l’aide d’une des méthodes suivantes :  

* Utiliser la commande `Connect-AzsVpn ` : 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quand vous y êtes invité, approuvez l’hôte Azure Stack et installez le certificat fourni par **AzureStackCertificateAuthority** dans le magasin de certificats de votre ordinateur local. (L’invite de commandes peut être masquée par la fenêtre PowerShell.) 

* Sur votre ordinateur local, ouvrez **Paramètres réseau** > **VPN** > **azurestack** > **Se connecter**. À l’invite de connexion, entrez le nom d’utilisateur (**AzureStack\AzureStackAdmin**) et votre mot de passe.

### <a name="test-vpn-connectivity"></a>Connectivité VPN de test

Pour tester la connexion au portail, ouvrez un navigateur Internet et accédez au portail de l’utilisateur (https://portal.local.azurestack.external/) ou au portail de l’opérateur (https://adminportal.local.azurestack.external/). Connectez-vous et créez un groupe de ressources.  

## <a name="next-steps"></a>Étapes suivantes

[Mettre des machines virtuelles à la disposition de vos utilisateurs Azure Stack](azure-stack-tutorial-tenant-vm.md)

