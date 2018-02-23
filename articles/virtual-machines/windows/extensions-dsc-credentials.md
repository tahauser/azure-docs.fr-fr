---
title: "Transfert des informations d’identification à Azure à l’aide de DSC | Microsoft Docs"
description: "Vue d’ensemble du transfert sécurisé des informations d’identification aux machines virtuelles Azure à l’aide de la Configuration de l’état souhaité PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/17/2018
ms.author: zachal,migreene
ms.openlocfilehash: 140ca3cc9b72afac720e5bcf1d620ac9b1b72132
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="passing-credentials-to-the-azure-dsceextension-handler"></a>Transfert des informations d’identification au gestionnaire DSCEextension Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Cet article traite de l’extension de Configuration de l’état souhaité pour Azure.
Vous trouverez une vue d’ensemble du gestionnaire de l’extension DSC dans [Présentation du gestionnaire de l’extension de Configuration de l’état souhaité Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="passing-in-credentials"></a>Transfert d’informations d’identification

Dans le cadre du processus de configuration, vous devrez peut-être configurer des comptes d’utilisateur, accéder aux services ou installer un programme dans un contexte utilisateur. Pour effectuer ces opérations, vous devez fournir les informations d’identification.

DSC permet d’effectuer des configurations paramétrables dans lesquelles les informations d’identification sont transmises à la configuration et stockées en toute sécurité dans les fichiers MOF.
Le Gestionnaire d’extensions Azure simplifie la gestion des informations d’identification en fournissant une gestion automatique des certificats.

Examinez le script de configuration DSC suivant qui crée un compte d’utilisateur local avec le mot de passe spécifié :

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Il est important d’inclure l’ *hôte local du nœud* dans le cadre de la configuration.
Si cette instruction est manquante, les étapes suivantes ne fonctionnent pas, car le gestionnaire d’extensions recherche spécifiquement l’instruction relative à l’hôte local du nœud.
Il est également important d’inclure le caractère *[PsCredential]*, car ce type spécifique déclenche l’extension pour chiffrer les informations d’identification.

Publiez ce script sur le stockage d’objets blob :

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Définissez l’extension DSC Azure et fournissez les informations d’identification :

```powershell
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```

## <a name="how-credentials-are-secured"></a>Sécurisation des informations d’identification

L’exécution de ce code invite à entrer les informations d’identification.
Une fois celles-ci fournies, elles sont stockées en mémoire brièvement.
Lorsqu’elles sont publiées avec l’applet de commande `Set-AzureVmDscExtension` , elles sont transmises via le protocole HTTPS à la machine virtuelle, où elles sont stockées de manière chiffrée sur le disque par Azure, à l’aide du certificat local de la machine virtuelle.
Elles sont ensuite déchiffrées brièvement en mémoire, puis rechiffrées pour leur transfert à DSC.

Ce comportement diffère de l’ [utilisation de configurations sécurisées sans le gestionnaire d’extensions](https://msdn.microsoft.com/powershell/dsc/securemof). L’environnement Windows Azure permet de transmettre des données de configuration en toute sécurité via des certificats. Lors de l’utilisation du gestionnaire d’extensions DSC, il est inutile de fournir une entrée $CertificatePath ou $CertificateID/$Thumbprint dans ConfigurationData.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le gestionnaire d’extensions DSC Azure, voir [Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Examinez le [modèle Azure Resource Manager pour l’extension DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Pour accéder aux fonctionnalités supplémentaires que vous pouvez gérer avec DSC PowerShell, [parcourez PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour voir des ressources DSC supplémentaires.
