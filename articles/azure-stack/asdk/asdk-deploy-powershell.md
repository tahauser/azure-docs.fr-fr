---
title: Déployer Azure Stack - PowerShell | Microsoft Docs
description: Dans ce didacticiel, vous allez installer l’ASDK à partir de la ligne de commande.
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
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>Didacticiel : Déployer l’ASDK à partir de la ligne de commande
Dans ce didacticiel, vous allez déployer le Kit de développement Azure Stack (ASDK) à partir de la ligne de commande dans un environnement non productif. 

Le Kit de développement Azure Stack est un environnement de développement et de test que vous pouvez déployer pour évaluer et présenter les fonctionnalités et services Azure Stack. Pour l’installer et le rendre opérationnel, vous devez préparer l’environnement matériel nécessaire et exécuter plusieurs scripts (cette opération peut prendre plusieurs heures). Une fois que vous aurez effectué ces étapes préalables, vous pourrez vous connecter aux portails de l’administrateur et de l’utilisateur pour commencer à utiliser Azure Stack.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Téléchargez et extrayez le package de déploiement
> * Préparer l’ordinateur hôte du Kit de développement 
> * Effectuer des configurations post-déploiement
> * Inscription auprès d’Azure

## <a name="prerequisites"></a>Prérequis
 
Préparez l’ordinateur hôte du Kit de développement. Planifiez votre matériel, vos logiciels et votre réseau. Vérifiez que l’ordinateur prévu pour héberger le Kit de développement (l’hôte du Kit de développement) a la configuration matérielle, logicielle et réseau requise. Vous devez également déterminer si vous allez utiliser Azure Active Directory (Azure AD) ou les services de fédération Active Directory (AD FS). Avant de démarrer votre déploiement, assurez-vous que tous ces prérequis sont remplis pour que le processus d’installation s’exécute correctement. 

Avant de déployer l’ASDK, assurez-vous que le matériel de l’ordinateur hôte du Kit de développement, le système d’exploitation, le compte et les configurations réseau répondent à la configuration minimale requise pour l’installation de l’ASDK.

**[Consultez les considérations en matière de planification du déploiement du Kit de développement Azure Stack](asdk-deploy-considerations.md)**

> [!TIP]
> Vous pouvez utiliser l’[outil de vérification des exigences de déploiement Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) après l’installation du système d’exploitation pour confirmer que votre matériel réponde à toutes les exigences.

## <a name="download-and-extract-the-deployment-package"></a>Téléchargez et extrayez le package de déploiement
Après vous être assuré que votre ordinateur hôte du kit de développement réponde aux exigences de base pour l’installation du kit de développement Azure Stack, il vous faut télécharger et extraire le package de déploiement du Kit de développement Azure Stack. Le package de déploiement contient le fichier Cloudbuilder.vhdx, un disque dur virtuel qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack.

Vous pouvez télécharger le package de déploiement sur l’hôte du Kit de développement ou sur un autre ordinateur. Une fois extraits, les fichiers de déploiement occupent jusqu’à 60 Go d’espace disque. L’utilisation d’un autre ordinateur peut vous permettre d’avoir une configuration matérielle moins exigeante pour l’hôte du Kit de développement.

