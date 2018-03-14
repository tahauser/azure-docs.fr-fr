---
title: " Gérer un serveur de traitement de montée en puissance parallèle dans Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment configurer et gérer un serveur de traitement de montée en puissance parallèle dans Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="manage-a-scale-out-process-server"></a>Gérer un serveur de traitement de montée en puissance parallèle

Un serveur de traitement de montée en puissance parallèle fait office de coordinateur pour le transfert de données entre les services Site Recovery et votre infrastructure locale. Cet article explique comment installer, configurer et gérer un serveur de traitement de montée en puissance parallèle.

## <a name="prerequisites"></a>Prérequis
Vous trouverez ci-dessous la configuration recommandée pour le matériel, le logiciel et le réseau nécessaire pour configurer un serveur de traitement de montée en puissance parallèle.

> [!NOTE]
> La [planification de la capacité](site-recovery-capacity-planner.md) est une étape importante pour vous assurer de déployer le serveur de traitement de montée en puissance parallèle avec une configuration répondant à vos exigences de charge. En savoir plus sur les [caractéristiques de mise à l’échelle pour un serveur de traitement de montée en puissance parallèle](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Télécharger le logiciel du serveur de traitement de montée en puissance parallèle
1. Connectez-vous au portail Azure et accédez à votre coffre Recovery Services.
2. Sélectionnez **Infrastructure Site Recovery** > **Serveurs de configuration** (sous For VMware & Physical Machines [Pour VMware et machines physiques]).
3. Sélectionnez votre serveur de configuration pour explorer la page des détails du serveur de configuration.
4. Cliquez sur le bouton **+ Serveur de traitement**.
5. Dans la page **Ajouter un serveur de traitement**, sélectionnez l’option **Déployer un serveur de traitement de montée en puissance parallèle local** dans la liste déroulante **Choisissez où vous voulez déployer votre serveur de traitement**.

  ![Page Ajouter des serveurs](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Cliquez sur le lien **Download the Microsoft Azure Site Recovery Unified Setup (Télécharger le programme d’installation unifiée de Microsoft Azure Site Recovery)** pour télécharger la dernière version d’installation du serveur de traitement de montée en puissance parallèle.

  > [!WARNING]
  La version de votre serveur de traitement de montée en puissance parallèle doit être égale ou inférieure à la version du serveur de configuration en cours d’exécution dans votre environnement. Un moyen simple d’assurer la compatibilité de la version consiste à utiliser les mêmes bits de programme d’installation que ceux récemment utilisés pour installer/mettre à jour votre serveur de configuration.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Installation et inscription d’un serveur de traitement de montée en puissance parallèle à partir de l’interface graphique utilisateur
Si vous devez monter en charge votre déploiement au-delà de 200 machines source ou si votre taux d’évaluation quotidien total dépasse 2 To, vous avez besoin de serveurs de traitement supplémentaires pour gérer le volume de trafic.

Vérifiez les [tailles recommandées pour les serveurs de processus](#size-recommendations-for-the-process-server) et suivez les instructions ci-dessous pour configurer le serveur de processus. Après avoir configuré le serveur, vous pouvez migrer les ordinateurs source pour les utiliser.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Installation et inscription d’un serveur de traitement de montée en puissance parallèle à l’aide de la ligne de commande

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Exemple d’utilisation
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Arguments de la ligne de commande du programme d’installation du serveur de traitement de montée en puissance parallèle.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Créer un fichier de configuration des paramètres de proxy
Le paramètre ProxySettingsFilePath prend un fichier en tant qu’entrée. Créez le fichier au format suivant et transmettez-le comme paramètre ProxySettingsFilePath d’entrée.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Modification des paramètres de proxy pour le serveur de traitement de montée en puissance parallèle
1. Connectez-vous à votre serveur de traitement de montée en puissance parallèle.
2. Ouvrez une fenêtre de commandes PowerShell administrateur.
3. Exécutez la commande suivante
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Accédez ensuite au répertoire **%PROGRAMDATA%\ASR\Agent** et exécutez la commande suivante
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Réinscription d’un serveur de traitement de montée en puissance parallèle
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Ouvrez ensuite une invite de commandes administrateur.
* Accédez au répertoire **%PROGRAMDATA%\ASR\Agent**, puis exécutez la commande

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Mise à niveau d’un serveur de traitement de montée en puissance parallèle
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Désaffectation d’un serveur de traitement de montée en puissance parallèle
1. Assurez-vous que :
  - L’état de la connexion du serveur de configuration se présente comme **Connecté** dans le portail Azure
  - Le serveur de traitement est toujours en mesure de communiquer avec le serveur de configuration.
2. Connexion au serveur de traitement en tant qu’administrateur
3. Ouvrez le Panneau de configuration > Programme > Désinstaller des programmes
4. Désinstallez les programmes dans l’ordre donné suivant :
  * Serveur de configuration Microsoft Azure Site Recovery/Serveur de traitement
  * Dépendances du serveur de configuration Microsoft Azure Site Recovery
  * Agent Microsoft Azure Recovery Services

La suppression du serveur de traitement peut prendre jusqu’à 15 minutes pour être prise en compte dans le portail Azure.

  > [!NOTE]
  Si le serveur de traitement ne peut pas communiquer avec le serveur de configuration (l’état de la connexion dans le portail est Déconnecté), vous devez suivre les étapes suivantes pour le purger du serveur de configuration.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Désinscription d’un serveur de traitement de montée en puissance parallèle déconnecté d’un serveur de configuration

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Exigences de dimensionnement pour un serveur de traitement de montée en puissance parallèle

| **Serveur de traitement supplémentaire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- |
|4 processeurs virtuels (2 sockets * 2 cœurs @ 2,5 GHz), 8 Go de mémoire |300 Go |250 Go ou moins |Répliquez 85 machines ou moins. |
|8 processeurs virtuels (2 sockets * 4 cœurs @ 2,5 GHz), 12 Go de mémoire |600 Go |250 Go à 1 To |Répliquez entre 85 et 150 machines. |
|12 processeurs virtuels (2 sockets * 6 cœurs @ 2,5 GHz), 24 Go de mémoire |1 To |1 To à 2 To |Répliquez entre 150 et 225 machines. |
