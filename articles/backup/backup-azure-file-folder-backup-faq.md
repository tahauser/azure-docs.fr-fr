---
title: Agent de sauvegarde Azure - Forum aux questions | Microsoft Docs
description: 'Réponses aux questions fréquentes concernant les sujets suivants : les tâches de l’agent de sauvegarde Azure et les limites de sauvegarde et de rétention.'
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
keywords: sauvegarde et récupération d’urgence ; service de sauvegarde
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 8777b2265666a4505a3432aca6683e2510519cac
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="questions-about-the-azure-backup-agent"></a>Questions sur le service de sauvegarde Azure
Cet article comporte les réponses aux questions fréquentes pour vous aider à comprendre rapidement les composants de l’agent de sauvegarde Azure. Certaines réponses comportent des liens vers les articles présentant des informations complètes. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="configure-backup"></a>Configurer une sauvegarde
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Où puis-je télécharger le dernier agent Azure Backup ? <br/>
Vous pouvez télécharger le dernier agent de sauvegarde de Windows Server, de System Center DPM ou du client Windows, en cliquant [ici](http://aka.ms/azurebackup_agent). Si vous souhaitez sauvegarder une machine virtuelle, utilisez l’Agent de machine virtuelle (qui installe automatiquement l’extension appropriée). L’agent de machine virtuelle est déjà présent dans les machines virtuelles créées à partir de la galerie Azure.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Pendant la configuration de l’agent Azure Backup, je suis invité à entrer des informations d’identification de coffre. Les informations d’identification de coffre expirent-elles ?
Oui, les informations d’identification de coffre expirent au bout de 48 heures. Si le fichier expire, connectez-vous au portail Azure et téléchargez les fichiers d’informations d’identification de votre coffre.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>À partir de quels types de lecteurs puis-je sauvegarder des fichiers et des dossiers ? <br/>
Vous ne pouvez pas sauvegarder les lecteurs/volumes suivants :

* Média amovible : toutes les sources d’éléments de sauvegarde l’élément doivent être déclarées fixes.
* Volumes en lecture seule : le volume doit être accessible en écriture pour que le service VSS puisse fonctionner.
* Volumes déconnectés : le volume doit être en ligne pour que le service VSS puisse fonctionner.
* Partage réseau : le volume doit être local sur le serveur à sauvegarder à l’aide de la sauvegarde en ligne.
* Volumes protégés par BitLocker : le volume doit être déverrouillé pour que la sauvegarde soit possible.
* Identification du système de fichiers : NTFS est le seul système de fichiers pris en charge.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Quels types de fichier et dossier puis-je sauvegarder à partir de mon serveur ?<br/>
Les types suivants sont pris en charge :

* Chiffré
* Compressé
* Partiellement alloué
* Compressé + partiellement alloué
* Liens physiques : non pris en charge, ignorés
* Point d’analyse : non pris en charge, ignoré
* Chiffré + partiellement alloué : non pris en charge, ignoré
* Flux compressé : non pris en charge, ignoré
* Flux partiellement alloué : non pris en charge, ignoré

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure déjà sauvegardée par le service Azure Backup en utilisant l’extension de machine virtuelle ? <br/>
Absolument. Azure Backup fournit une sauvegarde au niveau de la machine virtuelle pour les machines virtuelles Azure à l’aide de l’extension de machine virtuelle. Pour protéger des fichiers et dossiers du système d’exploitation Windows invité, installez l’agent Azure Backup sur ce dernier.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure pour sauvegarder des fichiers et des dossiers situés sur le stockage temporaire fourni par la machine virtuelle Azure ? <br/>
Oui. Installez l’agent Azure Backup sur le système d’exploitation Windows invité et sauvegardez les fichiers et dossiers sur un stockage temporaire. Les opérations de sauvegarde échouent une fois les données du stockage temporaire effacées. En outre, si les données de stockage temporaire ont été supprimées, vous pouvez uniquement restaurer les stockages non volatiles.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Quelle est la taille minimale requise du dossier du cache ? <br/>
La taille du dossier du cache détermine la quantité de données que vous sauvegardez. Le dossier cache doit représenter 5 % de l’espace requis pour le stockage de données.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Comment inscrire mon serveur dans un autre centre de données ?<br/>
Les données de sauvegarde sont envoyées au centre de données du coffre dans lequel il est inscrit. Le moyen le plus simple pour changer de centre de données consiste à désinstaller/réinstaller l’agent et à demander un nouveau coffre appartenant au centre de données choisi.

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>L’agent de sauvegarde Azure fonctionne-t-il sur un serveur qui utilise la déduplication Windows Server 2012 ? <br/>
Oui. Le service de l’agent convertit les données dédupliquées en données normales lorsqu'il prépare l'opération de sauvegarde. Il optimise ensuite les données pour la sauvegarde, chiffre les données, puis envoie les données chiffrées au service de sauvegarde en ligne.

## <a name="backup"></a>Sauvegarde
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Comment puis-je modifier l’emplacement du cache spécifié pour l’agent Azure Backup ?<br/>
Utilisez la liste suivante pour modifier l’emplacement du cache.

1. Arrêtez le moteur Backup en exécutant la commande qui suit dans une invite de commandes avec élévation de privilèges :

    ```PS C:\> Net stop obengine``` 
  
2. Ne déplacez pas les fichiers, mais copiez le dossier d’espace de cache dans un autre lecteur disposant d’un espace suffisant. L’espace de cache d’origine peut être supprimé après avoir confirmé que les sauvegardes fonctionnent avec le nouvel espace de cache.
3. Mettez à jour les entrées de registre suivantes en utilisant le chemin d’accès au nouveau dossier d’espace de cache.<br/>

    | Chemin d’accès au Registre | Clé de Registre | Valeur |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Emplacement du nouveau dossier de cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Emplacement du nouveau dossier de cache* |

4. Démarrez le moteur Backup en exécutant la commande suivante dans une invite de commandes avec élévation de privilèges :

    ```PS C:\> Net start obengine```

Une fois les sauvegardes correctement effectuées avec le nouvel emplacement de cache, vous pouvez supprimer le dossier de cache d’origine.


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Où dois-je placer le dossier du cache de l’agent Azure Backup pour que ce dernier fonctionne comme prévu ?<br/>
Les emplacements suivants pour le dossier du cache ne sont pas recommandés :

* Partage réseau ou un média amovible : le dossier du cache doit être local sur le serveur nécessitant une sauvegarde à l’aide de la sauvegarde en ligne. Les emplacements réseau ou les médias amovibles (tels que les lecteurs USB) ne sont pas pris en charge.
* Volumes hors connexion : le dossier du cache doit être en ligne pour la sauvegarde attendue avec l’agent Azure Backup.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Existe-t-il des attributs du dossier du cache qui ne sont pas pris en charge ?<br/>
Les attributs suivants ou leurs combinaisons ne sont pas pris en charge pour le dossier du cache :

* Chiffré
* Dédupliqué
* Compressé
* Partiellement alloué
* Point d’analyse

Le dossier du cache et les métadonnées du disque dur virtuel ne possèdent les attributs nécessaires pour l’agent de sauvegarde Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Existe-t-il un moyen d’adapter la quantité de bande passante utilisée par le service Backup ?<br/>
  Oui, utilisez l’option **Modifier les propriétés** de l’agent Backup pour régler la bande passante. Vous pouvez ajuster la quantité de bande passante et les heures d’utilisation de cette bande passante. Pour obtenir des instructions détaillées, consultez **[Activation de la limitation du réseau](backup-configure-vault.md#enable-network-throttling)**.

## <a name="manage-backups"></a>Gestion des sauvegardes
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Que se passe-t-il si je renomme un serveur Windows qui sauvegarde des données dans Azure ?<br/>
Lorsque vous renommez un serveur, toutes les sauvegardes actuellement configurées sont arrêtées.
Enregistrez le nouveau nom du serveur avec le coffre de sauvegarde. Lorsque vous enregistrez le nouveau nom avec le coffre, la première opération de sauvegarde est une sauvegarde *complète*. Si vous devez récupérer des données sauvegardées dans le coffre avec le nom de l’ancien serveur, utilisez l’option [**Un autre serveur**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) de l’assistant **Récupérer des données**.

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Quelle est la longueur maximale du chemin d’accès de fichier pouvant être spécifiée dans la stratégie de sauvegarde avec l’agent de sauvegarde Azure ? <br/>
L’agent Azure Backup utilise le format NTFS. La [spécification de longueur de chemin d’accès est limitée par l’API Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Si les fichiers que vous souhaitez protéger présentent une longueur de chemin d’accès supérieure à la limite autorisée par l’API Windows, sauvegardez le dossier parent ou le lecteur de disque.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Quels sont les caractères autorisés dans le chemin d’accès du fichier de stratégie Azure Backup à l’aide de l’agent Azure Backup ? <br>
 L’agent Azure Backup utilise le format NTFS. Elle active les [caractères NTFS pris en charge](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) dans le cadre de la spécification de fichier. 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Je reçois l’avertissement « Les sauvegardes Azure n’ont pas été configurées pour ce serveur » alors que j’avais configuré une stratégie de sauvegarde. <br/>
Cet avertissement est généré lorsque les paramètres de planification de la sauvegarde stockés sur le serveur local diffèrent des paramètres stockés dans le coffre de sauvegarde. Lorsque le serveur ou les paramètres ont été restaurés à un état correct connu, les planifications de sauvegarde peuvent se désynchroniser. Si vous recevez cet avertissement, [reconfigurez la stratégie de sauvegarde](backup-azure-manage-windows-server.md) , puis sélectionnez **Exécuter la sauvegarde maintenant** pour resynchroniser le serveur local avec Azure.
