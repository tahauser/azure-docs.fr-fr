---
title: " Gérer le serveur de configuration pour la récupération d’urgence de VMware avec Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment gérer un serveur de configuration existant pour la récupération d’urgence de VMware sur Azure, à l’aide du service Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2018
ms.author: anoopkv
ms.openlocfilehash: 9cdabfb4e24423d76e4f247f184ac4156c3b257b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-the-configuration-server"></a>Gérer le serveur de configuration

Vous configurez un serveur de configuration local quand vous utilisez le service [Azure Site Recovery](site-recovery-overview.md) pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure. Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure, et gère la réplication des données. Cet article résume les tâches courantes de gestion du serveur de configuration après son déploiement.

## <a name="modify-vmware-settings"></a>Modifier les paramètres de VMware

Modifiez les paramètres du serveur VMware auquel se connecte le serveur de configuration.

1. Connectez-vous à la machine exécutant le serveur de configuration.
2. Lancez le gestionnaire de configuration d’Azure Site Recovery en utilisant le raccourci du Bureau ou en accédant à la page **https://configuration-server-name/IP:44315**.
3. Cliquez sur **Gérer le serveur vCenter Server/vSPhere ESXi** :
    - Pour associer un autre serveur VMware au serveur de configuration, cliquez sur **Ajouter un serveur vCenter Server/vSphere ESXi** et entrez les détails du serveur.
    - Pour mettre à jour les informations d’identification à utiliser pour la connexion au serveur VMware et la détection automatique des machines virtuelles VMware, cliquez sur **Modifier**. Entrez les nouvelles informations d'identification, puis cliquez sur **OK**.

        ![Modification de VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Modifier les informations d’identification pour l’installation du service Mobilité

Modifiez les informations d’identification à utiliser pour installer automatiquement le service Mobilité sur les machines virtuelles VMware sur lesquelles vous activez la réplication.

1. Connectez-vous à la machine exécutant le serveur de configuration.
2. Lancez le gestionnaire de configuration d’Azure Site Recovery en utilisant le raccourci du Bureau ou en accédant à la page **https://configuration-server-name/IP:44315**.
3. Cliquez sur **Gérer les informations d'identification de machine virtuelle** et entrez les nouvelles informations d’identification. Cliquez ensuite sur **OK** pour mettre à jour les paramètres.

    ![Modification des informations d’identification du service Mobilité](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modifier les paramètres du proxy

Modifiez les paramètres du proxy que la machine serveur de configuration doit utiliser pour l’accès Internet à Azure. Si vous disposez d’une machine serveur de processus supplémentaire, en plus du serveur de processus par défaut exécuté sur la machine serveur de configuration, modifiez les paramètres sur les deux machines.

1. Connectez-vous à la machine exécutant le serveur de configuration.
2. Lancez le gestionnaire de configuration d’Azure Site Recovery en utilisant le raccourci du Bureau ou en accédant à la page **https://configuration-server-name/IP:44315**.
3. Cliquez sur **Gérer la connectivité** et mettez à jour les valeurs du proxy. Cliquez ensuite sur **Enregistrer** pour mettre à jour les paramètres.

## <a name="add-a-network-adapter"></a>Ajouter une carte réseau

Le modèle OVF déploie la machine virtuelle du serveur de configuration avec une seule carte réseau. Vous pouvez [ajouter une autre carte réseau à la machine virtuelle](how-to-deploy-configuration-server.md#add-an-additional-adapter), mais vous devez le faire avant d’inscrire le serveur de configuration dans le coffre.

Si vous souhaitez ajouter une carte réseau après l’inscription du serveur de configuration dans le coffre, vous devez l’ajouter dans les propriétés de la machine virtuelle, puis réinscrire le serveur dans le coffre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Réinscrire un serveur de configuration dans le même coffre

Vous pouvez réinscrire le serveur de configuration dans le même coffre, si nécessaire. Si vous disposez d’une machine serveur de processus supplémentaire, en plus du serveur de processus par défaut exécuté sur la machine serveur de configuration, réinscrivez les deux machines.

  1. Dans le coffre, cliquez sur **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**.
  2. Sous **Serveurs**, cliquez sur **Télécharger une clé d'inscription** pour télécharger le fichier d’informations d’identification du coffre.
  3. Connectez-vous à la machine serveur de configuration.
  4. Sous **%ProgramData%\ASR\home\svagent\bin**, ouvrez le fichier **cspsconfigtool.exe**.
  5. Dans l’onglet **Inscription du coffre**, cliquez sur Parcourir et recherchez le fichier d’informations d’identification du coffre que vous avez téléchargé.
  6. Si nécessaire, fournissez les détails du serveur proxy. Cliquez ensuite sur **S’inscrire**.
  7. Ouvrez une fenêtre de commande d’administration PowerShell et exécutez la commande suivante :

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```
## <a name="upgrade-the-configuration-server"></a>Mettre à niveau le serveur de configuration

Vous exécutez des correctifs cumulatifs pour mettre à jour le serveur de configuration. Les mises à jour peuvent être appliquées jusqu’aux versions N-4. Par exemple : 

- Si vous exécutez la version 9.7, 9.8, 9.9 ou 9.10, vous pouvez mettre à niveau directement vers la version 9.11.
- Si vous exécutez la version 9.6 ou une version antérieure, et souhaitez mettre à niveau vers la version 9.11, vous devez tout d’abord mettre à niveau vers la version 9.7 avant la version 9.11.

Des liens vers des correctifs cumulatifs pour la mise à niveau de toutes les versions du serveur de configuration sont disponibles dans la [page wiki relative aux mises à jour](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Mettez à niveau le serveur comme suit :

1. Téléchargez le fichier du programme d’installation des mises à jour sur le serveur de configuration.
2. Double-cliquez pour exécuter le programme d’installation.
3. Le programme d’installation détecte la version actuelle en cours d’exécution sur la machine.
4. Cliquez sur **OK** pour confirmer, puis exécutez la mise à niveau. 


## <a name="delete-or-unregister-a-configuration-server"></a>Supprimer un serveur de configuration ou annuler son inscription

1. [Désactivez la protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) de toutes les machines virtuelles relevant de ce serveur de configuration.
2. [Dissociez](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) et [supprimez](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) toutes les stratégies de réplication du serveur de configuration.
3. [Supprimez](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) tous les serveurs vCenter/hôtes vSphere associés au serveur de configuration.
4. Dans le coffre, cliquez sur **Infrastructure Site Recovery** > **Serveurs de configuration**.
5. Cliquez sur le serveur de configuration que vous souhaitez supprimer. Puis, sur la page **Détails**, cliquez sur **Supprimer**.

    ![Suppression d’un serveur de configuration](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Supprimer avec PowerShell

Vous pouvez également supprimer le serveur de configuration à l’aide de PowerShell :

1. [Installez](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) le module Azure PowerShell.
2. Connectez-vous à votre compte Azure à l’aide de la commande suivante :
    
    `Login-AzureRmAccount`
3. Sélectionnez l’abonnement du coffre :

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Définissez le contexte du coffre :
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Récupérez le serveur de configuration :

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Supprimez le serveur de configuration :

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Vous pouvez utiliser l’option **-Force** dans Remove-AzureRmSiteRecoveryFabric pour forcer la suppression du serveur de configuration.
 


## <a name="renew-ssl-certificates"></a>Renouveler les certificats SSL

Le serveur de configuration possède un serveur web intégré, qui orchestre les activités du service Mobilité, des serveurs de processus et des serveurs maîtres cibles connectés à celui-ci. Le serveur web utilise un certificat SSL pour authentifier les clients. Le certificat expire au bout de trois ans et peut être renouvelé à tout moment.

### <a name="check-expiry"></a>Vérifier la date d’expiration

Pour les déploiements de serveurs de configuration effectués avant mai 2016, le certificat expire au bout d’un an. Si votre certificat est sur le point d’expirer :

- Deux mois (ou moins) avant la date d’expiration, le service commence à envoyer des notifications via le portail et par e-mail (si vous avez souscrit aux notifications d’Azure Site Recovery).
- Une bannière de notification s’affiche sur la page de ressources du coffre. Cliquez sur la bannière pour plus de détails.
- Si un bouton **Mettre à niveau maintenant** apparaît, il indique que certains composants de votre environnement n’ont pas été mis à niveau vers 9.4.xxxx.x ou une version supérieure. Mettez à niveau les composants avant de renouveler le certificat. Vous ne pouvez pas renouveler de versions antérieures.

### <a name="renew-the-certificate"></a>Renouveler le certificat

1. Dans le coffre, accédez à **Infrastructure Site Recovery** > **Serveur de configuration**, puis cliquez sur le serveur de configuration requis.
2. La date d’expiration est indiquée sous **Intégrité de Configuration Server**.
3. Cliquez sur **Renouveler les certificats**. 


## <a name="next-steps"></a>étapes suivantes

Consultez les didacticiels pour en savoir plus sur la configuration de la récupération d’urgence de [machines virtuelles VMware](tutorial-vmware-to-azure.md) et de serveurs physiques (tutorial-physical-to-azure.md) sur Azure.
