---
title: Planification d’un déploiement Azure File Sync (préversion) | Microsoft Docs
description: Découvrez les éléments à prendre en compte lors de la planification d’un déploiement Azure Files.
services: storage
documentationcenter: ''
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
ms.openlocfilehash: 5f42bee31e3bc1a23c9b0c6de9d6748e23c94713
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planification d’un déploiement Azure File Sync (préversion)
Utilisez Azure File Sync (préversion) pour centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article décrit les points importants à prendre en compte pour le déploiement d’Azure File Sync. Nous vous recommandons de lire aussi l’article [Planification d’un déploiement Azure Files](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologie Azure File Sync
Avant de voir en détail la planification d’un déploiement Azure File Sync, il est important de bien comprendre la terminologie employée.

### <a name="storage-sync-service"></a>Service de synchronisation de stockage
Le service de synchronisation de stockage est la ressource Azure de niveau supérieur pour Azure File Sync. La ressource de service de synchronisation de stockage est un pair de la ressource de compte de stockage. Elle peut être déployée de la même façon dans des groupes de ressources Azure. Une ressource de niveau supérieur distincte de la ressource de compte de stockage est nécessaire, car le service de synchronisation de stockage peut créer des relations de synchronisation avec plusieurs comptes de stockage dans plusieurs groupes de synchronisation. Un abonnement peut avoir plusieurs ressources de service de synchronisation de stockage déployées.

### <a name="sync-group"></a>Groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Par exemple, si vous voulez gérer deux ensembles distincts de fichiers avec Azure File Sync, vous créez deux groupes de synchronisation et ajoutez des points de terminaison différents dans chacun de ces groupes. Un service de synchronisation de stockage peut héberger autant de groupes de synchronisation que nécessaire.  

### <a name="registered-server"></a>Serveur inscrit
L’objet serveur inscrit représente une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. Vous pouvez inscrire autant de serveurs que vous souhaitez auprès d’une instance du service de synchronisation de stockage. Toutefois, un serveur (ou cluster) peut être inscrit uniquement auprès d’un seul service de synchronisation de stockage à la fois.

### <a name="azure-file-sync-agent"></a>Agent Azure File Sync
L’agent Azure File Sync est un package téléchargeable qui permet à Windows Server de rester synchronisé avec un partage de fichiers Azure. L’agent Azure File Sync a trois composants principaux : 
- **FileSyncSvc.exe** : service Windows en arrière-plan qui gère la surveillance des changements sur les points de terminaison de serveur, ainsi que le démarrage des sessions de synchronisation dans Azure.
- **StorageSync.sys** : filtre du système de fichiers Azure File Sync qui gère la hiérarchisation des fichiers dans Azure Files (quand la hiérarchisation cloud est activée).
- **Applets de commande de gestion PowerShell** : applets de commande PowerShell qui vous permettent d’interagir avec le fournisseur de ressources Azure Microsoft.StorageSync. Ces composants se trouvent aux emplacements (par défaut) suivants :
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Point de terminaison de serveur
Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur. Plusieurs points de terminaison de serveur peuvent se trouver sur le même volume si leurs espaces de noms ne se chevauchent pas (par exemple, `F:\sync1` et `F:\sync2`). Vous pouvez configurer des stratégies de hiérarchisation cloud individuellement pour chaque point de terminaison de serveur. Actuellement, vous ne pouvez pas créer de point de terminaison de serveur pour la racine d’un volume (par exemple `F:\` ou `C:\myvolume`, si un volume est monté comme point de montage).

> [!Note]  
> Seuls les volumes non amovibles sont pris en charge.  Les lecteurs mappés à partir d’un partage distant ne sont pas pris en charge pour un chemin d’accès au point de terminaison du serveur.  En outre, un point de terminaison de serveur peut se trouver sur le volume système Windows, bien que la hiérarchisation cloud ne soit pas prise en charge sur le volume système.

Si vous ajoutez un emplacement de serveur contenant un ensemble de fichiers comme point de terminaison de serveur à un groupe de synchronisation, ces fichiers sont fusionnés avec les autres fichiers déjà présents sur les autres points de terminaison dans le groupe de synchronisation.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
Un point de terminaison cloud est un partage de fichiers Azure qui fait partie d’un groupe de synchronisation. L’intégralité du partage de fichiers Azure se synchronise. Un partage de fichiers Azure peut être membre d’un seul point de terminaison cloud. Un partage de fichiers Azure peut donc être membre d’un seul groupe de synchronisation. Si vous ajoutez un partage de fichiers Azure contenant un ensemble de fichiers existants comme point de terminaison cloud à un groupe de synchronisation, ces fichiers sont fusionnés avec les autres fichiers déjà présents sur les autres points de terminaison dans le groupe de synchronisation.

> [!Important]  
> Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud toutes les 24 heures uniquement. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Hiérarchisation cloud 
La hiérarchisation cloud est une fonctionnalité facultative d’Azure File Sync qui permet de hiérarchiser dans Azure Files les fichiers rarement utilisés ou les fichiers avec une taille supérieure à 64 Kio. Quand un fichier est hiérarchisé, le filtre du système de fichiers Azure File Sync (StorageSync.sys) remplace le fichier local par un pointeur, ou point d’analyse. Le point d’analyse représente une URL vers le fichier dans Azure Files. Un fichier hiérarchisé a l’attribut « hors connexion » défini dans NTFS, ce qui permet aux applications tierces de l’identifier. Quand un utilisateur ouvre un fichier hiérarchisé, Azure File Sync rappelle les données du fichier d’Azure Files de manière transparente, sans que l’utilisateur ait besoin de savoir que le fichier n’est pas stocké localement sur le système. Cette fonctionnalité est également appelée Gestion hiérarchique du stockage.

> [!Important]  
> La hiérarchisation cloud n’est pas prise en charge pour les points de terminaison de serveur sur les volumes système Windows.

## <a name="azure-file-sync-interoperability"></a>Interopérabilité d’Azure File Sync 
Cette section traite de l’interopérabilité d’Azure File Sync avec les fonctionnalités et rôles Windows Server, et avec les solutions tierces.

### <a name="supported-versions-of-windows-server"></a>Versions de Windows Server prises en charge
Les versions de Windows Server prises en charge par Azure File Sync sont les suivantes :

| Version | Références prises en charge | Options de déploiement prises en charge |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter et Standard | Complète (serveur avec une interface utilisateur) |
| Windows Server 2012 R2 | Datacenter et Standard | Complète (serveur avec une interface utilisateur) |

Nous prévoyons d’ajouter la prise en charge de versions ultérieures de Windows Server quand elles seront disponibles. Nous sommes susceptibles d’ajouter la prise en charge de versions antérieures de Windows Server en réponse aux commentaires des utilisateurs.

> [!Important]  
> Nous vous recommandons de mettre régulièrement à jour tous les serveurs que vous utilisez avec Azure File Sync en installant les dernières mises à jour disponibles sur Windows Update. 

### <a name="file-system-features"></a>Fonctionnalités du système de fichiers
| Fonctionnalité | État de la prise en charge | Notes |
|---------|----------------|-------|
| Listes ACL | Entièrement pris en charge | Les listes ACL Windows sont conservées par Azure File Sync et appliquées par Windows Server sur les points de terminaison de serveur. Les listes ACL Windows ne sont pas (encore) prises en charge par Azure Files si les fichiers sont accessibles directement dans le cloud. |
| Liens physiques | Ignoré | |
| Liens symboliques | Ignoré | |
| Points de montage | Partiellement pris en charge | Les points de montage peuvent être la racine d’un point de terminaison de serveur, mais ils sont ignorés s’ils se trouvent dans un espace de noms du point de terminaison de serveur. |
| Jonctions | Ignoré | Par exemple, les dossiers DfrsrPrivate de système de fichiers DFS et DFSRoots. |
| Points d’analyse | Ignoré | |
| Compression NTFS | Entièrement pris en charge | |
| Fichiers partiellement alloués | Entièrement pris en charge | Les fichiers partiellement alloués sont synchronisés (ils ne sont pas bloqués), mais ils sont synchronisés dans le cloud sous forme de fichiers complets. Si le contenu d’un fichier change dans le cloud (ou sur un autre serveur), le fichier n’est plus partiellement alloué quand le changement est téléchargé. |
| Autres flux de données | Conservés, mais pas synchronisés | Par exemple, les balises de classification créées par l’infrastructure de classification des fichiers ne sont pas synchronisées. Les balises de classification qui existent sur des fichiers à chacun des points de terminaison du serveur ne sont pas affectées. |

> [!Note]  
> Seuls les volumes NTFS sont pris en charge. ReFS, FAT, FAT32 et les autres systèmes de fichiers ne sont pas pris en charge.

### <a name="failover-clustering"></a>Clustering de basculement
Le clustering de basculement Windows Server est pris en charge par Azure File Sync pour l’option de déploiement « Serveur de fichiers pour une utilisation générale ». Le clustering de basculement n’est pas pris en charge sur le « serveur de fichiers avec montée en puissance parallèle pour les données d’application » (SOFS) ou sur les volumes partagés de cluster (CSV).

> [!Note]  
> L’agent Azure File Sync doit être installé sur chaque nœud d’un cluster de basculement pour que la synchronisation fonctionne correctement.

### <a name="data-deduplication"></a>Déduplication des données
Pour les volumes sur lesquels la hiérarchisation cloud n’est pas activée, Azure File Sync prend en charge la déduplication des données Windows Server quand elle est activée sur le volume. L’interopérabilité entre Azure File Sync avec hiérarchisation cloud et la déduplication des données n’est pas actuellement prise en charge.

### <a name="distributed-file-system-dfs"></a>Système de fichiers DFS
Azure File Sync prend en charge l’interopérabilité avec les espaces de noms DFS (DFS-N) et la réplication DFS (DFS-R) à partir de l’[agent Azure File Sync 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**Espaces de noms DFS (DFS-N)** : Azure File Sync est entièrement pris en charge sur les serveurs DFS-N. Vous pouvez installer l’agent Azure File Sync sur un ou plusieurs membres DFS-N pour synchroniser des données entre les points de terminaison de serveur et le point de terminaison cloud. Pour plus d’informations, consultez [Vue d’ensemble des espaces de noms DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Réplication DFS (DFS-R)** : étant donné que DFS-R et Azure File Sync sont deux solutions de réplication, dans la plupart des cas, nous recommandons de remplacer le DFS-R par Azure File Sync. Il existe cependant plusieurs scénarios où vous souhaiterez utiliser DFS-R et Azure File Sync :

- Vous migrez d’un déploiement de DFS-R vers un déploiement d’Azure File Sync. Pour plus d’informations, consultez [Migrer un déploiement de la réplication DFS (DFS-R) vers Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Tous les serveurs locaux ayant besoin d’une copie de vos données de fichiers ne peuvent pas être connectés directement à Internet.
- Les serveurs de succursale consolident les données sur un serveur hub pour lequel vous souhaitez utiliser Azure File Sync.

Pour qu’Azure File Sync et DFS-R fonctionnent côte à côte :

1. La hiérarchisation cloud d’Azure File Sync doit être désactivée sur les volumes avec des dossiers répliqué DFS-R.
2. Les points de terminaison de serveur ne doivent pas être configurés sur des dossiers de réplication DFS-R en lecture seule.

Pour plus d’informations, consultez [Vue d’ensemble de la réplication DFS](https://technet.microsoft.com/library/jj127250).

### <a name="antivirus-solutions"></a>Solutions antivirus
Du fait que les antivirus analysent les fichiers pour détecter la présence éventuelle de code malveillant connu, ils peuvent provoquer le rappel de fichiers hiérarchisés. Comme les fichiers hiérarchisés ont l’attribut « hors connexion » défini, nous vous recommandons de contacter votre éditeur de logiciel pour savoir comment configurer la solution de façon à ignorer la lecture des fichiers hors connexion. 

Les solutions suivantes peuvent ignorer les fichiers hors connexion :

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consultez « Scan only what you need to » à la page 90 du PDF)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Solutions de sauvegarde
Tout comme les solutions antivirus, les solutions de sauvegarde peuvent provoquer le rappel de fichiers hiérarchisés. Pour sauvegarder le partage de fichiers Azure, nous vous recommandons d’utiliser une solution de sauvegarde cloud au lieu d’un produit de sauvegarde locale.

### <a name="encryption-solutions"></a>Solutions de chiffrement
La prise en charge des solutions de chiffrement dépend de la façon dont elles sont implémentées. Azure File Sync est connu pour fonctionner avec les solutions suivantes :

- Chiffrement BitLocker
- Azure Information Protection, Azure Rights Management Services (Azure RMS) et Active Directory RMS

Azure File Sync est connu pour ne pas fonctionner avec les solutions suivantes :

- Système de fichiers chiffrés NTFS (EFS)

En règle générale, Azure File Sync prend en charge l’interopérabilité avec les solutions de chiffrement qui se trouvent sous le système de fichiers, comme BitLocker, et avec les solutions qui sont implémentées dans le format de fichier, comme BitLocker. Aucune interopérabilité spécifique n’a été prévue pour les solutions qui se trouvent au-dessus du système de fichiers (par exemple, le système de fichiers NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Autres solutions de gestion hiérarchique du stockage (HSM)
Aucune autre solution HSM ne doit être utilisée avec Azure File Sync.

## <a name="region-availability"></a>Disponibilité des régions
Azure File Sync (préversion) est disponible uniquement dans les régions suivantes :

| Région | Emplacement du centre de données |
|--------|---------------------|
| Est de l’Australie | Nouvelle-Galles du Sud |
| Centre du Canada | Toronto |
| Est des États-Unis | Virginie |
| Asie du Sud-Est | Singapour |
| Sud du Royaume-Uni | Londres |
| Europe de l'Ouest | Pays-bas |
| États-Unis de l’Ouest | Californie |

La préversion prend en charge la synchronisation uniquement avec un partage de fichiers Azure qui est situé dans la même région que le service de synchronisation de stockage.

## <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Planification d’un déploiement Azure Files](storage-files-planning.md)
* [Déployer Azure Files](storage-files-deployment-guide.md)
* [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
