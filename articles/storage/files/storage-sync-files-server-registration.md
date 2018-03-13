---
title: "Gérer des serveurs inscrits à Azure File Sync (préversion) | Microsoft Docs"
description: "Découvrez comment inscrire un serveur Windows Server au service de synchronisation de stockage Azure File Sync et le désinscrire."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: fcd79f25dee4ccaf674594222a6465fda137fd7a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Gérer des serveurs inscrits à Azure File Sync (préversion)
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

L’article suivant décrit comment inscrire un serveur au service de synchronisation de stockage et le gérer. Consultez [Guide pratique pour déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md) pour plus d’informations le déploiement de bout en bout d’Azure File Sync.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Inscrire/désinscrire un serveur au service de synchronisation de stockage
L’inscription d’un serveur à Azure File Sync établit une relation d’approbation entre Windows Server et Azure. Cette relation peut ensuite être utilisée pour créer des *points de terminaison de serveur* sur le serveur, qui représentent des dossiers spécifiques qui doivent être synchronisés avec un partage de fichiers Azure (également appelé un *point de terminaison de cloud*). 

### <a name="prerequisites"></a>Prérequis
Pour inscrire un serveur à un service de synchronisation de stockage, vous devez d’abord préparer votre serveur avec les prérequis nécessaires :

