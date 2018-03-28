---
title: Configurations post-déploiement pour le kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Décrit les changements de configuration qu’il est recommandé d’effectuer après avoir installé le kit de développement Azure Stack (ASDK).
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
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>Tâches de configuration après l’installation du kit ASDK
Après [l’installation du kit ASDK](asdk-install.md), il est recommandé d’apporter quelques modifications à la configuration. 

## <a name="install-azure-stack-powershell"></a>Installer Azure Stack PowerShell 
Des modules Azure PowerShell compatibles avec Azure Stack sont nécessaires pour utiliser Azure Stack.

Les commandes PowerShell pour Azure Stack sont installées par le biais de PowerShell Gallery. Pour inscrire le référentiel PSGallery, ouvrez une session PowerShell avec élévation de privilèges, puis exécutez la commande suivante :

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Les modules AzureRM compatibles avec Azure Stack sont installés par le biais de profils de version d’API. Azure Stack nécessite le profil de version d’API 2017-03-09-profile, qui est disponible avec l’installation du module AzureRM.Bootstrapper. 
 
 Vous pouvez installer Azure Stack PowerShell sur l’ordinateur hôte du kit ASDK avec ou sans connexion Internet :

- **Avec une connexion Internet** sur l’ordinateur hôte du kit ASDK. Exécutez le script PowerShell suivant pour installer ces modules dans le kit de développement :

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Si l’installation réussit, les modules AzureRM et AzureStack sont affichés dans la sortie.

- **Sans connexion Internet** sur l’ordinateur hôte du kit ASDK. Dans un scénario hors connexion, vous devez d’abord télécharger les modules PowerShell sur un ordinateur qui dispose d’une connexion Internet à l’aide des commandes PowerShell suivantes :

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureRM `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureStack `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11
  ```
  Ensuite, copiez les packages téléchargés sur l’ordinateur du kit ASDK, inscrivez cet emplacement comme référentiel par défaut, puis installez les modules AzureRM et AzureStack à partir de ce référentiel :

    ```PowerShell
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Télécharger les outils Azure Stack
[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) est un dépôt GitHub qui héberge des modules PowerShell permettant de gérer et déployer des ressources sur Azure Stack. Pour obtenir ces outils, clonez le dépôt GitHub ou téléchargez le dossier AzureStack-Tools en exécutant le script suivant :

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Valider l’installation du kit ASDK
Pour garantir le succès du déploiement du kit ASDK, vous pouvez utiliser l’applet de commande Test-AzureStack en procédant aux étapes suivantes :

1. Connectez-vous en tant qu’AzureStack\CloudAdmin sur l’ordinateur hôte du kit ASDK.
2. Ouvrez PowerShell en tant qu’administrateur (et non PowerShell ISE).
3. Exécuter : `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Exécuter : `Test-AzureStack`

L’exécution de ces tests nécessite quelques minutes. Si l’installation a réussi, la sortie ressemble à ceci :

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

En cas d’échec, suivez les étapes de dépannage.

## <a name="activate-the-administrator-and-tenant-portals"></a>Activer les portails d’administrateur et de locataire
Après les déploiements qui utilisent Azure AD, vous devez activer les portails d’administrateur et de locataire Azure Stack. Cette activation accepte de donner au portail Azure Stack et à Azure Resource Manager les autorisations appropriées (répertoriées sur la page de consentement) pour tous les utilisateurs du répertoire.

- Pour le portail Administrateur, accédez à https://adminportal.local.azurestack.external/guest/signup, lisez les informations, puis cliquez sur **Accepter**. Après avoir accepté, vous pouvez ajouter des administrateurs de service qui ne sont pas également des administrateurs de locataire de répertoire.

- Pour le portail Locataire, accédez à https://portal.local.azurestack.external/guest/signup, lisez les informations, puis cliquez sur **Accepter**. Après avoir accepté, les utilisateurs dans le répertoire peuvent se connecter au portail de locataire. 

> [!NOTE] 
> Si les portails ne sont pas activés, seul l’administrateur de répertoire peut se connecter et utiliser les portails. Si un autre utilisateur se connecte, il verra une erreur lui indiquant que l’administrateur n’a pas accordé d’autorisations aux autres utilisateurs. Lorsque l’administrateur n’appartient pas, en mode natif, au répertoire Azure Stack dans lequel il est enregistré, le répertoire Azure Stack doit être ajouté à l’URL d’activation. Par exemple, si Azure Stack est inscrit auprès de fabrikam.onmicrosoft.com et si l’utilisateur admin est admin@contoso.com, accédez à https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com pour activer le portail. 

## <a name="reset-the-password-expiration-policy"></a>Réinitialiser la stratégie d’expiration du mot de passe 
Pour faire en sorte que le mot de passe de l’hôte du kit de développement n’expire pas avant la fin de la période d’expiration, suivez ces étapes après avoir déployé le kit ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Pour modifier la stratégie d’expiration de mot de passe à partir de Powershell :
À partir d’une console Powershell avec élévation de privilèges, exécutez la commande :

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Pour modifier la stratégie d’expiration de mot de passe manuellement :
1. Sur l’hôte du kit de développement, ouvrez **Gestion des stratégies de groupe** (GPMC.MMC), puis accédez à **Gestion des stratégies de groupe** – **Forêt : azurestack.local** – **Domaines** – **azurestack.local**.
2. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
3. Dans l’Éditeur de gestion de stratégie de groupe, accédez à **Configuration de l’ordinateur** – **Stratégies** – **Paramètres Windows** – **Paramètres de sécurité**– **Stratégies de comptes** – **Stratégie de mot de passe**.
4. Dans le volet droit, double-cliquez sur **Antériorité maximale du mot de passe**.
5. Dans la boîte de dialogue **Antériorité maximale du mot de passe - Propriétés**, remplacez la valeur de **Le mot de passe expirera dans** par **180**, puis cliquez sur **OK**.

![Console de gestion des stratégies de groupe](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Étapes suivantes
[Inscrire le kit ASDK auprès d’Azure](asdk-register.md)
