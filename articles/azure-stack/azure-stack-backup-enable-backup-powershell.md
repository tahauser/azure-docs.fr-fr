---
title: "Activer la sauvegarde d’Azure Stack avec PowerShell | Microsoft Docs"
description: "Activez le service de sauvegarde d’infrastructure avec Windows PowerShell pour permettre la restauration d’Azure Stack en cas de panne."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: cbec6242fb4e185c9801a93fc2c4b35721269c2f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Activer la sauvegarde d’Azure Stack avec PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Activez le service de sauvegarde d’infrastructure avec Windows PowerShell pour permettre la restauration d’Azure Stack en cas de panne. Vous pouvez accéder aux cmdlets PowerShell pour activer la sauvegarde, démarrer la sauvegarde et obtenir des informations sur la sauvegardes via le point de terminaison de gestion d’opérateur.

## <a name="download-azure-stack-tools"></a>Télécharger les outils Azure Stack

Installer et configurez PowerShell pour Azure Stack et les outils Azure Stack. Consultez la page [Devenir opérationnel avec PowerShell dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Charger les modules de connexion et d’infrastructure

Ouvrez Windows PowerShell avec une invite et exécutez les commandes suivantes :

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Configurer l’environnement du gestionnaire de ressources et se connecter au point de terminaison de gestion d’opérateur

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour configurer l’environnement du gestionnaire de ressources et vous connecter au point de terminaison de gestion d’opérateur.

| Variable    | Description |
|---          |---          |
| $TenantName | Nom du locataire Azure Active Directory. |
| Nom du compte de l’opérateur        | Nom du compte de l’opérateur Azure Stack. |
| Point de terminaison Azure Resource Manager | URL menant à Azure Resource Manager. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Générer une nouvelle clé de chiffrement

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Vous devez utiliser les outils AzureStack pour générer la clé.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour activer la sauvegarde

Dans la même session PowerShell, modifiez le script PowerShell suivant en ajoutant les variables de votre environnement. Exécutez le script mis à jour pour indiquer le partage de sauvegarde, les informations d’identification et la clé de chiffrement pour le service de sauvegarde d’infrastructure.

| Variable        | Description   |
|---              |---                                        |
| $username       | Saisissez le **nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur de l’emplacement du lecteur partagé. Par exemple : `Contoso\administrator`. |
| $password       | Saisissez le **mot de passe** de l’utilisateur. |
| $sharepath      | Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Vous devez utiliser une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour garantir la disponibilité des données de sauvegarde, l’appareil doit se trouver dans un emplacement distinct. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Confirmer les paramètres de sauvegarde

Dans la même session PowerShell, exécutez les commandes suivantes :

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

Le résultat doit ressembler à la sortie JSON suivante :

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Étapes suivantes

 - Pour savoir comment exécuter une sauvegarde, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
- Pour savoir comment vérifier que votre sauvegarde a été exécutée, voir [Confirmer que la sauvegarde est terminée dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md ).