* Votre appareil doit exécuter une version prise en charge de Windows Server. Pour plus d’informations, consultez [Versions de Windows Server prises en charge](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Vérifiez qu’un service de synchronisation de stockage a été déployé. Pour plus d’informations sur le déploiement d’un service de synchronisation de stockage, consultez [Guide pratique pour déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).
* Vérifiez que le serveur est connecté à Internet et qu’Azure est accessible.
* Désactivez la Configuration de sécurité renforcée d’Internet Explorer pour les administrateurs avec l’interface utilisateur du Gestionnaire de serveur.
    
    ![Interface utilisateur du Gestionnaire de serveur avec la Configuration de sécurité renforcée d’Internet Explorer en surbrillance](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Vérifiez que le module AzureRM PowerShell est installé sur votre serveur. Si votre serveur est membre d’un cluster de basculement, chaque nœud du cluster nécessite le module AzureRM. Plus d’informations sur l’installation du module AzureRM sont disponibles dans [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Nous vous recommandons d’utiliser la version la plus récente du module AzureRM PowerShell pour inscrire/désinscrire un serveur. Si le package AzureRM a été installé précédemment sur ce serveur (et que la version de PowerShell sur ce serveur est 5.* ou supérieure), vous pouvez utiliser l’applet de commande `Update-Module` pour mettre à jour ce package. 
* Si vous utilisez un serveur proxy de réseau dans votre environnement, configurez les paramètres de proxy sur votre serveur que l’agent de synchronisation doit utiliser.
    1. Déterminez vos numéro de port et adresse IP de proxy
    2. Modifiez ces deux fichiers :
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Ajoutez les lignes dans la figure 1 (en dessous de cette section) sous /System.ServiceModel dans les deux fichiers ci-dessus en remplaçant 127.0.0.1:8888 par l’adresse IP appropriée (remplacez 127.0.0.1) et le numéro de port approprié (remplacez 8888) :
    4. Définissez les paramètres de proxy WinHTTP via la ligne de commande :
        * Affichez le proxy : netsh winhttp show proxy
        * Définissez le proxy : netsh winhttp set proxy 127.0.0.1:8888
        * Réinitialisez le proxy : netsh winhttp reset proxy
        * Si ces éléments sont configurés après l’installation de l’agent, redémarrez notre agent de synchronisation : net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Inscrire un serveur au service de synchronisation de stockage
Pour utiliser un serveur comme *point de terminaison de serveur* dans un *groupe de synchronisation* d’Azure File Sync, celui-ci doit être inscrit à un *service de synchronisation de stockage*. Un serveur peut être inscrit à un seul service de synchronisation de stockage à la fois.

#### <a name="install-the-azure-file-sync-agent"></a>Installer l’agent Azure File Sync
1. [Téléchargez l’agent Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Démarrez le programme d’installation de l’agent Azure File Sync.
    
    ![Première page du programme d’installation de l’agent Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Vous devez activer les mises à jour de l’agent Azure File Sync à l’aide de Microsoft Update. Cette étape est importante, car les correctifs de sécurité critiques et les améliorations de fonctionnalités pour le package du serveur sont fournis via Microsoft Update.

    ![Vérifiez que Microsoft Update est activé dans le volet Microsoft Update du programme d’installation de l’agent Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Si le serveur n’est pas déjà inscrit, l’interface utilisateur d’inscription de serveur s’affiche immédiatement une fois que l’installation est terminée.

> [!Important]  
> Si le serveur est membre d’un cluster de basculement, l’agent Azure File Sync doit être installé sur chaque nœud du cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Inscrire le serveur à l’aide de l’interface utilisateur d’inscription de serveur
> [!Important]  
> Les abonnements Fournisseur de solutions cloud (CSP) ne peuvent pas utiliser l’interface utilisateur d’inscription de serveur. Au lieu de cela, utilisez PowerShell (sous cette section).

1. Si l’interface utilisateur d’inscription de serveur n’a pas démarré immédiatement après la fin de l’installation de l’agent Azure File Sync, elle peut être démarrée manuellement en exécutant `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Cliquez sur *Connexion* pour accéder à votre abonnement Azure. 

    ![Ouverture de la boîte de dialogue de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Choisissez un abonnement, un groupe de ressources et un service de synchronisation de stockage dans la boîte de dialogue.

    ![Informations du service de synchronisation de stockage](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Dans la préversion, vous devez vous connecter une nouvelle fois pour terminer le processus. 

    ![Boîte de dialogue de connexion](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Si le serveur est membre d’un cluster de basculement, chaque serveur doit exécuter l’inscription de serveur. Lorsque vous affichez les serveurs inscrits dans le portail Azure, Azure File Sync reconnaît automatiquement chaque nœud comme un membre du même cluster de basculement et les regroupe en conséquence.

#### <a name="register-the-server-with-powershell"></a>Inscrire le serveur avec PowerShell
Vous pouvez également effectuer l’inscription du serveur via PowerShell. Il s’agit de la seule méthode d’inscription de serveur prise en charge pour les abonnements Fournisseur de solutions cloud (CSP) :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Désinscrire un serveur du service de synchronisation de stockage
Plusieurs étapes sont nécessaires pour désinscrire un serveur du service de synchronisation de stockage. Voyons comment désinscrire correctement un serveur.

#### <a name="optional-recall-all-tiered-data"></a>(Facultatif) Rappeler toutes les données hiérarchisées
Lorsque la hiérarchisation cloud est activée pour un point de terminaison de serveur, elle *hiérarchise* les fichiers dans des partages de fichiers Azure. Cela permet aux partages de fichiers locaux de faire office de cache, au lieu d’effectuer une copie complète du jeu de données, et d’utiliser efficacement l’espace sur le serveur de fichiers. Toutefois, si un point de terminaison de serveur est supprimé alors que des fichiers hiérarchisés sont toujours localement sur le serveur, ces fichiers deviennent inaccessibles. Par conséquent, si vous voulez un accès continu aux fichiers, vous devez rappeler tous les fichiers hiérarchisés dans Azure Files avant de poursuivre la désinscription. 

Cette opération peut être effectuée avec l’applet de commande PowerShell comme indiqué ci-dessous :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si le volume local qui héberge le point de terminaison de serveur n’a pas assez d’espace libre pour rappeler toutes les données hiérarchisées, l’applet de commande `Invoke-StorageSyncFileRecall` échoue.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Supprimer le serveur de tous les groupes de synchronisation
Avant de désinscrire le serveur du service de synchronisation de stockage, tous les points de terminaison de serveur sur ce serveur doivent être supprimés. Vous pouvez le faire dans le portail Azure :

1. Accédez au service de synchronisation de stockage auquel votre serveur est inscrit.
2. Supprimez tous les points de terminaison de serveur pour ce serveur dans chaque groupe de synchronisation du service de synchronisation de stockage. Pour ce faire, vous pouvez cliquer avec le bouton droit sur le point de terminaison de serveur concerné dans le volet Groupe de synchronisation.

    ![Suppression d’un point de terminaison de serveur d’un groupe de synchronisation](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Cette opération peut également être effectuée avec un script PowerShell simple :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Désinscrire le serveur
À présent que toutes les données ont été rappelées et que le serveur a été supprimé de tous les groupes de synchronisation, il peut être désinscrit. 

1. Dans le portail Azure, accédez à la section *Serveurs inscrits* du service de synchronisation de stockage.
2. Cliquez avec le bouton droit sur le serveur que vous voulez désinscrire et cliquez sur « Désinscrire le serveur ».

    ![Désinscrire le serveur](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>S’assurer qu’Azure File Sync Synchronisation est un voisin approprié dans votre centre de données 
Étant donné qu’Azure File Sync sera rarement le seul service en cours d’exécution dans votre centre de données, vous voudrez peut-être limiter l’utilisation du réseau et de stockage d’Azure File Sync.

> [!Important]  
> La définition de limites trop faibles aura un impact sur les performances de synchronisation et de rappel d’Azure File Sync.

### <a name="set-azure-file-sync-network-limits"></a>Définir des limites réseau d’Azure File Sync
Vous pouvez limiter l’utilisation du réseau d’Azure File Sync en utilisant les cmdlets `StorageSyncNetworkLimit`. 

Par exemple, créez une nouvelle limite pour vous assurer qu’Azure File Sync n’utilise pas plus de 10 Mbits/s entre 9h et 17h en semaine : 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Vous pouvez voir votre limite à l’aide de l’applet de commande suivante :

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Pour supprimer des limites réseau, utilisez `Remove-StorageSyncNetworkLimit`. Par exemple, la commande suivante supprime toutes les limites réseau :

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Utiliser la Qualité de service (QoS) de Windows Server 
Lorsqu’Azure File Sync est hébergé dans une machine virtuelle s’exécutant sur un hôte de virtualisation Windows Server, vous pouvez utiliser la QoS de stockage (qualité de service de stockage) pour contrôler la consommation d’E/S de stockage. La stratégie de QoS de stockage peut être définie comme une valeur maximale (ou limite, comme la limite StorageSyncNetwork appliquée ci-dessus) ou minimale (ou réservation). La définition d’une valeur minimale plutôt que maximale permet à Azure File Sync d’optimiser l’utilisation de la bande passante de stockage disponible si d’autres charges de travail ne l’utilisent pas. Pour plus d’informations, consultez [Qualité de service de stockage](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Voir aussi
- [Planification d’un déploiement Azure File Sync (préversion)](storage-sync-files-planning.md)
- [Déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md) 
- [Résoudre les problèmes de synchronisation de fichiers Azure (préversion)](storage-sync-files-troubleshoot.md)
