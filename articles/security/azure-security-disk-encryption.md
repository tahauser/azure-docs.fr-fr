---
title: Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux | Microsoft Docs
description: "Cet article offre une vue d’ensemble de Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux."
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: cc609d7c7b28fc4aef6eb1e25ee46fd77edd4102
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS
Microsoft Azure s’engage fermement à préserver la confidentialité, la souveraineté de vos données et vous permet de contrôler vos données Azure hébergées via une suite de technologies servant à chiffrer, contrôler et gérer les clés de chiffrement, le contrôle et l’audit de l’accès aux données. Les clients Azure ont ainsi la possibilité de choisir la solution qui répond le mieux à leurs besoins professionnels. Dans ce document, nous allons vous présenter une nouvelle solution technologique « Azure Disk Encryption for Windows and Linux IaaS VM’s » pour protéger et sauvegarder vos données afin de répondre aux engagements de votre sécurité en matière d’organisation et les exigences de conformité. Cet article fournit des instructions détaillées sur la façon d’utiliser les fonctionnalités de cryptage de disque Azure, notamment sur les scénarios pris en charge et sur les expériences utilisateur.

> [!NOTE]
> Certaines recommandations peuvent entraîner une augmentation de l’utilisation des données, des réseaux ou des ressources de calcul débouchant sur des coûts de licence ou d’abonnement supplémentaires.

## <a name="overview"></a>Vue d'ensemble
Azure Disk Encryption est une nouvelle fonctionnalité qui vous permet de chiffrer vos disques de machine virtuelle IaaS Windows et Linux. Azure Disk Encryption s’appuie sur la fonctionnalité standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux afin de fournir le chiffrement de volume pour le système d’exploitation et les disques de données. La solution est intégrée avec [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/), ce qui vous permet de contrôler et de gérer les clés et clés secrètes de chiffrement de disque dans votre abonnement Key Vault. Elle garantit également que toutes les données sur les disques de vos machines virtuelles sont chiffrées au repos dans votre stockage Azure.

Azure Disk Encryption pour les machines virtuelles Iaas Windows et Linux est désormais à la **disponibilité générale** dans toutes les régions publiques Azure et les régions AzureGov pour les machines virtuelles standard et les machines virtuelles avec Stockage Premium.

### <a name="encryption-scenarios"></a>Scénarios de chiffrement
Azure Disk Encryption prend en charge les scénarios client suivants :

* Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels déjà chiffrés et de clés de chiffrement
* Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir d’images de la galerie Azure prises en charge
* Activation du chiffrement sur des machines virtuelles IaaS existantes et fonctionnant dans Azure
* Désactivation du chiffrement sur les machines virtuelles IaaS Windows.
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux
* Activation du chiffrement des machines virtuelles avec disque managé
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de stockage non Premium et Premium chiffrée existante
* Sauvegarde et restauration de machines virtuelles chiffrées

La solution prend en charge les scénarios de machines virtuelles IaaS suivants lorsqu’ils sont activés dans Microsoft Azure :

