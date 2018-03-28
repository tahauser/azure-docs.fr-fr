---
title: Notes de publication sur l’agent Azure File Sync (préversion) | Microsoft Docs
description: Notes de publication sur l’agent Azure File Sync (préversion).
services: storage
author: wmgries
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/12/2018
ms.author: wgries
ms.openlocfilehash: b42287580078b4391ddbc5b8ff2835131c64236d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="release-notes-for-the-azure-file-sync-agent-preview"></a>Notes de publication sur l’agent Azure File Sync (préversion)
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes de l’agent Azure File Sync sont prises en charge :

| Jalon | Numéro de version de l’agent | Date de lancement | Statut |
|----|----------------------|--------------|------------------|
| Correctif cumulatif de mars | 2.2.0.0 | 12 mars 2018 | Prise en charge (version recommandée) |
| Correctif cumulatif de février | 2.1.0.0 | 28 février 2018 | Prise en charge |
| Actualisation 1 | 2.0.11.0 | 8 février 2018 | Prise en charge |
| Correctif cumulatif de janvier | 1.4.0.0 | 8 janvier 2018 | Prise en charge jusqu’au 8 mai 2018<sup>1</sup> |
| Correctif cumulatif de novembre | 1.3.0.0 | 30 novembre 2017 | Prise en charge jusqu’au 8 mai 2018<sup>1</sup> |
| Correctif cumulatif d’octobre | 1.2.0.0 | 31 octobre 2017 | Prise en charge jusqu’au 8 mai 2018<sup>1</sup> |
| Version préliminaire initiale | 1.1.0.0 | 26 septembre 2017 | Prise en charge jusqu’au 8 mai 2018<sup>1</sup> |

\[1\] : Les versions de l’agent Azure File Sync lors de la préversion ne sont volontairement pas conformes à la stratégie de mise à jour. La stratégie de mise à jour est appliquée avec la première version de l’agent après la mise à la disposition générale d’Azure File Sync déclarée.

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-2200"></a>Version 2.2.0.0 de l’agent
Les notes de publication suivantes concernent la version 2.2.0.0 de l’agent Azure File Sync publiée le 12 mars 2018.  Ces notes s’ajoutent aux notes de publication répertoriées pour les versions 2.1.0.0 et 2.0.11.0

L’installation de la version 2.1.0.0 pour certains clients échoue en raison de FileSyncSvc qui ne s’arrête pas. Cette mise à jour résout ce problème.

## <a name="agent-version-2100"></a>Agent version 2.1.0.0
Les notes de publication suivantes concernent la version 2.1.0.0 de l’agent Azure File Sync mise en production le 28 février 2018. Ces notes s’ajoutent aux notes de publication répertoriées pour la version 2.0.11.0.

Cette mise en production inclut les changements suivants :
- Améliorations de la gestion du basculement du cluster.
- Améliorations afin de permettre la gestion fiable de fichiers hiérarchisés.
- Prise en charge de l’installation de l’agent sur des machines de contrôleur de domaine ajoutées à un environnement de domaine Windows Server 2008 R2.
- Corrigé lors de cette mise en production : génération de diagnostics excessifs sur des serveurs comprenant de nombreux fichiers.
- Améliorations de la gestion des erreurs lors d’échecs de session.
- Améliorations de la gestion des erreurs lors de problèmes de transfert de fichiers.
- Dans cette mise en production, l’intervalle par défaut pour exécuter la hiérarchisation cloud lorsqu’elle est activée sur un serveur de point de terminaison est passé à 1 heure. 
- Problème bloquant temporaire : déplacer des ressources Azure File Sync (service de synchronisation de stockage) vers un nouvel abonnement Azure.

