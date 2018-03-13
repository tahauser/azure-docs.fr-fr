---
title: "Faire pivoter les clés secrètes dans Azure Stack | Microsoft Docs"
description: "Apprenez à faire pivoter vos clés secrètes dans Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Faire pivoter les clés secrètes dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Mettez régulièrement à jour vos mots de passe des composants Azure Stack.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Mise à jour des mots de passe pour le contrôleur BMC (Baseboard Management Controller)

Le contrôleur BMC (Baseboard Management Controller) analyse l’état physique de vos serveurs. Les spécifications et les instructions sur la mise à jour du mot de passe du contrôleur BMC varient en fonction de votre fournisseur de matériel OEM.

1. Mettez à jour le contrôleur BMC sur les serveurs physiques de Azure Stack en suivant les instructions de votre fabricant OEM. Tous les contrôleurs BMC de votre environnement doivent avoir le même mot de passe.
2. Ouvrez un point de terminaison privilégié dans des sessions Azure Stack. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md).
3. Lorsque votre invite PowerShell devient **[adresse IP ou nom de machine virtuelle ERCS]: PS>** ou **[azs-ercs01]: PS>**, en fonction de l’environnement, exécutez `Set-BmcPassword` en exploitant `invoke-command`. Passez la variable de session de votre point de terminaison privilégié en tant que paramètre. Par exemple : 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    Vous pouvez également utiliser la version statique de PowerShell avec les mots de passe sous forme de lignes de code :
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurité et Azure Stack, consultez [Situation de sécurité de l’infrastructure Azure Stack](azure-stack-security-foundations.md).