* Prise en main d’Azure Key Vault
* Machines virtuelles de niveau standard : [Machines virtuelles IaaS des séries A, D, DS, G, GS, F, etc.](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Activation du chiffrement sur les machines virtuelles IaaS Windows et Linux et les machines virtuelles de disques gérés créées à partir d’images de la galerie Azure prises en charge
* Désactivation du chiffrement des lecteurs de système d’exploitation et de données pour des machines virtuelles IaaS Windows et des machines virtuelles avec disque managé
* Désactivation du chiffrement des lecteurs de données pour des machines virtuelles IaaS Linux et des machines virtuelles avec disque managé
* Activation du chiffrement sur les machines virtuelles IaaS exécutant le système d’exploitation du client Windows.
* Activation du chiffrement sur les volumes avec chemins d’accès de montage.
* Activation du chiffrement sur des machines virtuelles Linux configurées avec une agrégation de disques (RAID) utilisant mdadm
* Activation du chiffrement sur des machines virtuelles Linux en utilisant LVM pour les disques de données
* Activer le chiffrement sur Linux LVM 7.3 pour le système d’exploitation et les disques de données 
* Activation du chiffrement sur les machines virtuelles Windows configurées avec des espaces de stockage
* Mise à jour des paramètres de chiffrement d’une machine virtuelle de stockage non Premium et Premium chiffrée existante
* Sauvegarde et restauration de machines virtuelles chiffrées, pour les scénarios KEK et non-KEK (KEK - clé de chiffrement de clé)
* Toutes les régions publiques Azure et AzureGov sont prises en charge

La solution ne prend pas en charge les scénarios, fonctionnalités et technologies suivants :

* Machines virtuelles IaaS de niveau de base
* Désactivation du chiffrement sur un lecteur de système d’exploitation pour les machines virtuelles IaaS Linux
* Désactivation du chiffrement sur un lecteur de données lorsque le lecteur du système d’exploitation est chiffré pour les machines virtuelles Iaas Linux
* Machines virtuelles IaaS créées à l’aide de la méthode classique de création de machines virtuelles
* L’activation du chiffrement sur les images client personnalisées de machines virtuelles Iaas Windows et Linux n’est PAS prise en charge.
* Intégration à votre service de gestion de clés local
* Azure Files (système de partage de fichiers), NFS (Network File System), volumes dynamiques et machines virtuelles Windows configurées avec des systèmes RAID logiciels

### <a name="encryption-features"></a>Fonctionnalités de chiffrement
Lorsque vous activez et déployez Azure Disk Encryption pour les machines virtuelles IaaS Azure, les fonctionnalités suivantes sont activées en fonction de la configuration fournie :

* Chiffrement du volume du système d’exploitation pour protéger le volume de démarrage au repos dans votre espace de stockage
* Chiffrement des volumes de données pour protéger les volumes de données au repos dans votre espace de stockage
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles IaaS Windows
* Désactivation du chiffrement sur les lecteurs de données pour les machines virtuelles IaaS Linux (à condition que le lecteur de système d’exploitation NE SOIT PAS chiffré)
* Sauvegarde des clés et clés secrètes de chiffrement dans votre abonnement Key Vault
* Création de rapports concernant l’état du chiffrement des machines virtuelles IaaS chiffrées
* Suppression des paramètres de configuration de chiffrement de disque à partir de la machine virtuelle IaaS
* Sauvegarde et restauration des machines virtuelles chiffrées à l’aide du service Sauvegarde Azure

La solution Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux comprend :

* L’extension de chiffrement de disque pour Windows.
* L’extension de chiffrement de disque pour Linux.
* Les applets de commande PowerShell pour le chiffrement de disque.
* Les applets de commande de l’interface de ligne de commande Azure pour le chiffrement de disque.
* Les modèles Azure Resource Manager pour le chiffrement de disque.

La solution Azure Disk Encryption est prise en charge sur les machines virtuelles IaaS exécutant les systèmes d’exploitation Windows ou Linux. Pour plus d’informations sur les systèmes d’exploitation pris en charge, consultez la section « Conditions préalables ».

> [!NOTE]
> Le chiffrement des disques de machine virtuelle avec Azure Disk Encryption est gratuit.

### <a name="value-proposition"></a>Proposition de valeur
La solution de gestion Azure Disk Encryption répond aux besoins professionnels suivants :

* Les machines virtuelles IaaS sont sécurisées au repos par le biais d’une technologie de chiffrement standard permettant de répondre aux exigences de sécurité organisationnelle et de conformité.
* Les machines virtuelles IaaS démarrent par le biais de stratégies et de clés contrôlées par les clients qui peuvent auditer leur utilisation dans le coffre de clés.

### <a name="encryption-workflow"></a>Workflow de chiffrement
Pour activer le chiffrement de disque pour les machines virtuelles Windows et Linux, procédez comme suit :

1. Choisissez un scénario de chiffrement parmi les scénarios de chiffrement précédents.
2. Optez pour l’activation du chiffrement de disque via le modèle Resource Manager de chiffrement de disque Azure, les applets de commande PowerShell ou les commandes CLI et spécifiez la configuration de chiffrement.

   * Pour le scénario de disque dur virtuel chiffré par le client, téléchargez le disque dur virtuel chiffré dans votre compte de stockage et le support de clé de chiffrement dans votre coffre de clés. Ensuite, fournissez la configuration de chiffrement pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.
   * Pour les nouvelles machines virtuelles créées à partir de la Place de marché Azure et les machines virtuelles existantes en cours d’exécution dans Azure, fournissez la configuration de chiffrement pour activer le chiffrement sur la machine virtuelle IaaS.

3. Accordez l’accès à la plateforme Azure pour lire le support de clé de chiffrement (systèmes de clés de chiffrement BitLocker pour les systèmes Windows et phrase secrète pour Linux) du coffre de clés et activez le chiffrement sur la machine virtuelle IaaS.

4. Fournissez l’identité d’application Azure Active Directory (Azure AD) pour écrire le support de clé de chiffrement dans votre coffre de clés. Cette opération active le chiffrement sur la machine virtuelle IaaS pour les scénarios mentionnés à l’étape 2.

5. Azure met à jour le modèle de service de machine virtuelle avec chiffrement et la configuration de coffre de clés, et configure vos machines virtuelles chiffrées.

 ![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Workflow de déchiffrement
Pour désactiver le chiffrement de disque pour les machines virtuelles IaaS, suivez les étapes de haut niveau suivantes :

1. Choisissez de désactiver le chiffrement (déchiffrement) sur une machine virtuelle IaaS en cours d’exécution dans Azure via le modèle Resource Manager Azure Disk Encryption ou via les applets de commande PowerShell, puis spécifiez la configuration de déchiffrement.

 Cette étape désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Toutefois, comme mentionné dans la section précédente, la désactivation du chiffrement du disque de système d’exploitation pour Linux n’est pas prise en charge. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux tant que le disque du système d’exploitation n’est pas chiffré.
2. Azure met à jour le modèle de service de machine virtuelle et la machine virtuelle Iaas est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos.

> [!NOTE]
> La désactivation du chiffrement ne supprime ni votre coffre de clés ni le support de clé de chiffrement (clés de chiffrement BitLocker pour Windows ou phrase secrète pour Linux).
 > La désactivation du chiffrement de disque de système d’exploitation pour Linux n’est pas prise en charge. L’étape de déchiffrement est autorisée uniquement pour les lecteurs de données sur les machines virtuelles Linux.
La désactivation du chiffrement des disques de données pour Linux n’est pas prise en charge si le lecteur de système d’exploitation est chiffré.

## <a name="prerequisites"></a>Prérequis
Voici les conditions requises pour activer Azure Disk Encryption sur les machines virtuelles IaaS Azure pour les scénarios pris en charge dans la section « Vue d’ensemble » :

* Vous devez disposer d’un abonnement Azure actif valide pour créer des ressources dans Azure dans les régions prises en charge.
* Azure Disk Encryption est pris en charge sur les versions Windows Server suivantes : Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016.
* Azure Disk Encryption est pris en charge sur les clients Windows suivants : client Windows 8 et client Windows 10.

> [!NOTE]
> Pour Windows Server 2008 R2, .NET Framework 4.5 doit être installé avant l’activation du chiffrement dans Azure. Vous pouvez l’installer à partir de Windows Update en installant la mise à jour facultative Microsoft .NET Framework 4.5.2 pour systèmes Windows Server 2008 R2 x64 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Azure Disk Encryption est pris en charge uniquement sur les versions et les distributions de serveur Linux basées sur Azure Gallery.  Pour obtenir la liste des versions actuellement prises en charge, reportez-vous à la [FAQ Azure Disk Encryption](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-faq).

* Azure Disk Encryption requiert que votre coffre de clés et vos machines virtuelles se trouvent dans la même région et le même abonnement Azure.

> [!NOTE]
> La configuration des ressources dans des régions distinctes provoque l’échec de l’activation de la fonctionnalité Azure Disk Encryption.

* Pour créer et configurer votre coffre de clés pour Azure Disk Encryption, voir la section **Créer et configurer votre coffre de clés pour Azure Disk Encryption** dans la rubrique *Conditions préalables* de cet article.
* Pour créer et configurer l’application Azure AD dans Azure Active Directory pour Azure Disk Encryption, voir la section **Configurer l’application Azure AD dans Azure Active Directory** de la rubrique *Conditions préalables* dans cet article.
* Pour créer et configurer la stratégie d’accès au coffre de clés pour l’application Azure AD, voir la section **Configurer la stratégie d’accès au coffre de clés pour l’application Azure AD** de la rubrique *Conditions préalables* dans cet article.
* Pour préparer un disque dur virtuel Windows pré-chiffré, voir la section **Préparer un disque dur virtuel Windows pré-chiffré** dans l’*Annexe*.
* Pour préparer un disque dur virtuel Linux pré-chiffré, voir la section **réparer un disque dur virtuel Linux pré-chiffré** dans l’*Annexe*.
* La plateforme Azure doit avoir accès aux clés ou aux clés secrètes de chiffrement dans votre coffre de clés afin de les mettre à disposition de la machine virtuelle au moment de lancer et de déchiffrer son volume de système d’exploitation. Pour accorder des autorisations pour la plateforme Azure, définissez la propriété **EnabledForDiskEncryption** dans le coffre de clés. Pour plus d’informations, voir la section **Créer et configurer votre coffre de clés pour Azure Disk Encryption** dans l’Annexe.
* Les URL de clé secrète de coffre de clés et de clé de chiffrement à clé (KEK) doivent être des versions gérées. Azure met en vigueur cette restriction de gestion de version. Voici des exemples d’URL de clé secrète et de clé de chiffrement à clé valides :

  * Exemple d’URL secrète valide : *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemple d’URL de clé de chiffrement à clé valide : *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption ne prend pas en charge l’intégration de numéros de port aux clés secrètes de coffre de clés et aux URL KEK. Voici des exemples d’URL de coffre de clés valides et non valides :

  * URL de coffre de clés non acceptée : *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL de coffre de clés acceptée : *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Pour activer la fonctionnalité Azure Disk Encryption, les machines virtuelles IaaS doivent répondre aux exigences de configuration du point de terminaison de réseau suivantes :
  * Pour obtenir un jeton afin de se connecter à votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Azure Active Directory \[Login.microsoftonline.com\].
  * Pour écrire les clés de chiffrement dans votre coffre de clés, la machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison Key Vault.
  * La machine virtuelle IaaS doit être en mesure de se connecter au point de terminaison de stockage Azure qui héberge le référentiel d’extensions Azure et au compte de stockage Azure qui héberge les fichiers de disque dur virtuel.

  > [!NOTE]
  > Si votre stratégie de sécurité limite l’accès à Internet à partir des machines virtuelles Azure, vous pouvez résoudre l’URI ci-dessus et configurer une règle spécifique pour autoriser les connexions sortantes vers les adresses IP.
  >
  >Pour configurer Azure Key Vault et y accéder derrière un pare-feu (https://docs.microsoft.com/fr-fr/azure/key-vault/key-vault-access-behind-firewall)

* Utilisez la dernière version du kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version d’[Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

 > [!NOTE]
  > Azure Disk Encryption n’est pas pris en charge dans le [Kit de développement logiciel (SDK) Azure PowerShell version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Si vous recevez une erreur liée à l’utilisation d’Azure PowerShell 1.1.0, consultez la rubrique [Azure Disk Encryption Error Related to Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx) (Erreur Azure Disk Encryption liée à Azure PowerShell 1.1.0).

* Pour exécuter les commandes de l’interface de ligne de commande Azure et les associer à votre abonnement Azure, vous devez d’abord installer l’interface de ligne de commande Azure :
  * Pour installer l’interface de ligne de commande Azure et l’associer à votre abonnement Azure, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../cli-install-nodejs.md).
  * Pour utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager, consultez la rubrique [Commandes de l’interface de ligne de commande Azure en mode Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* Lors du chiffrement d’un disque géré, une condition préalable exige de prendre un instantané du disque géré ou une sauvegarde du disque en dehors de Azure Disk Encryption avant d’activer le chiffrement.  Sans une sauvegarde mise en place, tout échec inattendu au cours du chiffrement peut rendre le disque et la machine virtuelle inaccessibles sans option de récupération possible.  Set-AzureRmVMDiskEncryptionExtension ne sauvegarde pas de disque géré actuellement, et il ne peut que déclencher une erreur s’il est utilisé avec un disque géré, sauf si le paramètre - skipVmBackup a été spécifié.  L’utilisation de ce paramètre n’est pas sûre, sauf si une sauvegarde a déjà été effectuée en dehors d’Azure Disk Encryption.   Lorsque le paramètre - skipVmBackup est spécifié, l’applet de commande n’effectue pas de sauvegarde du disque géré avant le chiffrement.  Ainsi, il est considéré comme condition préalable obligatoire de vérifier qu’une sauvegarde de la machine virtuelle du disque géré est bien en place avant d’activer Azure Disk Encryption, au cas où une récupération serait ultérieurement nécessaire.  
> [!NOTE]
 > Le paramètre - skipVmBackup ne doit jamais être utilisé, sauf si une sauvegarde ou un instantané a déjà été réalisé en dehors d’Azure Disk Encryption. 

* La solution Azure Disk Encryption utilise le protecteur de clé externe BitLocker pour les machines virtuelles IaaS Windows. Pour des machines virtuelles jointes au domaine, ne distribuez PAS de stratégies de groupe qui appliquent des protecteurs de Module de plateforme sécurisée (TPM). Pour en savoir plus sur la stratégie de groupe pour « Autoriser BitLocker sans module de plateforme sécurisée compatible », consultez la rubrique [BitLocker Group Policy Reference](https://technet.microsoft.com/library/ee706521) (Référence de stratégie de groupe BitLocker).
* La stratégie BitLocker, sur les machines virtuelles jointes à un domaine et ayant une stratégie de groupe personnalisée, doit inclure le paramètre suivant : `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption échoue lorsque les paramètres de stratégie de groupe personnalisée pour Bitlocker sont incompatibles. Sur les machines dont le paramètre de stratégie n’était pas correct, l’application de la nouvelle stratégie, la mise à jour forcée de la nouvelle stratégie (gpupdate.exe /force) et le redémarrage peuvent être nécessaires.  
* Pour créer l’application Azure AD, créer un coffre de clés ou en configurer un existant et activer le chiffrement, consultez la rubrique [Script PowerShell prérequis pour Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Pour configurer les composants requis de chiffrement de disque à l’aide de l’interface de ligne de commande Azure, consultez [ce script bash](https://github.com/ejarvi/ade-cli-getting-started).
* Pour utiliser le service Sauvegarde Azure afin de sauvegarder et de restaurer des machines virtuelles chiffrées, lorsque le chiffrement est activé avec Azure Disk Encryption, chiffrez vos machines virtuelles à l’aide de la configuration de clé Azure Disk Encryption. Le service de sauvegarde prend en charge les machines virtuelles chiffrées à l’aide des configurations KEK et non-KEK. Voir [Guide pratique de sauvegarde et restauration des machines virtuelles chiffrées avec Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Lors du chiffrement d’un volume de système d’exploitation Linux, notez qu’un redémarrage de la machine virtuelle est actuellement nécessaire à la fin du processus. Il est possible de le faire via le portail, Powershell ou CLI.   Pour suivre la progression du chiffrement, consultez régulièrement le message d’état retourné par Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Une fois le chiffrement terminé, le message d’état retourné par cette commande indique le résultat de cette opération.  Par exemple, « ProgressMessage : le chiffrement du disque du système d’exploitation s’est correctement déroulé, veuillez redémarrer la machine virtuelle ». À ce stade, la machine virtuelle peut être redémarrée et utilisée.  

* Azure Disk Encryption pour Linux exige que le système de fichiers des disques de données soit monté dans Linux avant l’opération de chiffrement.

* Les disques de données montés de façon récursive ne sont pas pris en charge par Azure Disk Encryption pour Linux. Par exemple, si le système cible a monté un disque sur /foo/bar, puis un autre sur /foo/bar/baz, le chiffrement de /foo/bar/baz réussit, mais le chiffrement de /foo/bar échoue. 

* Azure Disk Encryption est uniquement pris en charge sur les images de la galerie Azure prises en charge qui remplissent les conditions préalables susmentionnées. Les images client personnalisées ne sont pas prises en charge en raison de comportements de processus et de schémas de partition personnalisés pouvant exister sur ces images. De plus, même les machines virtuelles d’image de galerie, qui initialement remplissaient les conditions préalables demandées, mais qui ont été modifiées depuis leur création, peuvent être incompatibles.  Ainsi, la procédure suggérée pour chiffrer une machine virtuelle Linux consiste à démarrer depuis une nouvelle image de galerie, à chiffrer la machine virtuelle, puis à ajouter en fonction des besoins les données ou les logiciels personnalisés à cette machine virtuelle.  

* Azure Disk Encryption permet de configurer un volume de données local (volume Bek pour Windows et /mnt/azure_bek_disk pour Linux) pour y stocker en toute sécurité la clé de chiffrement d’une machine virtuelle IaaS. Vous ne devez pas supprimer ni modifier le contenu de ce disque. Vous ne devez pas non plus démonter ce disque, car les clés de chiffrement qui y sont stockées sont nécessaires pour effectuer les opérations de chiffrement sur les machines virtuelles IaaS. Consultez le fichier LISEZMOI fourni avec le volume pour obtenir plus d’informations.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Configurer l’application Azure AD dans Azure Active Directory
Lorsque le chiffrement doit être activé sur une machine virtuelle en cours d’exécution dans Azure, Azure Disk Encryption génère et écrit les clés de chiffrement dans votre coffre de clés. La gestion des clés de chiffrement dans votre coffre de clés nécessite l’authentification Azure AD.

Une application Azure AD doit donc être créée à cet effet. Vous trouverez la procédure détaillée d’inscription d’une application dans la section « Obtenir une identité d’application » du billet de blog [Azure Key Vault – Étape par étape](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Cet article contient également plusieurs exemples utiles sur l’installation et la configuration de votre coffre de clés. Pour l’authentification, vous pouvez utiliser soit l’authentification par clé secrète de client, soit l’authentification Azure AD par certificat de client.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Authentification par clé secrète de client pour Azure AD
Les sections qui suivent vous expliquent comment configurer une authentification par clé secrète de client pour Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Créer une application Azure AD à l’aide d’Azure PowerShell
Pour créer une application Azure AD, utilisez l’applet de commande PowerShell suivante :

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId est l’ID de client Azure AD et $aadClientSecret est la clé secrète de client que vous devez utiliser ultérieurement pour activer Azure Disk Encryption. Prenez soin de bien sauvegarder la clé secrète de client Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Configuration de l’ID de client Azure AD et de la clé secrète à partir du portail Azure
Vous pouvez également configurer votre ID de client Azure AD et la clé secrète à l’aide du portail Azure. Pour cela, procédez comme suit :

1. Cliquez sur l’onglet **Active Directory**.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Cliquez sur **Ajouter une application** et saisissez le nom de l’application.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Cliquez sur le bouton fléché et configurez les propriétés de l’application.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Cliquez sur la coche dans le coin inférieur gauche pour terminer. La page de configuration d’application s’affiche et l’ID de client Azure AD s’affiche en bas de la page.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Enregistrez la clé secrète de client Azure AD en cliquant sur le bouton **Enregistrer**. Notez la clé secrète de client Azure AD dans la zone de texte de clé. Prenez soin de bien la sauvegarder.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>Utiliser une application existante
Pour exécuter les commandes suivantes, téléchargez et utilisez le [module Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Vous devez exécuter les commandes ci-dessous à partir d’une nouvelle fenêtre PowerShell. N’utilisez pas Azure PowerShell ou la fenêtre Azure Resource Manager pour exécuter ces commandes. Nous vous recommandons cette approche car ces applets de commande se trouvent dans le module MSOnline ou Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Authentification par certificat pour Azure AD
> [!NOTE]
> L’authentification par certificat Azure AD n’est actuellement pas prise en charge sur les machines virtuelles Linux.

Les sections qui suivent expliquent comment configurer une authentification par certificat pour Azure AD.

##### <a name="create-an-azure-ad-application"></a>Créer une application Azure AD
Pour créer une application Azure AD, utilisez les applets de commande PowerShell suivantes :

> [!NOTE]
> Remplacez la chaîne `yourpassword` ci-dessous par votre mot de passe sécurisé et sauvegardez celui-ci.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Une fois que vous avez terminé cette étape, téléchargez un fichier .pfx dans votre coffre de clés et activez la stratégie d’accès nécessaire pour déployer ce certificat sur une machine virtuelle.

##### <a name="use-an-existing-azure-ad-application"></a>Utiliser une application Azure AD existante
Si vous configurez l’authentification par certificat pour une application existante, utilisez les applets de commande PowerShell ci-dessous. Veillez à les exécuter à partir d’une nouvelle fenêtre PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Une fois que vous avez terminé cette étape, téléchargez un fichier .pfx dans votre coffre de clés et activez la stratégie d’accès nécessaire pour déployer ce certificat sur une machine virtuelle.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Télécharger un fichier .pfx dans le coffre de clés
Pour obtenir une explication détaillée de ce processus, consultez [le Blog officiel de l’équipe Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Toutefois, vous aurez uniquement besoin des applets de commande PowerShell suivantes pour exécuter cette tâche. Veillez à les exécuter à partir de la console Azure PowerShell.

> [!NOTE]
> Remplacez la chaîne `yourpassword` ci-dessous par votre mot de passe sécurisé et sauvegardez celui-ci.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Déployer un certificat dans le coffre de clés sur une machine virtuelle existante
Après avoir téléchargé le fichier .pfx, déployez un certificat dans le coffre de clés sur une machine virtuelle existante à l’aide de la commande suivante :
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Définir la stratégie d’accès au coffre de clés pour l’application Azure AD
Votre application Azure AD a besoin d’autorisations d’accès aux clés ou aux clés secrètes dans le coffre. Utilisez l’applet de commande [`Set-AzureKeyVaultAccessPolicy`](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) pour accorder des autorisations à l’application en utilisant l’ID client (qui a été généré lors de l’enregistrement de l’application) comme valeur du paramètre _–ServicePrincipalName_. Pour en savoir plus, consultez le billet de blog [Azure Key Vault – Étape par étape](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Voici comment exécuter cette tâche via PowerShell :

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption requiert de configurer les stratégies d’accès suivantes sur votre application cliente Azure AD : autorisations _WrapKey_ et _Set_.

## <a name="terminology"></a>Terminologie
Reportez-vous au tableau de terminologie suivant pour comprendre certains des termes couramment utilisés par cette technologie :

| Terminologie | Définition |
| --- | --- |
| Azure AD | Azure AD est [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Un compte Azure AD est requis pour l’authentification, le stockage et l’extraction des clés secrètes d’un coffre de clés. |
| Azure Key Vault | Key Vault est un service de gestion de clés de chiffrement basé sur des modules de sécurité matériels FIPS (Federal Information Processing Standards) qui vous permet de protéger vos clés de chiffrement et les clés secrètes sensibles. Pour plus d’informations, consultez la documentation relative à [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| ARM | Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) est une technologie de chiffrement de volume Windows qui permet d’activer le chiffrement de disque sur des machines virtuelles IaaS Windows. |
| BEK | Les clés de chiffrement BitLocker servent à chiffrer le volume de démarrage du système d’exploitation et les volumes de données. Les clés BitLocker sont sauvegardées dans un coffre de clés en tant que clés secrètes. |
| Interface de ligne de commande | Voir [Interface de ligne de commande Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) est le sous-système de chiffrement de disque transparent Linux utilisé pour activer le chiffrement de disque sur les machines virtuelles IaaS Linux. |
| KEK | La clé de chiffrement à clé est la clé asymétrique (RSA 2048) que vous pouvez utiliser pour protéger ou encapsuler la clé secrète. Vous pouvez fournir une clé protégée par le module HSM ou une clé protégée par le logiciel. Pour plus d’informations, consultez la documentation relative à [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Applets de commande PS | Voir [Applets de commande Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Créer et configurer votre coffre de clés pour Azure Disk Encryption
Azure Disk Encryption protège les clés et les clés secrètes de chiffrement dans votre coffre de clés. Pour configurer votre coffre de clés pour Azure Disk Encryption, suivez les étapes de chacune des sections suivantes.

#### <a name="create-a-key-vault"></a>Création d’un coffre de clés
Pour créer un coffre de clés, utilisez l’une des options suivantes :

* [Modèle Resource Manager « 101-Key-Vault-Create »](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Applets de commande Key Vault Azure PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Comment [Sécuriser votre coffre de clés](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Si vous avez déjà configuré un coffre de clés pour votre abonnement, passez à la section suivante.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Configurer une clé de chiffrement à clé (facultatif)
Si vous souhaitez utiliser une clé de chiffrement à clé pour renforcer la protection des clés de chiffrement BitLocker, ajoutez une clé de chiffrement à clé à votre coffre de clés. Utilisez l’applet de commande [`Add-AzureKeyVaultKey`](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) pour créer une clé de chiffrement à clé dans le coffre de clés. Vous pouvez également importer une clé de chiffrement à clé à partir de votre module de sécurité matériel de gestion des clés locales. Pour plus d’informations, consultez la [Documentation relative à Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Vous pouvez ajouter la clé de chiffrement à clé via Azure Resource Manager ou l’interface de votre coffre de clés.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Définir des autorisations d’accès au coffre de clés
La plateforme Azure doit avoir accès aux clés et aux clés secrètes de chiffrement dans votre coffre de clés afin de les mettre à disposition de la machine virtuelle pour lancer et déchiffrer les volumes. Pour accorder des autorisations d’accès à la plateforme Azure, définissez la propriété **EnabledForDiskEncryption** dans le coffre de clés à l’aide de l’applet de commande de coffre de clés PowerShell suivante :

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Vous pouvez également définir la propriété **EnabledForDiskEncryption** en accédant à [l’Explorateur de ressources Azure](https://resources.azure.com).

Comme indiqué précédemment, vous devez définir la propriété **EnabledForDiskEncryption** dans votre coffre de clés. Si ce n'est pas le cas, le déploiement échouera.

Vous pouvez configurer des stratégies d’accès pour votre application Azure AD à partir de l’interface du coffre de clés, comme indiqué ci-dessous :

![coffre de clés Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Dans l’onglet **Stratégies d’accès avancées**, assurez-vous que votre coffre de clés est activé pour Azure Disk Encryption :

![Coffre de clés Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scénarios de déploiement de chiffrement de disque et expériences utilisateur
Il existe de nombreux scénarios permettant d’activer le chiffrement de disque et les étapes peuvent varier selon le scénario. Les sections suivantes décrivent ces scénarios de manière plus détaillée.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de la Place de marché
Vous pouvez activer le chiffrement de disque sur de nouvelles machines virtuelles IaaS Windows à partir de la Place de marché Azure à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**, saisissez la configuration de chiffrement dans le panneau **Paramètres**, puis cliquez sur **OK**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur **Créer** pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.

> [!NOTE]
> Ce modèle crée une machine virtuelle Windows chiffrée qui utilise l’image de la galerie Windows Server 2012.

Vous pouvez activer le chiffrement de disque sur une nouvelle machine virtuelle IaaS RedHat Linux 7.2 avec une baie RAID-0 de 200 Go à l’aide de ce [modèle Resource Manager](https://aka.ms/fde-rhel). Une fois que le modèle est déployé, vérifiez l’état du chiffrement de la machine virtuelle à l’aide de l’applet de commande `Get-AzureRmVmDiskEncryptionStatus` comme décrit dans la section [Chiffrement du lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution](#encrypting-os-drive-on-a-running-linux-vm). Lorsque la machine retourne l’état _VMRestartPending_, redémarrez la machine virtuelle.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour les nouvelles machines virtuelles dans un scénario Place de marche utilisant l’ID de client Azure AD :

| Paramètre | Description |
| --- | --- |
| adminUsername | Nom de l’utilisateur administrateur de la machine virtuelle. |
| adminPassword | Mot de passe de l’utilisateur administrateur de la machine virtuelle. |
| newStorageAccountName | Nom du compte de stockage pour stocker les disques durs virtuels du système d’exploitation et de données. |
| vmSize | Taille de la machine virtuelle. Actuellement, seules les séries A, D et G standard sont prises en charge. |
| virtualNetworkName | Nom du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| subnetName | Nom du sous-réseau du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| AADClientID | ID de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| AADClientSecret | Clé secrète de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| keyVaultURL | URL du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir à l’aide de l’applet de commande `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée (facultatif). |
| keyVaultResourceGroup | Groupe de ressources du coffre de clés. |
| vmName | Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée. |

> [!NOTE]
> _KeyEncryptionKeyURL_ est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) pour renforcer la clé de chiffrement des données (clé secrète de chiffrement) dans votre coffre de clés.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Activation du chiffrement sur de nouvelles machines virtuelles IaaS créées à partir de disques durs virtuels chiffrés par le client et de clés de chiffrement
Dans ce scénario, vous pouvez activer le chiffrement à l’aide du modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. Les sections ci-dessous décrivent de façon plus détaillée le modèle Resource Manager et les commandes CLI.

Suivez les instructions d’une de ces sections pour la préparation d’images déjà chiffrées qui peuvent être utilisées dans Azure. Une fois l’image créée, vous pouvez suivre la procédure décrite dans la section suivante pour créer une machine virtuelle Azure chiffrée.

* [Préparer un disque dur virtuel Windows déjà chiffré](#preparing-a-pre-encrypted-windows-vhd)
* [Préparer un disque dur virtuel Linux déjà chiffré](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Utilisation du modèle Resource Manager
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**, saisissez la configuration de chiffrement dans le panneau **Paramètres**, puis cliquez sur **OK**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur **Créer** pour activer le chiffrement sur la nouvelle machine virtuelle IaaS.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour votre disque dur virtuel chiffré :

| Paramètre | Description |
| --- | --- |
| newStorageAccountName | Nom du compte de stockage pour stocker le disque dur virtuel du système d’exploitation chiffré. Ce compte de stockage doit avoir été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle. |
| osVhdUri | URI du disque dur virtuel du système d’exploitation à partir du compte de stockage. |
| osType | Type de système d’exploitation (Windows/Linux). |
| virtualNetworkName | Nom du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. Le nom doit avoir été créé dans le même groupe de ressources et le même emplacement que la machine virtuelle. |
| subnetName | Nom du sous-réseau du réseau virtuel auquel la carte d’interface réseau de machine virtuelle appartient. |
| vmSize | Taille de la machine virtuelle. Actuellement, seules les séries A, D et G standard sont prises en charge. |
| keyVaultResourceID | L’ID de ressource qui identifie la ressource de coffre de clés dans Azure Resource Manager. Vous pouvez l’obtenir à l’aide de l’applet de commande PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL de la clé de chiffrement de disque définie dans le coffre de clés. |
| keyVaultKekUrl | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé de chiffrement de disque générée. |
| vmName | Nom de la machine virtuelle IaaS. |

#### <a name="using-powershell-cmdlets"></a>Utilisation d’applets de commande PowerShell
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré à l’aide de l’applet de commande PowerShell [`Set-AzureRmVMOSDisk`](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Utilisation des commandes CLI
Pour activer le chiffrement de disque de ce scénario à l’aide des commandes CLI, procédez comme suit :

1. Définissez des stratégies d’accès dans votre coffre de clés :

   * Définissez l’indicateur **EnabledForDiskEncryption** :

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Autorisez l’application Azure AD à écrire des clés secrètes dans votre coffre de clés :

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution, saisissez :

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenez l’état de chiffrement :

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir de votre disque dur virtuel chiffré, utilisez les paramètres suivants avec la commande `azure vm create` :

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Activer le chiffrement sur des machines virtuelles IaaS Windows existantes/en cours de fonctionnement dans Azure
Dans ce scénario, vous pouvez activer le chiffrement à l’aide du modèle Resource Manager, des applets de commande PowerShell ou des commandes CLI. Les sections ci-dessous décrivent de façon plus détaillée comment l’activer à l’aide du modèle Resource Manager et des commandes CLI.

#### <a name="using-the-resource-manager-template"></a>Utilisation du modèle Resource Manager
Vous pouvez activer le chiffrement de disque sur des machines virtuelles IaaS Windows existantes ou en cours d’exécution dans Azure à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**, saisissez la configuration de chiffrement dans le panneau **Paramètres**, puis cliquez sur **OK**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour les machines virtuelles existantes ou en cours d’exécution qui utilisent un ID de client Azure AD :

| Paramètre | Description |
| --- | --- |
| AADClientID | ID de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| AADClientSecret | Clé secrète de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| keyVaultName | Nom du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir à l’aide de l’applet de commande `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs valides sont _Système d’exploitation_, _Données_ et _Tous_. |
| sequenceVersion | Version de séquence de l’opération BitLocker. Incrémentez ce numéro de version à chaque fois qu’une opération de chiffrement de disque est exécutée sur la même machine virtuelle. |
| vmName | Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée. |

> [!NOTE]
> _KeyEncryptionKeyURL_ est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) pour renforcer la clé de chiffrement des données (clé secrète de chiffrement BitLocker) dans le coffre de clés.

#### <a name="using-powershell-cmdlets"></a>Utilisation d’applets de commande PowerShell
Pour plus d’informations sur l’activation du chiffrement avec Azure Disk Encryption à l’aide des applets de commande PowerShell, consultez les billets de blog [Explorer Azure Disk Encryption avec Azure PowerShell - partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [Explorer Azure Disk Encryption avec Azure PowerShell - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Utilisation des commandes CLI
Pour activer le chiffrement sur des machines virtuelles IaaS Windows existantes ou en cours d’exécution dans Azure à l’aide des commandes CLI, procédez comme suit :

1. Pour définir des stratégies d’accès dans le coffre de clés :
   * Définissez l’indicateur **EnabledForDiskEncryption** :

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Autorisez l’application Azure AD à écrire des clés secrètes dans votre coffre de clés :

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution :

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Pour obtenir l’état de chiffrement :

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir de votre disque dur virtuel chiffré, utilisez les paramètres suivants avec la commande `azure vm create` :

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Activer le chiffrement sur une machine virtuelle IaaS Linux existante ou en cours d’exécution dans Azure
Vous pouvez activer le chiffrement de disque sur une machine virtuelle IaaS Linux existante ou en cours d’exécution dans Azure à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Cliquez sur **Déployer sur Azure** dans le modèle de démarrage rapide Azure, saisissez la configuration de chiffrement dans le panneau **Paramètres**, puis cliquez sur **OK**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur **Créer** pour activer le chiffrement sur la machine virtuelle IaaS existante ou en cours d’exécution.

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour les machines virtuelles existantes ou en cours d’exécution qui utilisent un ID de client Azure AD :

| Paramètre | Description |
| --- | --- |
| AADClientID | ID de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| AADClientSecret | Clé secrète de client de l’application Azure AD qui dispose des autorisations pour écrire des clés secrètes dans le coffre de clés. |
| keyVaultName | Nom du coffre de clés dans lequel la clé BitLocker doit être téléchargée. Vous pouvez l’obtenir à l’aide de l’applet de commande `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL de la clé de chiffrement à clé utilisée pour chiffrer la clé BitLocker générée. Ce paramètre est facultatif si vous sélectionnez **nokek** dans la liste déroulante UseExistingKek. Si vous sélectionnez **kek** dans la liste déroulante UseExistingKek, vous devez entrer la valeur _keyEncryptionKeyURL_. |
| volumeType | Type de volume sur lequel l’opération de chiffrement est effectuée. Les valeurs prises en charge valides sont _Système d’exploitation_ ou _Toutes_ (voir les versions de Linux prises en charge et leurs versions pour système d’exploitation et disques de données dans la section des prérequis ci-dessus). |
| sequenceVersion | Version de séquence de l’opération BitLocker. Incrémentez ce numéro de version à chaque fois qu’une opération de chiffrement de disque est exécutée sur la même machine virtuelle. |
| vmName | Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée. |
| passPhrase | Saisissez une phrase secrète forte comme clé de chiffrement de données. |

> [!NOTE]
> _KeyEncryptionKeyURL_ est un paramètre facultatif. Vous pouvez apporter vos propres clés de chiffrement à clé (KEK) pour renforcer la clé de chiffrement des données (clé secrète de chiffrement) dans votre coffre de clés.

#### <a name="cli-commands"></a>Commandes CLI
Vous pouvez activer le chiffrement de disque sur votre disque dur virtuel chiffré en installant et en utilisant les [commandes CLI](../cli-install-nodejs.md). Pour activer le chiffrement sur des machines virtuelles IaaS Linux existantes ou en cours d’exécution dans Azure à l’aide des commandes CLI, procédez comme suit :

1. Définissez des stratégies d’accès dans le coffre de clés :

 * Définissez l’indicateur **EnabledForDiskEncryption** :

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Autorisez l’application Azure AD à écrire des clés secrètes dans votre coffre de clés :

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Pour activer le chiffrement sur une machine virtuelle existante ou en cours d’exécution :

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenez l’état de chiffrement :

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Pour activer le chiffrement sur une nouvelle machine virtuelle à partir de votre disque dur virtuel chiffré, utilisez les paramètres suivants avec la commande `azure vm create` :
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Obtenir l’état de chiffrement d’une machine virtuelle IaaS chiffrée
Vous pouvez obtenir l’état de chiffrement en utilisant Azure Resource Manager, des [applets de commande PowerShell](/powershell/azure/overview) ou des commandes CLI. Les sections ci-dessous expliquent comment utiliser le portail Azure et les commandes de l’interface de ligne de commande pour obtenir l’état de chiffrement.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Obtenir l’état de chiffrement d’une machine virtuelle Windows chiffrée à l’aide d’Azure Resource Manager
Pour obtenir l’état de chiffrement de la machine virtuelle IaaS à partir d’Azure Resource Manager, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis cliquez sur **Machines virtuelles** dans le volet gauche pour afficher un récapitulatif des machines virtuelles de votre abonnement. Vous pouvez filtrer la vue des machines virtuelles en sélectionnant le nom d’abonnement dans la liste déroulante**Abonnement**.

2. Cliquez sur **Colonnes** en haut de la page **Machines virtuelles**.

3. Dans le panneau **Choisir une colonne**, sélectionnez **Chiffrement de disque**, puis cliquez sur **Mettre à jour**. La colonne de chiffrement de disque présente l’état de chiffrement _Activé_ ou _Non activé_ pour chaque machine virtuelle, comme indiqué dans la figure ci-dessous :

 ![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Obtenir l’état de chiffrement d’une machine virtuelle IaaS (Windows/Linux) chiffrée à l’aide de l’applet de commande PowerShell de chiffrement de disque
Vous pouvez obtenir l’état de chiffrement de la machine virtuelle IaaS à l’aide de l’applet de commande PowerShell de disque de chiffrement `Get-AzureRmVMDiskEncryptionStatus`. Pour obtenir les paramètres de chiffrement de votre machine virtuelle, entrez les informations suivantes :

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Le résultat de _Get-AzureRmVMDiskEncryptionStatus_ peut être examiné à la recherche d’URL de clés de chiffrement.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Les valeurs des paramètres OSVolumeEncrypted et DataVolumesEncrypted sont définies sur _Encrypted_, indiquant que les deux volumes sont chiffrés avec Azure Disk Encryption. Pour plus d’informations sur l’activation du chiffrement avec Azure Disk Encryption à l’aide des applets de commande PowerShell, consultez les billets de blog [Explorer Azure Disk Encryption avec Azure PowerShell - partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) et [Explorer Azure Disk Encryption avec Azure PowerShell - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Sur les machines virtuelles Linux, l’applet de commande `Get-AzureRmVMDiskEncryptionStatus` prend 3-4 minutes pour signaler l’état de chiffrement.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Obtenir l’état de chiffrement de la machine virtuelle IaaS à partir de la commande CLI de chiffrement
Vous pouvez obtenir l’état de chiffrement de la machine virtuelle IaaS en utilisant la commande CLI de chiffrement de disque `azure vm show-disk-encryption-status`. Pour obtenir les paramètres de chiffrement de votre machine virtuelle, saisissez votre session CLI Azure :

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Désactivation du chiffrement sur une machine virtuelle IaaS Windows en cours d’exécution
Vous pouvez désactiver le chiffrement sur une machine virtuelle IaaS Windows ou Linux en cours d’exécution via le modèle Resource Manager Azure Disk Encryption ou via les applets de commande PowerShell et spécifier la configuration de déchiffrement.

##### <a name="windows-vm"></a>Machine virtuelle Windows
L’étape de désactivation du chiffrement désactive le chiffrement du volume de système d’exploitation ou du volume de données (ou les deux) sur la machine virtuelle IaaS Windows en cours d’exécution. Vous ne pouvez pas désactiver le volume de système d’exploitation et laisser le volume de données chiffré. Une fois le chiffrement désactivé,le modèle de déploiement Azure Classic met à jour le modèle de service de machine virtuelle et la machine virtuelle IaaS Windows est marquée comme déchiffrée. Le contenu de la machine virtuelle n’est plus chiffré au repos. Le déchiffrement ne supprime ni votre coffre de clés ni le support de clé de chiffrement (clés de chiffrement BitLocker pour Windows et phrase secrète pour Linux).

##### <a name="linux-vm"></a>Machine virtuelle Linux
L’étape de désactivation du chiffrement désactive le chiffrement du volume de données sur la machine virtuelle IaaS Linux en cours d’exécution. Cette étape fonctionne uniquement si le disque du système d’exploitation n’est pas chiffré.

> [!NOTE]
> La désactivation du chiffrement sur le lecteur du système d’exploitation n’est pas autorisée sur les machines virtuelles Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Désactivation du chiffrement sur une machine virtuelle IaaS existante ou en cours d’exécution
Vous pouvez désactiver le chiffrement de disque sur des machines virtuelles IaaS Windows en cours d’exécution à l’aide du [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Dans le modèle de démarrage rapide Azure, cliquez sur **Déployer sur Azure**, saisissez la configuration de déchiffrement dans le panneau **Paramètres**, puis cliquez sur **OK**.

2. Sélectionnez l’abonnement, le groupe de ressources, l’emplacement du groupe de ressources, les conditions juridiques et les accords, puis cliquez sur **Créer** pour activer le chiffrement sur une nouvelle machine virtuelle IaaS.

Pour les machines virtuelles Linux, vous pouvez désactiver le chiffrement à l’aide du modèle [Désactiver le chiffrement sur une machine virtuelle Linux en cours d’exécution](https://aka.ms/decrypt-linuxvm).

Le tableau suivant répertorie les paramètres du modèle Resource Manager pour la désactivation du chiffrement sur une machine virtuelle IaaS en cours d’exécution :

| Paramètre | Description |
| --- | --- |
| vmName | Nom de la machine virtuelle sur laquelle l’opération de chiffrement doit être effectuée.
| volumeType | Type de volume sur lequel l’opération de déchiffrement est effectuée. Les valeurs valides sont _Système d’exploitation_, _Données_ et _Tous_. Vous ne pouvez pas désactiver le chiffrement sur un volume de démarrage/système d’exploitation d’une machine virtuelle IaaS Windows en cours d’exécution sans désactiver le chiffrement sur le volume _Données_. Notez également que la désactivation du chiffrement sur le lecteur du système d’exploitation n’est pas autorisée sur les machines virtuelles Linux. |
| sequenceVersion | Version de séquence de l’opération BitLocker. Incrémentez ce numéro de version à chaque fois qu’une opération de déchiffrement de disque est exécutée sur la même machine virtuelle. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Désactivation du chiffrement sur une machine virtuelle IaaS existante ou en cours d’exécution
Pour désactiver le chiffrement sur une machine virtuelle IaaS existante ou en cours d’exécution à l’aide de l’applet de commande PowerShell, utilisez [`Disable-AzureRmVMDiskEncryption`](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Cette applet de commande prend en charge les machines virtuelles Linux et Windows. Elle installe une extension sur la machine virtuelle permettant de désactiver le chiffrement. Si le paramètre _Nom_ n’est pas spécifié, une extension avec le nom par défaut _AzureDiskEncryption pour les machines virtuelles Windows_ est créée.

Sur les machines virtuelles Linux, l’extension AzureDiskEncryptionForLinux est utilisée.

> [!NOTE]
> Cette applet de commande redémarre la machine virtuelle.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Activer le chiffrement sur une machine virtuelle IaaS pré-chiffrée avec un disque managé Azure
Utiliser le modèle ARM de disque managé Azure pour créer une machine virtuelle chiffrée à partir d’un disque dur virtuel pré-chiffré en utilisant le modèle ARM accessible via le lien   
[Créer un disque managé chiffré à partir d’un disque dur virtuel/bjet blob de stockage pré-chiffré] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Activer le chiffrement sur une nouvelle machine virtuelle IaaS Linux avec disque managé Azure
Utiliser le modèle ARM de disque managé Azure pour créer une nouvelle machine virtuelle IaaS Linux chiffrée en utilisant le modèle ARM accessible via le lien   
[Déploiement de RHEL 7.2 avec un chiffrement de disque complet] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Activer le chiffrement sur une nouvelle machine virtuelle IaaS Windows avec disque managé Azure
 Utiliser le modèle ARM de disque managé Azure pour créer une nouvelle machine virtuelle IaaS Linux chiffrée en utilisant le modèle ARM accessible via le lien   
 [Créer une machine virtuelle avec disque managé IaaS Windows chiffré à partir d’une image de la galerie] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Il est impératif de réaliser un instantané et/ou une sauvegarde d’une instance de machine virtuelle basée sur un disque géré en dehors d’Azure Disk Encryption et avant d’activer ce service de chiffrement.  Un instantané du disque géré peut être réalisé à partir du portail, sinon il est possible d’utiliser le service Sauvegarde Microsoft Azure.  Les sauvegardes vous garantissent une possibilité de récupération en cas de défaillance inattendue pendant le chiffrement.  Une fois la sauvegarde effectuée, il est possible d’utiliser l’applet de commande Set-AzureRmVMDiskEncryptionExtension pour chiffrer des disques gérés en spécifiant le paramètre - skipVmBackup.  Cette commande échoue sur les machines virtuelles de disques gérés tant qu’une sauvegarde n’a pas été effectuée et que ce paramètre n’a pas été spécifié.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Mettre à jour les paramètres de chiffrement d’une machine virtuelle non Premium chiffrée existante
  Utilisez les interfaces prises en charge de chiffrement de disque Azure existant pour une machine virtuelle en cours d’exécution [applets de commande PS, CLI ou modèles ARM] afin de mettre à jour des paramètres de chiffrement tels que ID client/secret AAD, clé de chiffrement de clé [KEK], clé de chiffrement BitLocker pour machine virtuelle Windows ou Phrase secrète pour machine virtuelle Linux, etc. Le paramètre de mise à jour de chiffrement est pris en charge pour les machines virtuelles de stockage Premium et non Premium.

## <a name="appendix"></a>Annexe
### <a name="connect-to-your-subscription"></a>Connexion à votre abonnement
Avant de poursuivre, voir la section *Conditions préalables* dans cet article. Après avoir vérifié que toutes les conditions préalables sont remplies, connectez-vous à votre abonnement en procédant comme suit :

1. Démarrez une session Azure PowerShell et connectez-vous à votre compte Azure avec la commande suivante :

    `Login-AzureRmAccount`

2. Si vous disposez de plusieurs abonnements et souhaitez spécifier un abonnement à utiliser en particulier, saisissez ce qui suit pour afficher les abonnements de votre compte :

    `Get-AzureRmSubscription`

3. Pour spécifier l’abonnement que vous souhaitez utiliser, saisissez :

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Pour vérifier que l’abonnement configuré est correct, saisissez :

    `Get-AzureRmSubscription`

5. Pour confirmer que les applets de commande Azure Disk Encryption sont installées, saisissez :

    `Get-command *diskencryption*`

6. La sortie ci-dessous confirme l’installation Powershell d’Azure Disk Encryption :

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Préparer un disque dur virtuel Windows déjà chiffré
Les sections qui suivent sont nécessaires pour préparer un disque dur virtuel Windows déjà chiffré qui sera déployé comme disque dur virtuel chiffré dans Azure IaaS. Utilisez ces informations pour préparer et démarrer une nouvelle machine virtuelle Windows (disque dur virtuel) sur Azure Site Recovery ou Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Mettre à jour la stratégie de groupe pour permettre la protection autre que par module de plateforme sécurisée pour la protection du système d’exploitation
Configurez le paramètre de stratégie de groupe BitLocker **Chiffrement de lecteur BitLocker** qui se trouve sous **Stratégie de l’ordinateur local** > **Configuration ordinateur** > **Modèles d’administration** > **Composants Windows**. Remplacez ce paramètre par **Lecteurs du système d’exploitation** > **Exiger une authentification supplémentaire au démarrage** > **Autoriser BitLocker sans un module de plateforme sécurisée compatible** comme indiqué dans la figure ci-dessous :

![Microsoft Antimalware dans Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Installer les composants de fonctionnalité BitLocker
Pour Windows Server 2012 ou version ultérieure, utilisez la commande suivante :

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Pour Windows Server 2008 R2, utilisez la commande suivante :

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Préparer le volume du système d’exploitation pour BitLocker à l’aide de `bdehdcfg`
Exécutez la commande suivante pour compresser la partition du système d’exploitation et préparer la machine pour BitLocker :

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Protéger le volume du système d’exploitation à l’aide de BitLocker
Utilisez la commande [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) pour activer le chiffrement sur le volume de démarrage à l’aide d’un protecteur de clé externe. Placez également la clé externe (fichier .bek) sur le disque ou le volume externe. Le chiffrement sera activé sur le volume système/de démarrage au prochain redémarrage.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Préparez la machine virtuelle avec un disque dur virtuel de données/de ressources distinct pour obtenir la clé externe à l’aide de BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Chiffrement d’un lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution
Le chiffrement d’un lecteur du système d’exploitation sur une machine virtuelle Linux en cours d’exécution est pris en charge sur les distributions suivantes :

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Configuration requise pour le chiffrement du lecteur du système d’exploitation

* La machine virtuelle doit être créée à partir de l’image Marketplace dans Azure Resource Manager.
* Machine virtuelle Azure au moins 4 Go de RAM (la taille recommandée est de 7 Go).
* (Pour RHEL et CentOS) Désactivez SELinux. Pour désactiver SELinux, consultez la rubrique « 4.4.2. Désactivation de SELinux » dans le [Guide d’utilisation et d’administration SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sur la machine virtuelle.
* Après avoir désactivé SELinux, redémarrez au moins une fois la machine virtuelle.

##### <a name="steps"></a>Étapes
1. Créez une machine virtuelle en utilisant l’une des distributions spécifiées précédemment.

 Pour CentOS 7.2, le chiffrement du lecteur du système d’exploitation est pris en charge via une image spécifique. Pour utiliser cette image, spécifiez « 7.2n » en tant que référence lorsque vous créez la machine virtuelle :
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configurez la machine virtuelle selon vos besoins. Si vous souhaitez chiffrer tous les lecteurs (de données et du système d’exploitation), les lecteurs de données doivent être spécifiés et montables à partir de /etc/fstab.

 > [!NOTE]
 > Utilisez UUID =... pour définir les lecteurs de données dans /etc/fstab au lieu de spécifier le nom de l’appareil de traitement par blocs (par exemple, /dev/sdb1). L’ordre des lecteurs sur la machine virtuelle est modifié au cours du chiffrement. Si votre machine virtuelle s’appuie sur un ordre spécifique d’appareils de traitement par blocs, leur montage échouera après le chiffrement.

3. Déconnectez-vous des sessions SSH.

4. Pour chiffrer le système d’exploitation, définissez volumeType sur **Tous** ou **Système d’exploitation** lors de [l’activation du chiffrement](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Tous les processus d’espace utilisateur qui ne s’exécutent pas en tant que services `systemd` doivent être arrêtés avec un `SIGKILL`. Redémarrez la machine virtuelle. Lorsque vous activez le chiffrement du lecteur du système d’exploitation sur une machine virtuelle en cours d’exécution, prévoyez un temps d’arrêt de la machine virtuelle.

5. Surveillez régulièrement la progression du chiffrement à l’aide des instructions fournies dans la [section suivante](#monitoring-os-encryption-progress).

6. Lorsque Get-AzureRmVmDiskEncryptionStatus indique « VMRestartPending », redémarrez votre machine virtuelle en vous y connectant ou en utilisant le portail, PowerShell ou CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Avant le redémarrage, nous vous recommandons d’enregistrer les [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de la machine virtuelle.

#### <a name="monitoring-os-encryption-progress"></a>Surveillance de la progression du chiffrement du système d’exploitation
Il existe trois façons de surveiller la progression du chiffrement du système d’exploitation :

* Utilisez l’applet de commande `Get-AzureRmVmDiskEncryptionStatus` et examinez le champ ProgressMessage :
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Dès que l’état « Le chiffrement du lecteur du système d’exploitation a démarré » s’affiche sur la machine virtuelle, l’opération prendra entre 40 et 50 minutes sur une machine virtuelle enregistrée dans un compte de stockage Premium.

 En raison de [l’erreur #388](https://github.com/Azure/WALinuxAgent/issues/388) dans WALinuxAgent, `OsVolumeEncrypted` et `DataVolumesEncrypted` apparaissent comme `Unknown` dans certaines distributions. Ce problème est résolu automatiquement dans WALinuxAgent version 2.1.5 et ultérieure. Si `Unknown` s’affiche dans la sortie, vous pouvez vérifier l’état du chiffrement du disque en utilisant l’Explorateur de ressources Azure.

 Accédez à [l’Explorateur de ressources Azure](https://resources.azure.com/), puis développez cette hiérarchie dans le panneau de sélection de gauche :

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 Faites défiler InstanceView pour afficher l’état de chiffrement de vos lecteurs.

 ![Vue d’instance de machine virtuelle](./media/azure-security-disk-encryption/vm-instanceview.png)

* Recherchez les [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Les messages de l’extension ADE doivent être précédés du préfixe `[AzureDiskEncryption]`.

* Connectez-vous à la machine virtuelle via SSH et obtenez le journal des extensions à partir de :

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Nous vous déconseillons de vous connecter à la machine virtuelle lorsque le chiffrement du système d’exploitation est en cours. Copiez les journaux uniquement lorsque les deux autres méthodes ont échoué.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Préparer un disque dur virtuel Linux déjà chiffré
##### <a name="ubuntu-16"></a>Ubuntu 16
Configurez le chiffrement lors de l’installation de la distribution en procédant comme suit :

1. Sélectionnez **Configure encrypted volumes** (Configurer les volumes chiffrés) lors du partitionnement des disques.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Créez un lecteur de démarrage séparé qui ne doit pas être chiffré. Chiffrez votre lecteur racine.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous chargez dans le coffre de clés.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Terminez le partitionnement.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Lorsque vous démarrez la machine virtuelle et devez fournir une phrase secrète, utilisez la phrase secrète que vous avez fournie à l’étape 3.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Préparez la machine virtuelle au chargement dans Azure en suivant [ces instructions](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

Configurez le chiffrement pour l’utiliser dans Azure en procédant comme suit :

1. Créez un fichier sous /usr/local/sbin/azure_crypt_key.sh, avec le contenu du script ci-dessous. Prêtez une attention particulière à KeyFileName, car il s’agit du nom de fichier de phrase secrète utilisé par Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Modifiez la configuration du chiffrement dans */etc/crypttab*. Il doit se présenter comme suit :
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Si vous modifiez *azure_crypt_key.sh* dans Windows et que vous le copiez sur Linux, exécutez `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Ajoutez des autorisations exécutables au script :
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Éditez */etc/initramfs-tools/modules* en ajoutant des lignes :
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Exécutez `update-initramfs -u -k all` pour mettre à jour l’initramfs afin de mettre en vigueur le `keyscript`.

7. Vous pouvez maintenant annuler l’approvisionnement de la machine virtuelle.

 ![Configuration d’Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Passez à l’étape suivante et [chargez votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Pour configurer le chiffrement lors de l’installation de la distribution, procédez comme suit :
1. Lorsque vous partitionnez les disques, sélectionnez **Chiffrer le groupe de volumes**, puis entrez un mot de passe. Il s’agit du mot de passe que vous allez charger dans votre coffre de clés.

 ![Configuration d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Démarrez la machine virtuelle à l’aide de votre mot de passe.

 ![Configuration d’openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Préparez la machine virtuelle pour le chargement dans Azure en suivant les instructions de la rubrique [Préparation d’une machine virtuelle SLES ou openSUSE pour Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

Pour configurer le chiffrement afin de l’utiliser dans Azure, procédez comme suit :
1. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Mettez ces lignes en commentaire à la fin du fichier /usr/lib/dracut/modules.d/90crypt/module-setup.sh :
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Ajoutez la ligne suivante au début du fichier /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh :
 ```
    DRACUT_SYSTEMD=0
 ```
Puis, remplacez toutes les occurrences de :
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
to:
```
    if [ 1 ]; then
```
4. Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et ajoutez-le à « # Open LUKS device » :

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Exécutez `/usr/sbin/dracut -f -v` pour mettre à jour le fichier initrd.

6. Vous pouvez désormais annuler l’approvisionnement de la machine virtuelle et [charger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

##### <a name="centos-7"></a>CentOS 7
Pour configurer le chiffrement lors de l’installation de la distribution, procédez comme suit :
1. Sélectionnez **Chiffrer mes données** lors du partitionnement des disques.

 ![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Vérifiez que **Chiffrer** est sélectionné pour la partition racine.

 ![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Indiquez une phrase secrète. Il s’agit de la phrase secrète que vous allez charger dans votre coffre de clés.

 ![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Lorsque vous démarrez la machine virtuelle et devez fournir une phrase secrète, utilisez la phrase secrète que vous avez fournie à l’étape 3.

 ![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Préparez la machine virtuelle pour le chargement dans Azure en suivant les instructions « CentOS 7.0+ » dans la rubrique [Préparation d’une machine virtuelle CentOS pour Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). N’exécutez pas encore la dernière étape (annulation de l’approvisionnement de la machine virtuelle).

6. Vous pouvez désormais annuler l’approvisionnement de la machine virtuelle et [charger votre disque dur virtuel](#upload-encrypted-vhd-to-an-azure-storage-account) dans Azure.

Pour configurer le chiffrement afin de l’utiliser dans Azure, procédez comme suit :

1. Modifiez le fichier /etc/dracut.conf et ajoutez la ligne suivante :
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Mettez ces lignes en commentaire à la fin du fichier /usr/lib/dracut/modules.d/90crypt/module-setup.sh :
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Ajoutez la ligne suivante au début du fichier /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh :
```
    DRACUT_SYSTEMD=0
```
Puis, remplacez toutes les occurrences de :
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. Modifiez /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh et ajoutez-le après « # Open LUKS device » :
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Exécutez « /usr/sbin/dracut -f -v » pour mettre à jour le fichier initrd.

![Configuration de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Télécharger des disques durs virtuels cryptés dans un compte de stockage Azure
Une fois le chiffrement BitLocker ou DM-Crypt activé, le disque dur virtuel chiffré local doit être chargé sur votre compte de stockage.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Télécharger la clé secrète de chiffrement de disque pour la machine virtuelle déjà chiffrée dans le coffre de clés
La clé secrète de chiffrement de disque obtenue précédemment doit être téléchargée en tant que clé secrète dans votre coffre de clés. Le chiffrement de disque et les autorisations du coffre de clés doivent être activés pour votre client Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>La clé secrète de chiffrement de disque non chiffré avec une clé de chiffrement à clé KEK
Utilisez [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) pour configurer la clé secrète dans votre coffre de clés. Si vous disposez d’une machine virtuelle Windows, le fichier bek est encodé sous forme de chaîne en base64, puis téléchargé dans le coffre de clés à l’aide de l’applet de commande `Set-AzureKeyVaultSecret`. Pour Linux, la phrase secrète est encodée sous forme de chaîne en base64, puis téléchargée dans le coffre de clés. Assurez-vous également que les balises suivantes sont définies lors de la création de la clé secrète dans le coffre de clés.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Utilisez `$secretUrl` à l’étape suivante pour [attacher le lecteur du système d’exploitation sans utiliser de clé de chiffrement à clé](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Disque chiffré avec une clé secrète de chiffrement de disque à clé KEK
Avant de télécharger la clé secrète dans le coffre de clés, vous pouvez éventuellement la chiffrer à l’aide d’une clé de chiffrement à clé. Utilisez [l’API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de retour à la ligne pour chiffrer d’abord la clé secrète à l’aide de la clé de chiffrement à clé. La sortie de cette opération de retour à la ligne est une chaîne d’URL encodée en base64 que vous pouvez ensuite charger comme clé secrète à l’aide de l’applet de commande [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret).

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Utilisez `$KeyEncryptionKey` et `$secretUrl` à l’étape suivante pour [attacher le lecteur du système d’exploitation à l’aide de la clé de chiffrement à clé](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Spécifier une URL secrète lorsque vous attachez un lecteur de système d’exploitation
#### <a name="without-using-a-kek"></a>Sans utiliser de clé de chiffrement à clé KEK
Lorsque vous attachez le lecteur du système d’exploitation, exécutez la commande `$secretUrl`. L’URL a été générée dans la section « La clé secrète de chiffrement de disque non chiffrée avec une clé de chiffrement à clé ».

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>À l’aide d’une clé de chiffrement à clé KEK
Lorsque vous attachez le lecteur du système d’exploitation, exécutez les commandes `$KeyEncryptionKey` et `$secretUrl`. L’URL a été générée dans la section « La clé secrète de chiffrement de disque non chiffrée avec une clé de chiffrement à clé ».

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="for-more-information"></a>Pour plus d’informations
[Explorer Azure Disk Encryption avec Azure PowerShell - partie 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Explorer Azure Disk Encryption avec Azure PowerShell - partie 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