## <a name="agent-version-20110"></a>Agent version 2.0.11.0
Les notes de publication suivantes concernent la version 2.0.11.0 de l’agent Azure File Sync (mise en production le 9 février 2018). 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour en savoir plus sur l’installation et la configuration de l’agent Azure File Sync avec un serveur Windows, consultez [Planification d’un déploiement Azure File Sync (préversion)](storage-sync-files-planning.md) et [Déploiement Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2016 et Windows Server 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes d’Azure File Sync (préversion)](storage-sync-files-troubleshoot.md).
- Cette mise en production ajoute la prise en charge de DFS-R. Pour plus d’informations, consultez le [Guide de planification](storage-sync-files-planning.md#distributed-file-system-dfs).
- N’utilisez pas les Outils de gestion de ressources pour serveur de fichiers (FSRM) ou d’autres filtres de fichiers. Les filtres de fichiers peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- La duplication de serveurs inscrits (y compris le clonage de machines virtuelles) peut produire des résultats inattendus. En particulier, il se peut que la synchronisation ne converge jamais.
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.
 
### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données. 
    
    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison ne peut pas se trouver sur le volume système. Par exemple, C:\MonDossier n’est pas un chemin d’accès acceptable, sauf si C:\MonDossier est un point de montage.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- La suppression d’un grand nombre de répertoires (plus de 10 000) d’un serveur en une seule fois peut entraîner des échecs de synchronisation. Supprimez les répertoires par lots de moins de 10 000. Assurez-vous que la synchronisation des opérations de suppression réussisse avant de supprimer le lot suivant.
- Dans cette mise en production, la racine de synchronisation présente à la racine d’un volume est désormais prise en charge.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
- Dans cette mise en production, de nouveaux événements ont été ajoutés pour suivre le temps total d’exécution de la hiérarchisation cloud (EventID 9016), la progression du chargement de la synchronisation (EventID 9302) et les fichiers non synchronisés (EventID 9900).
- Dans cette mise en production, les performances rapides de synchronisation d’espace de noms de récupération d’urgence ont été considérablement améliorées.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Dans cette version, les nouveaux fichiers sont hiérarchisés dans un délai d’1 heure (contre 32 heures auparavant) sous réserve des paramètres de stratégie de hiérarchisation. Nous fournissons une cmdlet PowerShell pour hiérarchiser à la demande. Vous pouvez utiliser la cmdlet pour évaluer la hiérarchisation plus efficacement sans attendre le processus d’arrière-plan.
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
- Dans cette nouvelle version, les fichiers sont maintenant téléchargés en tant que fichiers hiérarchisés sur d’autres serveurs, dans la mesure où le fichier est nouveau ou a déjà été hiérarchisé.

## <a name="agent-version-1100"></a>Agent version 1.1.0.0
Les notes de publication suivantes concernent la version 1.1.0.0 de l’agent Azure File Sync (mise en production le 9 septembre 2017, préversion initiale). 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour en savoir plus sur l’installation et la configuration de l’agent Azure File Sync avec un serveur Windows, consultez [Planification d’un déploiement Azure File Sync (préversion)](storage-sync-files-planning.md) et [Déploiement Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est pris en charge uniquement sur Windows Server 2016 et 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes d’Azure File Sync (préversion)](storage-sync-files-troubleshoot.md).
- N’utilisez pas FSRM ou autres filtres de fichiers. Les filtres de fichiers peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- La duplication de serveurs inscrits (y compris le clonage de machines virtuelles) peut produire des résultats inattendus. En particulier, il se peut que la synchronisation ne converge jamais.
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.
 
### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Chemins de plus de 2 048 caractères.
- La partie DACL d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie SACL d’un descripteur de sécurité utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données. 
    
    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison ne peut pas se trouver sur le volume système. Par exemple, C:\MonDossier n’est pas un chemin d’accès acceptable, sauf si C:\MonDossier est un point de montage.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les CSV.
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- La suppression d’un grand nombre de répertoires (plus de 10 000) d’un serveur en une seule fois peut entraîner des échecs de synchronisation. Supprimez les répertoires par lots de moins de 10 000. Assurez-vous que la synchronisation des opérations de suppression réussisse avant de supprimer le lot suivant.
- Un point de terminaison de serveur à la racine d’un volume n’est actuellement pas pris en charge.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Afin de garantir que les fichiers peuvent être rappelés correctement, il se peut que le système ne hiérarchise pas automatiquement les fichiers nouveaux ou modifiés avant 32 heures. Ce processus inclut la première hiérarchisation après avoir configuré un nouveau point de terminaison de serveur. Nous fournissons une cmdlet PowerShell pour hiérarchiser à la demande. Vous pouvez utiliser la cmdlet pour évaluer la hiérarchisation plus efficacement sans attendre le processus d’arrière-plan.
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.