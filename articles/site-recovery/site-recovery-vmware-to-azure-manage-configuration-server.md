---
title: " Gérer un serveur de configuration dans Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment définir et gérer un serveur de configuration."
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
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 4f23750ff1ba261ea3cf782f7c85858e46632cfa
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="manage-a-configuration-server"></a>Gérer un serveur de configuration

Le serveur de configuration fait office de coordinateur entre les services Site Recovery et votre infrastructure locale. Cet article explique comment configurer et gérer le serveur de configuration.

> [!NOTE]
> La [planification de la capacité](site-recovery-capacity-planner.md) est une étape importante pour vous assurer de déployer le serveur de configuration avec une configuration répondant à vos exigences de charge. Pour en savoir plus, consultez la section [Exigences de dimensionnement d’un serveur de configuration](#sizing-requirements-for-a-configuration-server).


## <a name="prerequisites"></a>Composants requis
Vous trouverez ci-dessous des informations sur la configuration minimale requise pour le matériel, le logiciel et le réseau pour configurer un serveur de configuration.
> [!IMPORTANT]
> Quand vous déployez un serveur de configuration pour protéger des machines virtuelles VMware, nous vous recommandons de le déployer en tant que machine virtuelle **hautement disponible**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Téléchargement du logiciel serveur de configuration

1. Connectez-vous au portail Azure et accédez à votre coffre Recovery Services.
2. Sélectionnez **Infrastructure Site Recovery** > **Serveurs de configuration** (sous For VMware & Physical Machines [Pour VMware et machines physiques]).

  ![Page Ajouter des serveurs](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Cliquez sur le bouton **+Serveurs**.
4. Dans la page **Ajouter un serveur** , cliquez sur le bouton Télécharger pour télécharger la clé d’inscription. Vous avez besoin de cette clé pendant l’installation du serveur de configuration pour l’inscrire auprès du service Azure Site Recovery.
5. Cliquez sur le lien **Download the Microsoft Azure Site Recovery Unified Setup (Télécharger le programme d’installation unifiée de Microsoft Azure Site Recovery)** pour télécharger la dernière version du serveur de configuration.

  ![Page de téléchargement](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  La dernière version du serveur de configuration peut être téléchargée directement à partir de la [page de téléchargement du Centre de téléchargement Microsoft](http://aka.ms/unifiedsetup).

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Installation et inscription d’un serveur de configuration à partir de l’interface graphique utilisateur
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Installation et inscription d’un serveur de configuration à l’aide de la ligne de commande

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exemple d’utilisation
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Arguments de la ligne de commande du programme d’installation du serveur de configuration
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Créer un fichier d’informations d’identification de MySql
Le paramètre MySQLCredsFilePath prend un fichier en tant qu’entrée. Créez le fichier au format suivant et transmettez-le comme paramètre MySQLCredsFilePath d’entrée.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Créer un fichier de configuration des paramètres de proxy
Le paramètre ProxySettingsFilePath prend un fichier en tant qu’entrée. Créez le fichier au format suivant et transmettez-le comme paramètre ProxySettingsFilePath d’entrée.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Modification des paramètres de proxy du serveur de configuration
1. Connectez-vous à votre serveur de configuration.
2. Lancez l’exécutable cspsconfigtool.exe à l’aide du raccourci sur votre serveur.
3. Cliquez sur l’onglet **Vault Registration (Inscription du coffre)**.
4. Téléchargez un nouveau fichier d’inscription du coffre à partir du portail et indiquez-le comme entrée de l’outil.

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Indiquez les détails du nouveau serveur proxy, puis cliquez sur le bouton **Inscrire**.
6. Ouvrez une fenêtre de commandes PowerShell administrateur.
7. Exécutez la commande suivante
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Si vous avez attaché des serveurs de processus de montée en puissance parallèle à ce serveur de configuration, vous devez [corriger les paramètres de proxy sur tous les serveurs de processus de montée en puissance parallèle](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) de votre déploiement.

## <a name="modify-user-accounts-and-passwords"></a>Modifier les comptes d’utilisateurs et mots de passe

L’outil CSPSConfigTool.exe est utilisé pour gérer les comptes d’utilisateurs utilisés pour la **détection automatique des machines virtuelles VMware** et effectuer **l’installation Push du service Mobilité sur les ordinateurs protégés. 

1. Connectez-vous à votre serveur de configuration.
2. Lancez CSPSConfigtool.exe en cliquant sur le raccourci sur le bureau.
3. Cliquez sur l’onglet **Gérer les comptes**.
4. Sélectionnez le compte pour lequel le mot de passe doit être modifié, puis cliquez sur le bouton **Modifier**.
5. Entrez le nouveau mot de passe et cliquez sur **OK**


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Réinscription d’un serveur de configuration auprès du même coffre Recovery Services
  1. Connectez-vous à votre serveur de configuration.
  2. Lancez l’exécutable cspsconfigtool.exe à l’aide du raccourci sur votre bureau.
  3. Cliquez sur l’onglet **Vault Registration (Inscription du coffre)**.
  4. Téléchargez un nouveau fichier d’inscription à partir du portail et indiquez-le comme entrée de l’outil.
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. Indiquez les détails du serveur proxy, puis cliquez sur le bouton **Inscrire**.  
  6. Ouvrez une fenêtre de commandes PowerShell administrateur.
  7. Exécutez la commande suivante

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Si vous avez attaché des serveurs de processus de montée en puissance parallèle à ce serveur de configuration, vous devez [réinscrire tous les serveurs de processus de montée en puissance parallèle](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) de votre déploiement.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Inscription d’un serveur de configuration auprès d’un autre coffre Recovery Services.

> [!WARNING]
> L’étape suivante dissocie la configuration du coffre actuel, entraînant l’arrêt de la réplication de toutes les machines virtuelles protégées sous le serveur de configuration.

1. Connectez-vous à votre serveur de configuration.
2. À partir d’une invite de commandes d’administration, exécutez la commande indiquée ci-dessous.

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Lancez l’exécutable cspsconfigtool.exe à l’aide du raccourci sur votre serveur.
4. Cliquez sur l’onglet **Vault Registration (Inscription du coffre)**.
5. Téléchargez un nouveau fichier d’inscription à partir du portail et indiquez-le comme entrée de l’outil.

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Indiquez les détails du serveur proxy, puis cliquez sur le bouton **Inscrire**.  
7. Ouvrez une fenêtre de commandes PowerShell administrateur.
8. Exécutez la commande suivante
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrading-a-configuration-server"></a>Mise à niveau d’un serveur de configuration

> [!WARNING]
> Les mises à jour sont prises en charge jusqu’à la version N+4 uniquement. Par exemple, si la dernière version sur le marché est 9.11, vous pouvez alors mettre à jour de la version 9.10, 9.9, 9.8 ou 9.7 directement vers la version 9.11. Cependant, si vous utilisez une version inférieure ou égale à 9.6, vous devez mettre à jour vers la version 9.7 minimum pour pouvoir appliquer les dernières mises à jour à votre serveur de configuration. Les liens de téléchargement de la version précédente sont disponibles sous [Mises à jour du service Azure Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

1. Téléchargez le programme d’installation de mise à jour sur votre serveur de configuration.
2. Exécutez le programme d’installation en double-cliquant sur celui-ci.
3. Le programme d’installation détecte la version des composants de Site Recovery présents sur l’ordinateur et demande une confirmation. 
4. Cliquez sur le bouton OK pour confirmer et continuer la mise à niveau.


## <a name="delete-or-unregister-a-configuration-server"></a>Supprimer un serveur de configuration ou annuler son inscription

> [!WARNING]
> Effectuez les opérations suivantes avant de désaffecter votre serveur de configuration.
> 1. [Désactivez la protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) de toutes les machines virtuelles relevant de ce serveur de configuration.
> 2. [Dissociez ](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) et [supprimez](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) toutes les stratégies de réplication du serveur de configuration.
> 3. [Supprimez](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) tous les serveurs vCenter/hôtes vSphere associés au serveur de configuration.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Supprimer le serveur de configuration du portail Azure
1. Dans le portail Azure, sélectionnez **Infrastructure Site Recovery** > **Serveurs de configuration** dans le menu Coffre.
2. Cliquez sur le serveur de configuration que vous souhaitez désaffecter.
3. Dans la page des détails du serveur de configuration, cliquez sur le bouton Supprimer.

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Cliquez sur **Oui** pour confirmer la suppression du serveur.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Désinstaller le logiciel serveur de configuration et ses dépendances
  > [!TIP]
  Si vous envisagez de réutiliser le serveur de configuration avec Azure Site Recovery, vous pouvez passer directement à l’étape 4.

1. Connectez-vous au serveur de configuration en tant qu’administrateur.
2. Ouvrez le Panneau de configuration > Programme > Désinstaller des programmes.
3. Désinstallez les programmes dans la séquence suivante :
  * Agent Microsoft Azure Recovery Services
  * Service Mobilité/Serveur cible maître Microsoft Azure Site Recovery
  * Fournisseur Microsoft Azure Site Recovery
  * Serveur de configuration/Serveur de processus Microsoft Azure Site Recovery
  * Dépendances du serveur de configuration Microsoft Azure Site Recovery
  * MySQL Server 5.5
4. Exécutez la commande suivante à partir d’une invite de commandes d’administration :
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Supprimer un serveur de configuration ou annuler son inscription (PowerShell)

1. [Installez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) le module Azure PowerShell.
2. Connectez-vous à votre compte Azure à l’aide de la commande suivante :
    
    `Login-AzureRmAccount`
3. Sélectionnez l’abonnement sous lequel le coffre est présent.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Configurez votre contexte de coffre.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Sélectionnez votre serveur de configuration.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Supprimez le serveur de configuration.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> L’option **-Force** dans Remove-AzureRmSiteRecoveryFabric peut être utilisée pour forcer la suppression du serveur de configuration.

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Renouveler les certificats SSL du serveur de configuration
Le serveur de configuration possède un serveur web intégré, qui orchestre les activités du service Mobilité, des serveurs de processus et des serveurs maîtres cibles connectés au serveur de configuration. Le serveur web du serveur de configuration utilise un certificat SSL pour authentifier ses clients. Ce certificat a un délai d’expiration de trois ans et peut être renouvelé à tout moment à l’aide de la méthode suivante :

> [!WARNING]
L’expiration du certificat peut être appliquée uniquement sur les versions 9.4.XXXX.X ou supérieures. Mettez à niveau tous les composants Azure Site Recovery (serveur de configuration, serveur de processus, serveur cible maître, service Mobilité) avant de commencer le flux de travail de renouvellement des certificats.

1. Dans le portail Azure, sélectionnez votre Coffre > Infrastructure Site Recovery > Serveur de configuration.
2. Cliquez sur le serveur de configuration pour lequel vous devez renouveler le certificat SSL.
3. Sous Intégrité du serveur de configuration, vous pouvez observer la date d’expiration du certificat SSL.
4. Renouvelez le certificat en cliquant sur l’action **Renouveler les certificats** comme illustré ci-après :

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Avertissement relatif à l’expiration du certificat SSL

> [!NOTE]
La validité du certificat SSL de toutes les installations qui ont été effectuées avant mai 2016 a été définie sur un an. Vous avez commencé à voir des notifications d’expiration de certificat s’afficher dans le portail Azure.

1. Si le certificat SSL du serveur de configuration arrive à expiration dans les deux prochains mois, le service commence à en informer les utilisateurs via le portail Azure et la messagerie électronique (vous devez être abonné aux notifications Azure Site Recovery). Une bannière de notification est affichée dans la page des ressources du coffre.

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Cliquez sur la bannière pour obtenir des informations supplémentaires sur l’expiration du certificat.

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Si un bouton **Mettre à niveau maintenant** s’affiche à la place d’un bouton **Renouveler maintenant**, Le bouton Mettre à niveau maintenant indique que certains composants de votre environnement n’ont pas encore été mis à niveau vers 9.4.xxxx.x ou supérieur.

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>Réactiver un serveur de configuration si le certificat SSL a expiré

1. Mettre à jour votre serveur de configuration vers la [version la plus récente](http://aka.ms/unifiedinstaller)
2. Si vous avez des serveurs de traitement avec montée en puissance parallèle, des serveurs cibles maîtres de restauration automatique et des serveurs de traitement de restauration automatique, mettez-les à jour vers la dernière version.
3. Mettez à jour le service Mobilité sur tous les ordinateurs virtuels protégés vers la dernière version.
4. Connectez-vous au serveur de configuration et ouvrez une invite de commandes avec des privilèges d’administrateur.
5. Recherchez le dossier %ProgramData%\ASR\home\svsystems\bin.
6. Exécutez RenewCerts.exe pour renouveler le certificat SSL sur le serveur de configuration.
7. Si l’opération réussit, vous devez voir un message similaire à « Renouvellement du certificat réussi ».


## <a name="sizing-requirements-for-a-configuration-server"></a>Exigences de dimensionnement d’un serveur de configuration

| **UC** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- | --- |
| 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) |16 Go |300 Go |500 Go ou moins |Répliquez moins de 100 machines. |
| 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) |18 Go |600 Go |500 Go à 1 To |Répliquez entre 100 et 150 machines. |
| 16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) |32 Go |1 To |1 To à 2 To |Répliquez entre 150 et 200 machines. |

  >[!TIP]
  Si l’activité quotidienne des données excède 2 To ou que vous envisagez de répliquer plus de 200 machines virtuelles, il est recommandé de déployer d’autres serveurs de processus pour équilibrer la charge du trafic de réplication. Pour en savoir plus, consultez l’article How to deploy Scale-out Process servers (Déploiement des serveurs de processus de montée en puissance parallèle).


## <a name="common-issues"></a>Problèmes courants
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]
