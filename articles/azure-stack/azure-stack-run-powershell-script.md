---
title: "Déployer le Kit de développement Azure Stack | Microsoft Docs"
description: "Découvrez comment préparer le Kit de développement Azure Stack et exécuter le script PowerShell pour le déployer."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6a5912117a475c7af028f01ea47a7042677992ca
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-azure-stack-development-kit"></a>Déployer le Kit de développement Azure Stack

*S’applique au Kit de développement Azure Stack*

Pour déployer le [Kit de développement Azure Stack](azure-stack-poc.md), vous devez effectuer les étapes suivantes :

1. [Téléchargez le package de déploiement](https://azure.microsoft.com/overview/azure-stack/try/?v=try) pour obtenir le fichier Cloudbuilder.vhdx.
2. Préparez le fichier cloudbuilder.vhdx pour configurer l’ordinateur (l’hôte du kit de développement) sur lequel vous voulez installer le kit de développement. Après cette étape, l’hôte du Kit de développement démarre à partir du fichier Cloudbuilder.vhdx.
3. Déployez le kit de développement sur l’hôte du kit de développement.

> [!NOTE]
> Pour un résultat optimal, même si vous voulez utiliser un environnement Azure Stack déconnecté, il est préférable de déployer en étant connecté à Internet. De cette façon, la version d’évaluation de Windows Server 2016 incluse avec l’installation du kit de développement peut être activée au moment du déploiement.

## <a name="download-and-extract-the-development-kit"></a>Télécharger et extraire le Kit de développement
1. Avant de commencer le téléchargement, vérifiez que votre ordinateur répond aux prérequis suivants :

  - L’ordinateur doit avoir au moins 60 Go d’espace disque disponible sur quatre disques durs logiques identiques et distincts en plus du disque de système d’exploitation.
  - [.NET Framework 4.6 (ou ultérieur)](https://aka.ms/r6mkiy) doit être installé.

2. [Accédez à la page de démarrage](https://azure.microsoft.com/overview/azure-stack/try/?v=try) sur laquelle vous pouvez télécharger le Kit de développement Azure Stack, renseignez vos informations, puis cliquez sur **Envoyer**.
3. Téléchargez et exécutez le script de vérification des conditions requises du [vérificateur de déploiement pour le Kit de développement Azure Stack](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409). Ce script autonome passe en revue les vérifications des conditions requises réalisées par la configuration pour le Kit de développement Azure Stack. Il vous permet de vous assurer que vous respectez les exigences matérielles et logicielles avant de télécharger le package plus volumineux du Kit de développement Azure Stack.
4. Sous **Télécharger le logiciel**, cliquez sur **Kit de développement Azure Stack**.

  > [!NOTE]
  > Le téléchargement d’ASDK (AzureStackDevelopmentKit.exe) représente 10 Go à lui seul. Si vous choisissez de télécharger le fichier ISO de la version d’évaluation de Windows Server 2016, la taille du téléchargement peut atteindre environ 17 Go. Vous pouvez utiliser ce fichier ISO pour créer et ajouter une image de machine virtuelle Windows Server 2016 à la Place de marché Azure Stack une fois l’installation d’ASDK terminée. Notez que cette image d’évaluation Windows Server 2016 peut uniquement être utilisée avec l’ASDK et qu’elle est soumise aux termes du contrat de licence d’ASDK.

5. Une fois le téléchargement terminé, cliquez sur **Exécuter** pour lancer l’auto-extracteur ASDK (AzureStackDevelopmentKit.exe).
6. Lisez et acceptez le contrat de licence de la page **Contrat de licence** de l’Assistant de l’auto-extracteur, plus cliquez sur **Suivant**.
7. Passez en revue la déclaration de confidentialité de la page **Avis important** de l’Assistant de l’auto-extracteur, puis cliquez sur **Suivant**.
8. Sélectionnez l’emplacement où les fichiers de configuration Azure Stack doivent être extraits sur la page **Sélectionner l’emplacement de destination** de l’Assistant de l’auto-extracteur, puis cliquez sur **Suivant**. L’emplacement par défaut est *dossier actuel*\Kit de développement Azure Stack. 
9. Passez en revue le résumé de l’emplacement de la page **Ready to Extract** (Prêt pour l’extraction) de l’Assistant de l’auto-extracteur, puis cliquez sur **Extraire** pour extraire les fichiers CloudBuilder.vhdx (environ 25 Go) et ThirdPartyLicenses.rtf. Ce processus prend un certain temps.
10. Copiez ou déplacez le fichier CloudBuilder.vhdx à la racine du lecteur C:\ (C:\CloudBuilder.vhdx) sur l’ordinateur hôte ASDK.

> [!NOTE]
> Après avoir extrait les fichiers, vous pouvez supprimer les fichiers .EXE et .BIN pour récupérer de l’espace sur le disque dur. Vous pouvez aussi sauvegarder ces fichiers pour ne pas avoir à les télécharger de nouveau en cas de redéploiement de l’ASDK.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Déployer l’ASDK à l’aide d’une expérience d’interface graphique utilisateur
L’ASDK peut être déployé à l’aide d’une interface graphique utilisateur (GUI) fournie en téléchargeant et exécutant le script PowerShell asdk-installer.ps1.

> [!NOTE]
> L’interface utilisateur du programme d’installation du Kit de développement Azure Stack est un script open source basé sur WCF et PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Préparer l’hôte du kit de développement à l’aide d’une expérience d’interface graphique utilisateur
Avant de pouvoir installer l’ASDK sur l’ordinateur hôte, l’environnement ASDK doit être préparé.
1. Connectez-vous en tant qu’administrateur local à votre ordinateur hôte ASDK.
2. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (C:\CloudBuilder.vhdx).
3. Exécutez le script suivant pour télécharger le fichier du programme d’installation du kit de développement (asdk-installer.ps1) du [répertoire des outils GitHub Azure Stack](https://github.com/Azure/AzureStack-Tools) au dossier **C:\AzureStack_Installer** de votre ordinateur hôte du kit de développement :

  ```powershell
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Depuis une console PowerShell avec élévation de privilèges, lancez le script **C:\AzureStack_Installer\asdk-installer.ps1**, puis cliquez sur **Préparer l’environnement**.
5. Sur la page **Sélectionner le fichier vhdx Cloudbuilder** du programme d’installation, recherchez et sélectionnez le fichier **cloudbuilder.vhdx** que vous avez téléchargé et extrait dans les étapes précédentes. Sur cette page, vous pouvez également (si vous le souhaitez) cocher la case **Ajouter des pilotes** si vous avez besoin de pilotes supplémentaires sur l’ordinateur hôte du kit de développement.  
6. Sur la page **Paramètres facultatifs**, spécifiez un compte d’administrateur local pour l’ordinateur hôte du kit de développement. 

  > [!IMPORTANT]
  > Si vous ne fournissez pas ces informations d’identification, vous aurez besoin d’un accès KVM ou direct à l’hôte après le redémarrage de l’ordinateur dans le cadre de la procédure de configuration du kit de développement.

7. Vous pouvez également renseigner ces paramètres facultatifs sur la page **Paramètres facultatifs** :
    - **Nom de l’ordinateur** : cette option définit le nom de l’hôte du Kit de développement. Le nom doit respecter les spécifications des noms de domaine complets et ne pas dépasser 15 caractères. La valeur par défaut est un nom d’ordinateur aléatoire généré par Windows.
    - **Fuseau horaire** : définit le fuseau horaire pour l’hôte du Kit de développement. La valeur par défaut est (UTC-8:00) Heure du Pacifique (États-Unis et Canada).
    - **Configuration IP statique** : indique que votre déploiement doit utiliser une adresse IP statique. Dans le cas contraire, quand le programme d’installation redémarre dans cloudbuilder.vhx, les interfaces réseau sont configurées avec DHCP.
11. Cliquez sur **Suivant**.
12. Si vous avez choisi une configuration IP statique à l’étape précédente, vous devez maintenant :
    - Sélectionner une carte réseau. Vérifiez que vous pouvez vous connecter à la carte avant de cliquer sur **Suivant**.
    - Vérifiez que les valeurs pour **Adresse IP**, **Passerelle** et **DNS** sont correctes, puis cliquez sur **Suivant**.
13. Cliquez sur **Suivant** pour démarrer le processus de préparation.
14. Quand la préparation indique **Terminé**, cliquez sur **Suivant**.
15. Cliquez sur **Redémarrer maintenant** pour démarrer dans cloudbuilder.vhdx et continuer le processus de déploiement.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Déployer le kit de développement à l’aide d’une expérience d’interface graphique utilisateur
Après avoir préparé l’ordinateur hôte ASDK, l’ASDK peut être déployé dans l’image CloudBuilder.vhdx en suivant les étapes ci-après. 
1. Une fois que l’ordinateur hôte a correctement démarré dans l’image CloudBuilder.vhdx, connectez-vous avec les informations d’identification d’administrateur indiquées aux étapes précédentes. 
2. Ouvrez une console PowerShell avec élévation de privilèges et exécutez le script **\AzureStack_Installer\asdk-installer.ps1** (qui peut maintenant être sur un lecteur différent dans l’image Cloudbuilder.vhdx). Cliquez sur **Installer**.
3. Dans la zone déroulante **Type**, sélectionnez **Cloud Azure** ou **AD FS**.
    - **Cloud Azure** : configure Azure Active Directory (Azure AD) comme fournisseur d’identité. Pour utiliser cette option, vous aurez besoin d’une connexion Internet, du nom complet d’un locataire d’annuaire Azure AD au format *nomdomaine*.onmicrosoft.com ou d’un nom de domaine personnalisé vérifié Azure AD et des informations d’identification d’administrateur général de l’annuaire spécifié. 
    - **AD FS** : le service d’annuaire de marquage par défaut sera utilisé comme fournisseur d’identité. Le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre de la configuration.
4. Sous **Mot de passe de l’administrateur local**, dans la zone **Mot de passe**, tapez le mot de passe de l’administrateur local (qui doit correspondre au mot de passe de l’administrateur local actuellement configuré), puis cliquez sur **Suivant**.
5. Sélectionnez une carte réseau à utiliser pour le Kit de développement, puis cliquez sur **Suivant**.
6. Sélectionnez une configuration réseau DHCP ou statique pour la machine virtuelle BGPNAT01.
    - **DHCP** (par défaut) : la machine virtuelle obtient la configuration réseau IP auprès du serveur DHCP.
    - **Statique** : utilisez cette option seulement si DHCP ne peut pas affecter une adresse IP valide pour l’accès Internet d’Azure Stack. Une adresse IP statique doit être spécifiée avec la longueur du masque de sous-réseau au format CIDR (par exemple 10.0.0.5/24).
7. Si vous le souhaitez, vous pouvez aussi définir les valeurs suivantes :
    - **ID de VLAN** : définit l’ID du réseau local virtuel. Utilisez cette option seulement si l’hôte et AzS-BGPNAT01 doivent configurer l’ID du réseau local virtuel pour accéder au réseau physique (et à Internet). 
    - **Redirecteur DNS** : un serveur DNS est créé dans le cadre du déploiement d’Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.
    - **Serveur de temps** : ce champ requis définit le serveur de temps à utiliser par le kit de développement. Ce paramètre doit être fourni sous la forme d’une adresse IP de serveur temps valide. Les noms de serveur ne sont pas pris en charge.
  
  > [!TIP]
  > Pour rechercher l’adresse IP d’un serveur de temps, visitez [pool.ntp.org](http:\\pool.ntp.org) ou effectuez un test ping time.windows.com. 
  
8. Cliquez sur **Suivant**. 
9. Dans la page **Vérification des propriétés de la carte d’interface réseau**, vous voyez une barre de progression. 
    - Si elle indique **Impossible de télécharger une mise à jour**, suivez les instructions de la page.
    - Quand elle indique **Terminé**, cliquez sur **Suivant**.
10. Dans la page **Récapitulatif**, cliquez sur **Déployer**. Ici, vous pouvez également copier les commandes de configuration PowerShell qui seront utilisées pour installer le kit de développement.
11. Si vous utilisez un déploiement Azure AD, vous serez invité à entrer vos informations d’identification du compte administrateur général quelques minutes après le lancement de la configuration.
12. Le processus de déploiement peut prendre quelques heures, au cours desquelles le système ne redémarre automatiquement qu’une seule fois. Quand le déploiement est terminé, la console PowerShell affiche : **TERMINÉ : Action « Déploiement »**. Si le déploiement échoue, vous pouvez en [faire un nouveau](azure-stack-redeploy.md) à partir de zéro ou utiliser les commandes PowerShell suivantes, depuis la même fenêtre PowerShell avec des privilèges élevés, pour redémarrer le déploiement à partir de la dernière étape réussie :

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Si vous voulez surveiller la progression du déploiement, connectez-vous en tant que azurestack\AzureStackAdmin lorsque l’hôte du kit de développement redémarre pendant la configuration. Si vous vous connectez en tant qu’administrateur local une fois que la machine est jointe au domaine, vous ne voyez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack\AzureStackAdmin pour vérifier qu’il est en cours d’exécution.
   

## <a name="deploy-the-asdk-using-powershell"></a>Déployer l’ASDK à l’aide de PowerShell
Les étapes précédentes vous ont présenté le déploiement de l’ASDK à l’aide d’une expérience d’interface graphique utilisateur. Vous pouvez aussi utiliser PowerShell pour déployer l’ASDK sur l’hôte de kit de développement en suivant les étapes décrites dans cette section.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Configurer l’ordinateur hôte ASDK pour démarrer à partir de CloudBuilder.vhdx
Ces commandes configureront votre ordinateur hôte ASDK pour qu’il démarre à partir du disque dur virtuel Azure Stack téléchargé et extrait (CloudBuilder.vhdx). Après avoir effectué ces étapes, redémarrez l’ordinateur hôte ASDK.

1. Lancez une invite de commandes en tant qu’administrateur.
2. Exécutez `bcdedit /copy {current} /d "Azure Stack"`.
3. Copiez (CTRL + C) la valeur CLSID renvoyée, y compris les caractères {} requis. Cette valeur correspond à {CLSID} et devra être collée (CTRL + V ou clic droit) dans les étapes restantes.
4. Exécutez `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`. 
5. Exécutez `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`. 
6. Exécutez `bcdedit /set {CLSID} detecthal on`. 
7. Exécutez `bcdedit /default {CLSID}`.
8. Pour vérifier les paramètres de démarrage, exécutez `bcdedit`. 
9. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (C:\CloudBuilder.vhdx) et redémarrez l’ordinateur hôte du kit de développement. Lors du redémarrage de l’hôte ASDK, par défaut, il doit désormais démarrer à partir de la machine virtuelle CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Préparer l’hôte du kit de développement à l’aide de PowerShell 
Une fois que l’ordinateur hôte du kit développement démarre correctement dans l’image CloudBuilder.vhdx, vous pouvez ouvrir une console PowerShell avec élévation de privilèges et exécutez les commandes de cette section pour déployer l’ASDK sur l’hôte du kit de développement.

> [!IMPORTANT] 
> L’installation d’ASDK prend en charge une seule carte réseau de mise en réseau. Si vous avez plusieurs cartes réseau, vérifiez qu’une seule d’entre elles est activée (et que toutes les autres sont désactivées) avant d’exécuter le script de déploiement.

Vous pouvez déployer Azure Stack avec Azure AD ou AD FS comme fournisseur d’identité. Azure Stack, les fournisseurs de ressources et les autres applications fonctionnent de la même façon avec les deux. Pour en savoir plus sur les éléments pris en charge avec AD FS dans Azure Stack, consultez l’article [Fonctionnalités et concepts clés de Azure Stack](.\azure-stack-key-features.md).

> [!TIP]
> Si vous ne fournissez aucun paramètre de configuration (reportez-vous aux exemples et paramètres facultatifs InstallAzureStackPOC.ps1 ci-dessous), vous serez invité à entrer les paramètres requis.

### <a name="deploy-azure-stack-using-azure-ad"></a>Déployer Azure Stack à l’aide d’Azure AD 
Pour déployer Azure Stack **à l’aide d’Azure AD comme fournisseur d’identité**, vous devez disposer d’une connectivité Internet directe ou via un proxy transparent. Exécutez les commandes PowerShell suivantes pour déployer le kit de développement à l’aide d’Azure AD :

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

Le processus de déploiement peut prendre quelques heures, au cours desquelles le système ne redémarre automatiquement qu’une seule fois. Quand le déploiement est terminé, la console PowerShell affiche : **TERMINÉ : Action « Déploiement »**. Si le déploiement échoue, vous pouvez essayer d’exécuter le script à nouveau en utilisant le paramètre -rerun. Vous pouvez aussi [redéployer ASDK](.\azure-stack-redeploy.md) à partir de zéro.
> [!IMPORTANT]
> Si vous voulez surveiller la progression du déploiement, après le redémarrage de l’hôte ASDK, vous pouvez vous connecter en tant que AzureStack\AzureStackAdmin. Si vous vous connectez en tant qu’administrateur local une fois que l’ordinateur hôte est redémarré (et joint au domaine azurestack.local), vous ne verrez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack pour vérifier qu’il est en cours d’exécution.
>

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

Si votre environnement **n’a pas** de DHCP activé, vous devez inclure les paramètres supplémentaires suivants à l’une des options ci-dessus (exemple d’utilisation fourni) : 

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

## <a name="activate-the-administrator-and-tenant-portals"></a>Activer les portails d’administrateur et de locataire
Après les déploiements qui utilisent Azure AD, vous devez activer les portails d’administrateur et de locataire Azure Stack. Cette activation accepte de donner au portail Azure Stack et à Azure Resource Manager les autorisations appropriées (répertoriées sur la page de consentement) pour tous les utilisateurs du répertoire.

- Pour le portail d’administrateur, accédez à https://adminportal.local.azurestack.external/guest/signup, lisez les informations, puis cliquez sur **Accepter**. Après avoir accepté, vous pouvez ajouter des administrateurs de service qui ne sont pas également des administrateurs de locataire de répertoire.

- Pour le portail de locataire, accédez à https://portal.local.azurestack.external/guest/signup, lisez les informations, puis cliquez sur **Accepter**. Après avoir accepté, les utilisateurs dans le répertoire peuvent se connecter au portail de locataire. 

> [!NOTE] 
> Si les portails ne sont pas activés, seul l’administrateur de répertoire peut se connecter et utiliser les portails. Si un autre utilisateur se connecte, il verra une erreur lui indiquant que l’administrateur n’a pas accordé d’autorisations à d’autres utilisateurs. Lorsque l’administrateur n’appartient pas, en mode natif, au répertoire Azure Stack dans lequel il est enregistré, le répertoire Azure Stack doit être ajouté à l’URL d’activation. Par exemple, si Azure Stack est enregistré sur fabrikam.onmicrosoft.com et si l’utilisateur administrateur est admin@contoso.com, accédez à https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com pour activer le portail. 

## <a name="reset-the-password-expiration-policy"></a>Réinitialiser la stratégie d’expiration du mot de passe 
Pour faire en sorte que le mot de passe de l’hôte du kit de développement n’expire pas avant la fin de la période d’expiration, suivez ces étapes après avoir déployé l’ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Pour modifier la stratégie d’expiration de mot de passe à partir de Powershell :
À partir d’une console Powershell avec élévation de privilèges, exécutez la commande :

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Pour modifier la stratégie d’expiration de mot de passe manuellement :
1. Sur l’hôte du Kit de développement, ouvrez **Gestion des stratégies de groupe** et accédez à **Gestion des stratégies de groupe** – **Forêt : azurestack.local** – **Domaines** – **azurestack.local**.
2. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
3. Dans l’Éditeur de gestion de stratégie de groupe, accédez à **Configuration de l’ordinateur** – **Stratégies** – **Paramètres Windows** – **Paramètres de sécurité**– **Stratégies de comptes** – **Stratégie de mot de passe**.
4. Dans le volet droit, double-cliquez sur **Antériorité maximale du mot de passe**.
5. Dans la boîte de dialogue **Antériorité maximale du mot de passe - Propriétés**, remplacez la valeur de **Le mot de passe expirera dans** par 180, puis cliquez sur **OK**.


## <a name="next-steps"></a>Étapes suivantes

[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)

[Devenez rapidement opérationnel avec PowerShell dans Azure Stack.](azure-stack-powershell-configure-quickstart.md)

[Inscrire Azure Stack auprès de votre abonnement Azure](azure-stack-register.md)



