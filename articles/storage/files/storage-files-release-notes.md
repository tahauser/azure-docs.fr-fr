---
title: "Notes de publication sur l’agent Azure File Sync | Microsoft Docs"
description: Notes de version sur la synchronisation de fichiers Azure
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: tamram
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 5f57edb33770acd7a97287d5cfd650b7fe8366f4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-file-sync-agent-release-notes"></a>Notes de publication sur l’agent Azure File Sync
La synchronisation de fichiers Azure (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article comprend les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes sont prises en charge Azure File Sync :

| Numéro de version de l’agent | Date de lancement | Pris en charge jusqu’à |
|----------------------|--------------|------------------|
| 2.1.0.0 | 2018-02-28 | Version actuelle |
| 2.0.11.0 | 2018-02-08 | Version actuelle |
| 1.1.0.0 | 2017-09-26 | 2018-07-30 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-2100"></a>Agent version 2.1.0.0
Les notes de publication suivantes concernent l’agent version 2.1.0, disponible depuis le 28 février 2018. Celles-ci s’ajoutent aux notes de publications ci-dessous pour la version 2.0.11.0

Parmi les changements uniques à cette mise à jour mensuelle :
- Amélioration de la gestion du basculement du cluster.
- Amélioration de la gestion de fichiers hiérarchisés pour les rendre plus fiables.
- Autorise l’installation d’agent sur des machines de contrôleur de domaine ajoutées à un environnement de domaine 2008R2.
- Corrige la génération excessive de diagnostics sur des serveurs avec de nombreux fichiers.
- Amélioration de la gestion d’erreur lors d’échecs de session.
- Amélioration de la gestion d’erreur pour des problèmes de transfert de fichiers.
- Modifie l’intervalle par défaut pour exécuter la hiérarchisation cloud lorsqu’elle est activée sur un point de terminaison de serveur à une heure. 
- Bloque temporairement des ressources Azure File Sync (Storage Sync Service) en mouvement vers un nouvel abonnement Azure

## <a name="agent-version-20110"></a>Agent version 2.0.11.0
Les notes de publication suivantes concernent l’agent version 2.0.11.0, disponible depuis le 9 février 2018. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour en savoir plus sur l’installation et la configuration de l’agent Azure File Sync avec un serveur Windows, consultez [Planification d’un déploiement Azure File Sync (préversion)](storage-sync-files-planning.md) et [Déploiement Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- Non pris en charge avec les options de déploiement Windows Server Core ou Nano Server.
- Pris en charge uniquement sur Windows Server 2016 et 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes d’Azure File Sync (préversion)](storage-sync-files-troubleshoot.md)
- Dans cette mise à jour, DFS-R est désormais pris en charge.  Consultez le [guide de planification](storage-sync-files-planning.md#distributed-file-system-dfs) pour plus d’informations.
- Ne pas utiliser les filtres de fichiers de l’outil de gestion de ressources pour serveur de fichiers (ou autre) : les filtres de fichiers peuvent entraîner d’interminables échecs de synchronisation si les fichiers sont bloqués par un filtre.
- La duplication d’un serveur inscrit (y compris le clonage de machine virtuelle) peut entraîner des résultats inattendus (convergence de la synchronisation impossible, principalement).
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.
 
### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Chemins de plus de 2048 caractères
- Portion DACL d’un descripteur de sécurité, si plus grand que 2000. Ceci constitue un problème si vous avez plus de 40 entrées Access Control sur le même élément.
- Portion SACL d’un descripteur de sécurité (pour audit)
- Attributs étendus
- Autres flux de données
- Points d’analyse
- Liens physiques
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données 
    
    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit, et ces données peuvent être chiffrées au repos dans Azure.
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison de serveur peut ne pas se trouver sur le volume du serveur. Par exemple : C:\MyFolder n’est pas un chemin accepté, à moins qu’il soit un point de montage.
- Le clustering de basculement est pris en charge avec les disques en cluster uniquement, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué, mais il peut coexister sur le même volume en parallèle des autres.
- La suppression d’un grand nombre de répertoires d’un serveur en une fois (plus de 10000) peut entraîner des échecs de synchronisation. Supprimez moins de 10000 répertoires et veillez à ce que la synchronisation des opérations de suppression ait réussi avant d’en supprimer davantage.
- Dans cette version, la racine de synchronisation présente à la racine d’un volume est désormais prise en charge.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un point de terminaison de serveur.
- Dans cette version, de nouveaux événements ont été ajoutés pour suivre le temps total d’exécution de la hiérarchisation cloud (EventID 9016), la progression du chargement de la synchronisation (EventID 9302) et les fichiers non synchronisés (EventID 9900).
- Dans cette version, les performances rapides de synchronisation d’espace de noms de récupération d’urgence ont été considérablement améliorées.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Dans cette nouvelle version, les nouveaux fichiers sont hiérarchisés en 1 heure conformément au paramétrage de la stratégie de hiérarchisation (contre 32 heures précédemment). Nous fournissons une applet de commande PowerShell afin de hiérarchiser à la demande et d’évaluer la hiérarchisation plus efficacement sans attendre le processus en arrière-plan.
- Si un fichier hiérarchisé est copié dans un autre emplacement à l’aide de Robocopy, le fichier obtenu ne sera pas hiérarchisé mais l’attribut hors ligne peut être défini, car Robocopy inclut mal cet attribut lors des opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
- Dans cette nouvelle version, les fichiers sont maintenant téléchargés en tant que fichiers hiérarchisés sur d’autres serveurs, dans la mesure où le fichier est nouveau ou a déjà été hiérarchisé.

## <a name="agent-version-1100"></a>Agent version 1.1.0.0
Les notes de publication suivantes concernent l’agent version 1.1.0.0, disponible depuis le 9 septembre 2017. Sa sortie marque le début de la version préliminaire d’Azure File Sync !

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour en savoir plus sur l’installation et la configuration de l’agent Azure File Sync avec un serveur Windows, consultez [Planification d’un déploiement Azure File Sync (préversion)](storage-sync-files-planning.md) et [Déploiement Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- Non pris en charge avec les options de déploiement Windows Server Core ou Nano Server.
- Pris en charge uniquement sur Windows Server 2016 et 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes d’Azure File Sync (préversion)](storage-sync-files-troubleshoot.md)
- Ne pas utiliser les filtres de fichiers de l’outil de gestion de ressources pour serveur de fichiers (ou autre) : les filtres de fichiers peuvent entraîner d’interminables échecs de synchronisation si les fichiers sont bloqués par un filtre.
- La duplication d’un serveur inscrit (y compris le clonage de machine virtuelle) peut entraîner des résultats inattendus (convergence de la synchronisation impossible, principalement).
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.
 
### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Chemins de plus de 2048 caractères
- Portion DACL d’un descripteur de sécurité, si plus grand que 2000. Ceci constitue un problème si vous avez plus de 40 entrées Access Control sur le même élément.
- Portion SACL d’un descripteur de sécurité (pour audit)
- Attributs étendus
- Autres flux de données
- Points d’analyse
- Liens physiques
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données 
    
    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit, et ces données peuvent être chiffrées au repos dans Azure.
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison de serveur peut ne pas se trouver sur le volume du serveur. Par exemple : C:\MyFolder n’est pas un chemin accepté, à moins qu’il soit un point de montage.
- Le clustering de basculement est pris en charge avec les disques en cluster uniquement, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué, mais il peut coexister sur le même volume en parallèle des autres.
- La suppression d’un grand nombre de répertoires d’un serveur en une fois (plus de 10000) peut entraîner des échecs de synchronisation. Supprimez moins de 10000 répertoires et veillez à ce que la synchronisation des opérations de suppression ait réussi avant d’en supprimer davantage.
- Non pris en charge à la racine d’un volume.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un point de terminaison de serveur.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Afin de garantir que les fichiers puissent être rappelés correctement, le système ne peut pas hiérarchiser des fichiers modifiés ou nouveaux jusqu’à 32 heures, et cela inclut la première hiérarchisation après configuration d’un nouveau point de terminaison de serveur. Nous fournissons une cmdlet PowerShell pour hiérarchiser à la demande. Ceci vous permettra d’évaluer la hiérarchisation plus efficacement sans attendre le processus en arrière-plan.
- Si un fichier hiérarchisé est copié dans un autre emplacement à l’aide de Robocopy, le fichier obtenu ne sera pas hiérarchisé mais l’attribut hors ligne peut être défini, car Robocopy inclut mal cet attribut lors des opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