**[Télécharger et extraire le Kit de développement Azure Stack (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du Kit de développement
Avant de pouvoir installer le Kit de développement Azure Stack sur l’ordinateur hôte, l’environnement doit être préparé et le système doit être configuré pour démarrer à partir du disque dur virtuel. Après cette étape, l’hôte du Kit de développement démarrera sur Cloudbuilder.vhdx (disque dur virtuel qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack).

Utilisez PowerShell pour configurer l’ordinateur hôte ASDK pour qu’il démarre à partir de CloudBuilder.vhdx. Ces commandes configurent votre ordinateur hôte ASDK pour qu’il démarre à partir du disque dur virtuel Azure Stack téléchargé et extrait (CloudBuilder.vhdx). Après avoir effectué ces étapes, redémarrez l’ordinateur hôte ASDK.

Pour configurer l’ordinateur hôte ASDK pour qu’il démarre à partir de CloudBuilder.vhdx :

  1. Lancez une invite de commandes en tant qu’administrateur.
  2. Exécutez `bcdedit /copy {current} /d "Azure Stack"`.
  3. Copiez (CTRL + C) la valeur CLSID renvoyée, y compris les caractères {} requis. Cette valeur correspond à {CLSID} et devra être collée (CTRL + V ou clic droit) dans les étapes restantes.
  4. Exécutez `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`. 
  5. Exécutez `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`. 
  6. Exécutez `bcdedit /set {CLSID} detecthal on`. 
  7. Exécutez `bcdedit /default {CLSID}`.
  8. Pour vérifier les paramètres de démarrage, exécutez `bcdedit`. 
  9. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (C:\CloudBuilder.vhdx) et redémarrez l’ordinateur hôte du kit de développement. Une fois redémarré, l’ordinateur hôte ASDK démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour que vous puissiez commencer le déploiement du Kit. 

> [!IMPORTANT]
> Assurez-vous d’avoir un accès physique direct ou un accès à KVM à l’ordinateur hôte du Kit de développement avant de le redémarrer. Au premier démarrage de l’ordinateur virtuel, vous êtes invité à exécuter le programme d’installation de Windows Server. Fournissez les mêmes informations d’identification d’administrateur que celles utilisées pour vous connecter à l’ordinateur hôte du Kit de développement. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Préparer l’hôte du kit de développement à l’aide de PowerShell 
Après le démarrage correct de l’ordinateur hôte du Kit de développement dans l’image CloudBuilder.vhdx, connectez-vous avec les mêmes informations d’identification d’administrateur local utilisées pour vous connecter à l’ordinateur hôte du Kit de développement (et que vous avez fournies dans le cadre de la finalisation de l’installation de Windows Server lorsque l’ordinateur hôte a démarré à partir du disque dur virtuel). 

> [!NOTE]
> Si vous voulez, vous pouvez aussi configurer les [Paramètres de télémétries Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *avant* l’installation du Kit de développement Azure Stack.

Ouvrez une console PowerShell avec élévation de privilèges et exécutez les commandes de cette section pour déployer l’ASDK sur l’hôte du Kit de développement.

> [!IMPORTANT] 
> L’installation d’ASDK prend en charge une seule carte réseau de mise en réseau. Si vous avez plusieurs cartes réseau, vérifiez qu’une seule d’entre elles est activée (et que toutes les autres sont désactivées) avant d’exécuter le script de déploiement.

Vous pouvez déployer Azure Stack avec Azure AD ou Windows Server AD FS comme fournisseur d’identité. Azure Stack, les fournisseurs de ressources et les autres applications fonctionnent de la même façon avec les deux.

> [!TIP]
> Si vous ne fournissez aucun paramètre de configuration (reportez-vous aux exemples et paramètres facultatifs InstallAzureStackPOC.ps1 ci-dessous), vous êtes invité à entrer les paramètres requis.

### <a name="deploy-azure-stack-using-azure-ad"></a>Déployer Azure Stack à l’aide d’Azure AD 
Pour déployer Azure Stack **à l’aide d’Azure AD comme fournisseur d’identité**, vous devez disposer d’une connectivité Internet directe ou via un proxy transparent. 

Exécutez les commandes PowerShell suivantes pour déployer le kit de développement à l’aide d’Azure AD :

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Après quelques minutes dans l’installation d’ASDK, vous serez invité à entrer des informations d’identification Azure AD. Vous devez fournir vos informations d’identification d’administrateur général d’Azure AD pour votre locataire Azure AD. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Déployer Azure Stack à l’aide d’AD FS 
Pour déployer le kit de développement **à l’aide d’AD FS comme fournisseur d’identité**, exécutez les commandes PowerShell suivantes (vous avez simplement besoin d’ajouter le paramètre -UseADFS) : 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Dans les déploiements AD FS, le service d’annuaire de marquage par défaut est utilisé comme fournisseur d’identité. Le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre des commandes de configuration PowerShell.

Le processus de déploiement peut prendre quelques heures, au cours desquelles le système ne redémarre automatiquement qu’une seule fois. Quand le déploiement est terminé, la console PowerShell affiche : **TERMINÉ : Action « Déploiement »**. Si le déploiement échoue, vous pouvez essayer d’exécuter le script à nouveau en utilisant le paramètre -rerun. Vous pouvez aussi [redéployer ASDK](asdk-redeploy.md) à partir de zéro.

> [!IMPORTANT]
> Si vous voulez surveiller la progression du déploiement, après le redémarrage de l’hôte ASDK, vous pouvez vous connecter en tant que AzureStack\AzureStackAdmin. Si vous vous connectez en tant qu’administrateur local une fois que l’ordinateur hôte est redémarré (et joint au domaine azurestack.local), vous ne verrez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack pour vérifier qu’il est en cours d’exécution.


#### <a name="azure-ad-deployment-script-examples"></a>Exemples de script de déploiement Azure AD
Vous pouvez écrire un script pour l’ensemble du déploiement Azure AD. Voici quelques exemples commentés qui incluent certains paramètres facultatifs.

Si votre identité Azure AD est associée uniquement à **un** répertoire Azure AD :
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Si votre identité Azure AD est associée à **plus d’un** répertoire Azure AD :
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Si votre environnement n’a pas de DHCP activé, vous devez inclure les paramètres supplémentaires suivants à l’une des options ci-dessus (exemple d’utilisation fourni) : 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Paramètres facultatifs InstallAzureStackPOC.ps1 ASDK
|Paramètre|Obligatoire ou facultatif|Description|
|-----|-----|-----|
|AdminPassword|Obligatoire|Définit le compte d’administrateur local et tous les autres comptes d’utilisateur sur toutes les machines virtuelles créées dans le cadre du déploiement du kit de développement. Ce mot de passe doit correspondre au mot de passe d’administrateur local actuel sur l’hôte.|
|InfraAzureDirectoryTenantName|Obligatoire|Définit le répertoire du locataire. Utilisez ce paramètre pour indiquer un répertoire spécifique où le compte AAD dispose des autorisations pour gérer plusieurs répertoires. Nom complet d’un locataire d’annuaire AAD au format .onmicrosoft.com ou d’un nom de domaine personnalisé vérifié Azure AD.|
|TimeServer|Obligatoire|Utilisez ce paramètre pour spécifier un serveur de temps spécifique. Ce paramètre doit être fourni sous la forme d’une adresse IP de serveur temps valide. Les noms de serveur ne sont pas pris en charge.|
|InfraAzureDirectoryTenantAdminCredential|Facultatif|Définit le nom d’utilisateur et le mot de passe Azure Active Directory. Ces informations d’identification Azure doivent être un ID org.|
|InfraAzureEnvironment|Facultatif|Sélectionnez l’environnement Azure avec lequel vous souhaitez enregistrer ce déploiement Azure Stack. Les options incluent Azure public, Azure - Chine, Azure - Gouvernement des États-Unis.|
|DNSForwarder|Facultatif|Un serveur DNS est créé dans le cadre du déploiement Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.|
|NatIPv4Address|Requis pour la prise en charge NAT DHCP|Définit une adresse IP statique pour MAS-BGPNAT01. Utilisez ce paramètre uniquement si le protocole DHCP ne peut pas attribuer une adresse IP valide pour accéder à Internet.|
|NatIPv4Subnet|Requis pour la prise en charge NAT DHCP|Préfixe de sous-réseau IP utilisé pour la prise en charge de DHCP sur NAT. Utilisez ce paramètre uniquement si le protocole DHCP ne peut pas attribuer une adresse IP valide pour accéder à Internet.|
|PublicVlanId|Facultatif|Définit l’ID du réseau local virtuel. Utilisez ce paramètre uniquement si l’hôte et MAS-BGPNAT01 doivent configurer l’ID du réseau local virtuel pour accéder au réseau physique (et à Internet). Par exemple,.\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Rerun|Facultatif|Utilisez cet indicateur pour réexécuter le déploiement. Toutes les entrées précédentes sont utilisées. Une nouvelle saisie des données précédemment fournies n’est pas prise en charge, car plusieurs valeurs uniques sont générées et utilisées pour le déploiement.|


## <a name="perform-post-deployment-configurations"></a>Effectuer des configurations post-déploiement
Après avoir installé le Kit de développement Azure Stack, nous vous recommandons d’effectuer quelques vérifications post-installation et des modifications de configuration. Vous pouvez valider votre installation à l’aide de la cmdlet test-AzureStack, et installer les outils Azure Stack PowerShell et GitHub. 

Vous devez aussi réinitialiser la stratégie d’expiration du mot de passe pour faire en sorte que le mot de passe de l’hôte du Kit de développement n’expire pas avant la fin de la période d’expiration.

> [!NOTE]
> Si vous voulez, vous pouvez aussi configurer les [Paramètres de télémétries Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *après* avoir installé le Kit de développement Azure Stack.

**[Tâches post-déploiement du Kit de développement Azure Stack](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Inscription auprès d’Azure
Vous devez inscrire Azure Stack auprès d’Azure pour pouvoir ensuite [télécharger des éléments marketplace Azure](asdk-marketplace-item.md) dans Azure Stack.

**[Inscrire Azure Stack auprès d’Azure](asdk-register.md)**

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Après toutes ces étapes, l’environnement pour le Kit de développement est prêt, avec les portails de l’[administrateur](https://adminportal.local.azurestack.external) et de l’[utilisateur](https://portal.local.azurestack.external). 

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Téléchargez et extrayez le package de déploiement
> * Préparer l’ordinateur hôte du Kit de développement 
> * Effectuer des configurations post-déploiement
> * Inscription auprès d’Azure

Passez au didacticiel suivant pour apprendre à ajouter un élément marketplace Azure Stack.

> [!div class="nextstepaction"]
> [Ajouter un élément marketplace Azure Stack](asdk-marketplace-item.md)

