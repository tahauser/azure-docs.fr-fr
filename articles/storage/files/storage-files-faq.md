---
title: Questions fréquentes (FAQ) sur Azure Files | Microsoft Docs
description: Trouvez des réponses aux questions fréquemment posées sur Azure Files.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: e203787bffa80b324508f7df8f8e7a8c62355695
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Questions fréquentes (FAQ) sur Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers managés dans le cloud qui sont accessibles par le biais du [protocole SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard du secteur (également connu sous le nom de Common Internet File System ou CIFS). Vous pouvez monter des partages de fichiers Azure simultanément sur des déploiements cloud ou locaux de Windows, Linux et macOS. Vous pouvez également mettre en cache des partages de fichiers Azure sur des ordinateurs Windows Server à l’aide d’Azure File Sync (préversion) pour bénéficier d’un accès rapide proche de l’endroit où les données sont utilisées.

Cet article répond à des questions courantes sur les fonctionnalités d’Azure Files, notamment concernant l’utilisation d’Azure File Sync avec Azure Files. Si vous ne trouvez pas de réponse à votre question ici, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

1. La section Commentaires de cet article
2. [Forum du Stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Support Microsoft Pour créer une demande de support, dans le portail Azure, sous l’onglet **Aide**, sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.

## <a name="general"></a>Généralités
* <a id="why-files-useful"></a>
**En quoi Azure Files est-il utile ?**  
   Vous pouvez utiliser Azure Files pour créer des partages de fichiers dans le cloud, sans être responsable de la gestion de la surcharge d’un serveur physique, d’un appareil ou d’un dispositif. Nous nous chargeons du travail monotone pour vous, notamment des mises à jour du système d’exploitation et du remplacement des disques défectueux. Pour en savoir plus sur les scénarios qui se prêtent à l’utilisation d’Azure Files, consultez [Pourquoi Azure Files est-il utile ?](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Quelles sont les différentes façons d’accéder aux fichiers dans Azure Files ?**  
    Vous pouvez monter le partage de fichiers sur votre ordinateur local à l’aide du protocole SMB 3.0, ou vous servir d’outils tels que [l’Explorateur de stockage](http://storageexplorer.com/) pour accéder aux fichiers dans votre partage de fichiers. À partir de votre application, vous pouvez utiliser des bibliothèques clientes de stockage, des API REST, PowerShell ou Azure CLI pour accéder à vos fichiers dans le partage de fichiers Azure.

* <a id="what-is-afs"></a>
**Qu’est-ce qu’Azure File Sync ?**  
    Vous pouvez utiliser Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme vos ordinateurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS (Network File System) et FTPS (File Transfer Protocol Service). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

* <a id="files-versus-blobs"></a>
**Pourquoi utiliser un partage de fichiers Azure plutôt que le stockage Blob Azure pour mes données ?**  
    Azure Files et le stockage Blob Azure permettent tous les deux de stocker de grandes quantités de données dans le cloud, mais sont utiles à des fins légèrement différentes. 
    
    Le stockage Blob Azure est utile pour les applications cloud à grande échelle qui doivent stocker des données non structurées. Le stockage Blob Azure étant une abstraction de stockage plus simple qu’un véritable système de fichiers, le niveau de performance et la mise à l’échelle s’en trouvent optimisés. Vous pouvez accéder au stockage Blob Azure uniquement par le biais de bibliothèques clientes basées sur REST (ou directement par le biais du protocole basé sur REST).

    Azure Files est plus précisément un système de fichiers. Azure Files a toutes les abstractions de fichiers que vous connaissez et appréciez suite à vos années d’expérience avec les systèmes d’exploitation locaux. Comme le stockage Blob Azure, Azure Files offre une interface REST et des bibliothèques clientes basées sur REST. Contrairement au stockage Blob Azure, Azure Files offre un accès SMB aux partages de fichiers Azure. Grâce à SMB, vous pouvez monter un partage de fichiers Azure directement sur des machines virtuelles cloud ou locales Windows, Linux ou macOS, sans écrire de code ou joindre des pilotes spéciaux au système de fichiers. Vous pouvez également mettre en cache des partages de fichiers Azure sur des serveurs de fichiers locaux à l’aide d’Azure File Sync pour bénéficier d’un accès rapide proche de l’endroit où les données sont utilisées. 
   
    Pour une obtenir une description plus approfondie des différences entre Azure Files et le stockage Blob Azure, consultez [Quand utiliser le stockage Blob Azure, Azure Files ou Disques Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour en savoir plus sur le stockage Blob Azure, consultez [Présentation du Stockage Blob](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Pourquoi utiliser un partage de fichiers Azure plutôt que Disques Azure ?**  
    Un disque dans Azure Disks est simplement un disque. Un disque autonome en lui-même n’est pas très utile. Pour tirer parti de Disques Azure, vous devez attacher un disque à une machine virtuelle qui s’exécute dans Azure. Vous pouvez utiliser Disques Azure pour toutes les opérations que vous effectueriez sur un disque sur un serveur local. Vous pouvez l’utiliser en tant que disque de système d’exploitation, espace d’échange pour un système d’exploitation ou stockage dédié pour une application. Vous pouvez notamment recourir à Disques Azure pour créer un serveur de fichiers dans le cloud et l’utiliser là ou vous utiliseriez un partage de fichiers Azure. Déployer un serveur de fichiers sur des machines virtuelles Azure est un moyen très efficace d’obtenir un stockage de fichiers dans Azure quand vous avez besoin d’options de déploiement qui ne sont pas prises en charge par Azure Files (telles que la prise en charge du protocole NFS ou le stockage Premium). 

    Toutefois, exécuter un serveur de fichiers avec Disques Azure en tant que stockage backend est généralement beaucoup plus onéreux qu’utiliser un partage de fichiers Azure, pour plusieurs raisons. Tout d’abord, outre le stockage sur disque, vous devez payer les dépenses liées à l’exécution d’une ou plusieurs machines virtuelles Azure. Ensuite, vous devez également gérer les machines virtuelles servant à exécuter le serveur de fichiers. Par exemple, vous êtes responsable des mises à niveau du système d’exploitation. Pour finir, si des données doivent être mises en cache localement, il vous appartient de configurer et de gérer des technologies de réplication (telles que la réplication du système de fichiers DFS).

    Une approche intéressante pour tirer parti d’Azure Files et d’un serveur de fichiers hébergé sur des machines virtuelles Azure (en plus d’utiliser Disques Azure comme stockage backend) consiste à installer Azure File Sync sur un serveur de fichiers hébergé sur une machine virtuelle cloud. Si le partage de fichiers Azure se trouve dans la même région que votre serveur de fichiers, vous pouvez activer la hiérarchisation cloud et définir le pourcentage d’espace libre du volume sur la valeur maximale (99 %). Cela garantit la duplication minimale des données. Vous pouvez également utiliser les applications de votre choix avec vos serveurs de fichiers, telles que des applications qui exigent la prise en charge du protocole NFS.

    Pour plus d’informations sur une option permettant de configurer un serveur de fichiers à hautes performances et à disponibilité élevée dans Azure, consultez [Déploiement de clusters invités de machine virtuelle IaaS dans Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Pour une obtenir une description plus approfondie des différences entre Azure Files et Disques Azure, consultez [Quand utiliser le stockage Blob Azure, Azure Files ou Disques Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour en savoir plus sur Disques Azure, consultez [Vue d’ensemble d’Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Comment commencer à utiliser Azure Files ?**  
   La prise en main d’Azure Files est simple. Tout d’abord, [créez un partage de fichiers](storage-how-to-create-file-share.md), puis montez-le dans le système d’exploitation de votre choix : 

    * [Monter dans Windows](storage-how-to-use-files-windows.md)
    * [Monter dans Linux](storage-how-to-use-files-linux.md)
    * [Monter dans macOS](storage-how-to-use-files-mac.md)

   Pour obtenir un guide détaillé sur le déploiement d’un partage de fichiers Azure afin de remplacer des partages de fichiers de production dans votre organisation, consultez [Planification d’un déploiement Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Quelles sont les options de redondance de stockage prises en charge par Azure Files ?**  
    À l’heure actuelle, Azure Files prend en charge le stockage localement redondant (LRS), le stockage redondant dans une zone (ZRS) et le stockage géoredondant (GRS). Nous envisageons la prise en charge du stockage géoredondant avec accès en lecture (RA-GRS), mais aucun calendrier n’est arrêté pour l’instant.

* <a id="tier-options"></a>
**Quels sont les niveaux de stockage pris en charge par Azure Files ?**  
    Actuellement, Azure Files prend uniquement en charge le niveau de stockage standard. Aucun calendrier n’est arrêté pour l’instant en ce qui concerne la prise en charge du stockage Premium et du stockage froid. 
    
    > [!NOTE]
    > Vous ne pouvez pas créer de partages de fichiers Azure à partir de comptes de stockage d’objets blob uniquement ou de comptes de stockage Premium.

* <a id="give-us-feedback"></a>
**Je souhaite vraiment qu’une fonctionnalité soit ajoutée à Azure Files. Pouvez vous ajouter ?**  
    L’équipe Azure Files est à l’écoute de tous vos commentaires sur notre service. Veuillez voter pour les demandes de fonctionnalités sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files). Nous sommes impatients de mettre à votre disposition de nombreuses nouvelles fonctionnalités.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
**Quelles sont les régions prises en charge par Azure File Sync (préversion) ?**  
    À l’heure actuelle, Azure File Sync est disponible dans les régions Est de l’Australie, Centre du Canada, États-Unis de l’Est, Sud-Est asiatique, Royaume-Uni Sud, Europe de l’Ouest et États-Unis de l’Ouest. La prise en charge d’autres régions sera ajoutée d’ici la date de disponibilité générale. Pour plus d’informations, consultez [Disponibilité des régions](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**Un même groupe de synchronisation peut-il contenir des serveurs joints à un domaine et des serveurs non joints à un domaine ?**  
    Oui. Un groupe de synchronisation peut contenir des points de terminaison de serveur qui ont des appartenances Active Directory différentes, même s’ils ne sont pas joints à un domaine. Bien que cette configuration fonctionne sur le plan technique, nous la déconseillons comme configuration standard, car les listes de contrôle d’accès (ACL, Access Control Lists) définies pour les fichiers et dossiers sur un serveur peuvent ne pas être applicables par d’autres serveurs dans le groupe de synchronisation. Pour de meilleurs résultats, nous vous recommandons d’effectuer une synchronisation entre des serveurs qui sont dans la même forêt Active Directory, entre des serveurs qui sont dans des forêts Active Directory différentes mais qui ont des relations d’approbation établies, ou entre des serveurs n’appartenant pas un domaine. Nous vous déconseillons d’utiliser une combinaison de ces configurations.

* <a id="afs-change-detection"></a>
**J’ai créé un fichier directement dans mon partage de fichiers Azure à l’aide de SMB ou dans le portail. Combien de temps faut-il pour que le fichier soit synchronisé vers les serveurs du groupe de synchronisation ?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Si le même fichier est modifié sur deux serveurs à peu près au même moment, que se passe-t-il ?**  
    Azure File Sync utilise une stratégie de résolution de conflit simple : nous conservons les modifications apportées aux fichiers modifiés sur deux serveurs en même temps. Le fichier le plus récemment écrit conserve son nom d’origine. Concernant l’ancien fichier, la machine « source » et le numéro de conflit sont ajoutés à son nom, avec le format suivant : 
   
    \<nom_fichier_sans_extension\>-\<nom_ordinateur\>\[-#\].\<ext\>  

    Par exemple, le premier conflit de CompanyReport.docx deviendra CompanyReport-CentralServer.docx si CentralServer est l’endroit où l’écriture la plus ancienne s’est produite. Le deuxième conflit sera nommé CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Le stockage géoredondant est-il pris en charge par Azure File Sync ?**  
    Oui, Azure Files prend en charge le stockage localement redondant (LRS) et le stockage géoredondant (GRS). En cas de basculement GRS entre des régions associées, nous vous recommandons de traiter la nouvelle région en tant que sauvegarde des données uniquement. Azure File Sync ne démarre pas automatiquement la synchronisation avec la nouvelle région principale. 

* <a id="sizeondisk-versus-size"></a>
**Pourquoi la propriété *Taille* sur le disque pour un fichier ne correspond-elle pas à la propriété *Taille* après l’utilisation d’Azure File Sync ?**  
    L’Explorateur de fichiers Windows expose deux propriétés pour représenter la taille d’un fichier : **Taille** et **Taille sur le disque**. Ces propriétés ont un sens légèrement différent. La **Taille** représente la taille complète du fichier. La **Taille sur le disque** représente la taille du flux de fichier stocké sur le disque. Les valeurs de ces propriétés peuvent différer pour diverses raisons, telles que la compression, l’utilisation de la déduplication des données ou la hiérarchisation cloud avec Azure File Sync. Si un fichier est hiérarchisé sur un partage de fichiers Azure, la taille sur le disque est égale à zéro, car le flux de fichier est stocké dans votre partage de fichiers Azure, et non sur le disque. Un fichier peut également être partiellement hiérarchisé (ou partiellement rappelé). Dans un fichier partiellement hiérarchisé, une partie du fichier est sur le disque. Cela peut se produire quand des fichiers sont partiellement lus par des applications telles que des lecteurs multimédias ou des utilitaires de compression. 

* <a id="is-my-file-tiered"></a>
**Comment puis-je savoir si un fichier a été hiérarchisé ?**  
    Vous pouvez déterminer de plusieurs façons si un fichier a été hiérarchisé sur votre partage de fichiers Azure :
    
   *  **Vérifier les attributs du fichier.**
     Pour ce faire, cliquez avec le bouton droit sur le fichier, accédez à **Détails**, puis faites défiler jusqu’à la propriété **Attributs**. Un fichier hiérarchisé a les attributs suivants définis :     
        
        | Lettre de l’attribut | Attribut | Définition |
        |:----------------:|-----------|------------|
        | A | Archivage | Indique que le fichier doit être sauvegardé par un logiciel de sauvegarde. Cet attribut est toujours défini, que le fichier soit hiérarchisé ou entièrement stocké sur le disque. |
        | P | Fichier partiellement alloué | Indique qu’il s’agit d’un fichier partiellement alloué. Un fichier partiellement alloué est un type de fichier spécial offert par NTFS, qui est efficace quand le flux sur disque du fichier est pratiquement vide. Azure File Sync utilise des fichiers partiellement alloués, car un fichier est soit entièrement hiérarchisé, soit partiellement rappelé. Dans un fichier entièrement hiérarchisé, le flux de fichier est stocké dans le cloud. Dans un fichier partiellement rappelé, cette partie du fichier est déjà sur le disque. Si un fichier est entièrement rappelé sur le disque, Azure File Sync le convertit d’un fichier partiellement alloué en fichier normal. |
        | L | Point d’analyse | Indique que le fichier a un point d’analyse. Un point d’analyse est un pointeur spécial destiné à être utilisé par un filtre de système de fichiers. Azure File Sync utilise des points d’analyse pour définir dans le filtre de système de fichiers d’Azure File Sync (StorageSync.sys) à quel endroit du cloud le fichier est stocké. Cela permet de bénéficier d’un accès aisé. Les utilisateurs n’ont pas besoin de savoir qu’Azure File Sync est utilisé, ni comment obtenir l’accès au fichier dans votre partage de fichiers Azure. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime le point d’analyse du fichier. |
        | O | Hors ligne | Indique qu’une partie ou la totalité du contenu du fichier n’est pas stockée sur le disque. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime cet attribut. |

        ![Boîte de dialogue Propriétés d’un fichier dans laquelle l’onglet Détails est sélectionné](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Vous pouvez également voir les attributs de tous les fichiers d’un dossier en ajoutant le champ **Attributs** à l’affichage sous forme de tableau de l’Explorateur de fichiers. Pour ce faire, cliquez avec le bouton droit sur une colonne (par exemple, **Taille**), sélectionnez **Autres**, puis sélectionnez **Attributs** dans la liste déroulante.
        
   * **Utiliser `fsutil` pour rechercher les points d’analyse sur un fichier.**
       Comme indiqué dans l’option précédente, un fichier hiérarchisé a toujours un point d’analyse défini. Un pointeur d’analyse est un pointeur spécial pour le filtre de système de fichiers Azure File Sync (StorageSync.sys). Pour vérifier si un fichier a un point d’analyse, dans une fenêtre d’invite de commandes avec élévation de privilèges ou une fenêtre PowerShell, exécutez l’utilitaire `fsutil` :
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Si le fichier a un point d’analyse, vous devez voir **Valeur de la balise d’analyse : 0x8000001e**. Cette valeur hexadécimale est la valeur de point d’analyse détenue par Azure File Sync. La sortie contient également les données d’analyse qui représentent le chemin du fichier dans votre partage de fichiers Azure.

        > [!WARNING]  
        > La commande d’utilitaire `fsutil reparsepoint` permet également de supprimer un point d’analyse. N’exécutez pas cette commande, sauf si l’équipe d’ingénierie Azure File Sync vous le demande. L’exécution de cette commande peut entraîner une perte de données. 

* <a id="afs-recall-file"></a>**Un fichier que je souhaite utiliser a été hiérarchisé. Comment puis-je rappeler le fichier sur le disque pour l’utiliser localement ?**  
    Pour rappeler un fichier sur le disque, le plus simple consiste à l’ouvrir. Le filtre de système de fichiers d’Azure File Sync (StorageSync.sys) télécharge le fichier à partir de votre partage de fichiers Azure de façon fluide, sans aucune intervention de votre part. Pour les types de fichiers qui peuvent être partiellement lus, tels que les fichiers multimédias ou .zip, l’ouverture d’un fichier n’entraîne pas le téléchargement du fichier entier.

    Vous pouvez également utiliser PowerShell pour forcer le rappel d’un fichier. Cette option peut être utile si vous souhaitez rappeler plusieurs fichiers en même temps (par exemple tous les fichiers d’un dossier). Ouvrez une session PowerShell sur le nœud de serveur sur lequel Azure File Sync est installé, puis exécutez les commandes PowerShell suivantes :
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Comment faire pour imposer la hiérarchisation d’un fichier ou répertoire ?**  
    Quand elle est activée, la fonctionnalité de hiérarchisation cloud hiérarchise automatiquement les fichiers en fonction de la date du dernier accès et de la dernière modification afin d’atteindre le pourcentage d’espace libre du volume spécifié sur le point de terminaison cloud. Parfois, cependant, vous pouvez être amené à imposer une hiérarchisation manuelle d’un fichier. Cette opération peut s’avérer utile si vous enregistrez un fichier volumineux que vous n’envisagez pas de réutiliser pendant un certain temps, et que vous souhaitez dédier dans l’immédiat l’espace libre sur le volume à d’autres fichiers et dossiers. Vous pouvez forcer la hiérarchisation à l’aide des commandes PowerShell suivantes :

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Comment *l’espace libre du volume* est-il interprété quand il y a plusieurs points de terminaison de serveur sur un volume ?**  
    Quand il y a plusieurs point de terminaison de serveur sur un volume, le seuil d’espace libre de volume effectif est l’espace libre de volume le plus élevé spécifié sur tous les points de terminaison de serveur sur ce volume. Les fichiers sont hiérarchisés en fonction de leurs modèles d’utilisation, quel que soit le point de terminaison de serveur auquel ils appartiennent. Par exemple, si vous avez deux points de terminaison de serveur sur un volume, Point1 et Point2, où Point1 a un seuil d’espace libre de volume de 25 % et Point2 a un seuil d’espace libre de volume de 50 %, le seuil d’espace libre de volume pour les deux points de terminaison de serveur est de 50 %.

* <a id="afs-files-excluded"></a>
**Quels fichiers ou dossiers sont automatiquement exclus par Azure File Sync ?**  
    Par défaut, la synchronisation de fichiers Azure exclut les fichiers suivants :
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    Les dossiers suivants sont également exclus par défaut :

    * \System Volume Information
    * \$RECYCLE.BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Puis-je utiliser Azure File Sync avec Windows Server 2008 R2, Linux ou mon périphérique de stockage NAS (Network-Attached Storage) ?**  
    À l’heure actuelle, Azure File Sync prend uniquement en charge Windows Server 2016 et Windows Server 2012 R2. À ce stade, nous n’avons pas d’autres plans à partager, mais nous sommes ouverts à l’idée de prendre en charge des plateformes supplémentaires en fonction de la demande des clients. Indiquez-nous les plateformes que vous souhaiteriez voir prises en charge sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Sécurité, authentification et contrôle d’accès
* <a id="ad-support"></a>
**Azure Files prend-il en charge le contrôle d’accès et l’authentification Active Directory ?**  
    Azure Files offre deux manières de gérer le contrôle d’accès :

    - Vous pouvez utiliser des signatures d’accès partagé (SAP) pour générer des jetons qui ont des autorisations spécifiques et qui sont valides pendant un laps de temps spécifié. Par exemple, vous pouvez générer un jeton offrant un accès en lecture seule à un fichier spécifique, valide pendant 10 minutes. Toute personne possédant le jeton pendant sa période de validité dispose d’un accès en lecture seule à ce fichier pendant 10 minutes. Actuellement, les clés de signature d’accès partagé sont prises en charge uniquement par le biais de l’API REST ou dans les bibliothèques clientes. Vous devez monter le partage de fichiers Azure sur SMB à l’aide des clés de compte de stockage.

    - Azure File Sync conserve et réplique toutes les listes de contrôle d’accès discrétionnaire, ou listes DACL (qu’elles soient locales ou basées sur Active Directory) sur tous les points de terminaison de serveur avec lesquels il effectue une synchronisation. Étant donné que Windows Server peut déjà s’authentifier auprès d’Active Directory, Azure File Sync constitue une option efficace en attendant que l’authentification Active Directory et les listes ACL soient totalement prises en charge.

    Actuellement, Azure Files ne prend pas en charge Active Directory directement.

* <a id="encryption-at-rest"></a>
**Comment puis-je m’assurer que mon partage de fichiers Azure est chiffré au repos ?**  
    Le chiffrement du service Stockage Azure est en cours d’activation par défaut dans toutes les régions. Pour ces régions, vous n’avez rien à faire pour activer le chiffrement. Pour les autres régions, consultez [Chiffrement côté serveur](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Comment donner accès à un fichier spécifique à l’aide d’un navigateur web ?**  
    Vous pouvez utiliser des signatures d’accès partagé (SAP) pour générer des jetons qui ont des autorisations spécifiques et qui sont valides pendant un laps de temps spécifié. Par exemple, vous pouvez générer un jeton qui procure un accès en lecture seule à un fichier spécifique pendant un laps de temps donné. Toute personne possédant l’URL peut accéder au fichier directement à partir de n’importe quel navigateur web tant que le jeton est valide. Vous pouvez facilement générer une clé de signature d’accès partagé à partir d’une interface utilisateur comme l’Explorateur de stockage.

* <a id="file-level-permissions"></a>
**Est-il possible de spécifier des autorisations en lecture seule ou en écriture seule sur des dossiers au sein du partage ?**  
    Si vous montez le partage de fichiers à l’aide de SMB, vous ne bénéficiez pas du contrôle au niveau des dossiers sur les autorisations. Toutefois, si vous créez une signature d’accès partagé à l’aide de l’API REST ou des bibliothèques clientes, vous pouvez spécifier des autorisations en lecture seule ou en écriture seule sur des dossiers dans le partage.

* <a id="ip-restrictions"></a>
**Puis-je implémenter des restrictions d’adresses IP pour un partage de fichiers Azure ?**  
    Oui. L’accès à votre partage de fichiers Azure peut être limité au niveau du compte de stockage. Pour plus d’informations, consultez [Configurer Pare-feu et réseaux virtuels dans Stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Quelles sont les stratégies de conformité des données prises en charge par Azure Files ?**  
   Azure Files s’exécute sur la même architecture de stockage que d’autres services de stockage dans Stockage Azure. Azure Files applique les mêmes stratégies de conformité des données que celles utilisées dans d’autres services de stockage Azure. Pour plus d’informations sur la conformité des données de Stockage Azure, vous pouvez télécharger et consulter le document sur la [protection des données dans Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) et le [Centre de gestion de la confidentialité Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Accès local
* <a id="expressroute-not-required"></a>
**Dois-je utiliser Azure ExpressRoute pour me connecter à Azure Files ou pour utiliser Azure File Sync localement ?**  
    Non. ExpressRoute n’est pas nécessaire pour accéder à un partage de fichiers Azure. Si vous montez un partage de fichiers Azure directement localement, la seule contrainte est que le port 445 (TCP sortant) soit ouvert pour l’accès à Internet (il s’agit du port sur lequel SMB communique). Si vous utilisez Azure File Sync, seul est nécessaire le port 443 (TCP sortant) pour l’accès HTTPS (aucun protocole SMB requis). Toutefois, vous *pouvez* utiliser ExpressRoute avec l’une ou l’autre de ces options d’accès.

* <a id="mount-locally"></a>
**Comment monter un partage de fichiers Azure sur mon ordinateur local ?**  
    Vous pouvez monter le partage de fichiers à l’aide du protocole SMB si le port 445 (TCP sortant) est ouvert et que votre client prend en charge le protocole SMB 3.0 (par exemple si vous utilisez Windows 10 ou Windows Server 2016). Si le port 445 est bloqué par la stratégie de votre organisation ou par votre fournisseur de services Internet, vous pouvez utiliser Azure File Sync pour accéder à votre partage de fichiers Azure.

## <a name="backup"></a>Sauvegarde
* <a id="backup-share"></a>
**Comment faire pour sauvegarder mon partage de fichiers Azure ?**  
    Vous pouvez utiliser des [instantanés de partage](storage-snapshots-files.md) périodiques pour la protection contre les suppressions accidentelles. Vous pouvez aussi utiliser AzCopy, RoboCopy ou un outil de sauvegarde tiers capable de sauvegarder un partage de fichiers monté. 

## <a name="share-snapshots"></a>Instantanés de partage
### <a name="share-snapshots-general"></a>Instantanés de partage - Généralités
* <a id="what-are-snaphots"></a>
**Qu’est-ce qu’un instantané de partage de fichiers ?**  
    Vous pouvez utiliser des instantanés de partage de fichiers Azure pour créer une version en lecture seule de vos partages de fichiers. Vous pouvez également utiliser Azure Files pour copier une version antérieure de votre contenu sur le même partage, à un autre emplacement dans Azure ou localement en vue d’effectuer d’autres modifications. Pour en savoir plus sur les instantanés de partage, consultez [Vue d’ensemble des instantanés de partage](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Où sont stockés mes instantanés de partage ?**  
    Les instantanés de partage sont stockés dans le même compte de stockage que celui du partage de fichiers.

* <a id="snapshot-perf-impact"></a>
**L’utilisation d’instantanés de partage a-t-elle un impact sur les performances ?**  
    Les instantanés de partage n’affectent pas le niveau de performance.

* <a id="snapshot-consistency"></a>
**Les instantanés de partage sont-ils cohérents par rapport aux applications ?**  
    Non, les instantanés de partage ne sont pas cohérents par rapport aux applications. L’utilisateur doit vider les écritures à partir de l’application vers le partage avant de capturer l’instantané de partage.

* <a id="snapshot-limits"></a>
**Existe-t-il des limites quant au nombre d’instantanés de partage que je peux utiliser ?**  
    Oui. Azure Files peut conserver un maximum de 200 instantanés de partage. Les instantanés de partage n’étant pas comptabilisés dans le quota de partages, il n’y a pas de limite par partage quant à l’espace total utilisé par tous les instantanés de partage. Les limites de compte de stockage continuent de s’appliquer. Au-delà de 200 instantanés de partage, vous devez supprimer les anciens instantanés pour en créer de nouveaux.
* <a id="snapshot-cost"></a>
**Quel est le coût des instantanés de partage ?**  
    Le coût d’une transaction standard et du stockage standard s’applique à l’instantané. Les instantanés sont incrémentiels par nature. L’instantané de base est le partage lui-même. Tous les instantanés suivants étant incrémentiels, chacun ne stocke que la différence par rapport à l’instantané précédent. Cela signifie que les modifications delta qui apparaîtront dans la facture seront minimales si l’évolution de votre charge de travail est minime. Consultez la [page Tarification](https://azure.microsoft.com/en-us/pricing/details/storage/files/) pour obtenir des informations sur la tarification Azure Files standard. Actuellement, la façon d’examiner la taille utilisée par un instantané de partage consiste à comparer la capacité facturée avec la capacité utilisée. Nous travaillons sur des outils pour améliorer la génération de rapports.


### <a name="create-share-snapshots"></a>Créer des instantanés de partage
* <a id="file-snaphsots"></a>
**Puis-je créer des instantanés de partage de fichiers individuels ?**  
    Les instantanés de partage sont créés au niveau du partage de fichiers. Vous pouvez restaurer des fichiers individuels à partir de l’instantané de partage de fichiers, mais vous ne pouvez pas créer d’instantanés de partage au niveau du fichier. Toutefois, si vous avez capturé un instantané de partage au niveau du partage et que vous souhaitez répertorier les instantanés de partage où un fichier spécifique a changé, vous pouvez effectuer cette opération sous **Versions précédentes** sur un partage monté sur Windows. 
    
    N’hésitez pas à nous indiquer si vous avez besoin d’une fonctionnalité de capture d’instantanés de fichiers sur [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Puis-je créer des instantanés d’un partage de fichiers chiffré ?**  
    Vous pouvez capturer un instantané de partages de fichiers Azure pour lesquels le chiffrement est activé au repos. Vous pouvez restaurer des fichiers depuis un instantané de partage vers un partage de fichiers chiffré. Si votre partage est chiffré, l’instantané de partage l’est également.

* <a id="geo-redundant-snaphsots"></a>
**Mes instantanés de partage sont-ils géoredondants ?**  
    Les instantanés de partage ont la même redondance que le partage de fichiers Azure auquel ils sont destinés. Si vous avez sélectionné un stockage géoredondant pour votre compte, votre instantané de partage est également stocké de façon redondante dans la région associée.

### <a name="manage-share-snapshots"></a>Gérer les instantanés de partage
* <a id="browse-snapshots-linux"></a>
**Puis-je parcourir mes instantanés de partage à partir de Linux ?**  
    Vous pouvez utiliser Azure CLI 2.0 pour créer, répertorier, explorer et restaurer des instantanés de partage dans Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Puis-je copier les instantanés de partage vers un autre compte de stockage ?**  
    Vous pouvez copier des fichiers des instantanés de partage vers un autre emplacement, mais vous ne pouvez pas copier les instantanés de partage eux-mêmes.

### <a name="restore-data-from-share-snapshots"></a>Restaurer des données à partir d’instantanés de partage
* <a id="promote-share-snapshot"></a>
**Puis-je promouvoir un instantané de partage vers le partage de base ?**  
    Vous pouvez copier des données d’un instantané de partage vers toute autre destination. Vous ne pouvez pas promouvoir un instantané de partage en partage de base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Puis-je restaurer des données depuis mon instantané de partage vers un autre compte de stockage ?**  
    Oui. Vous pouvez copier les fichiers d’un instantané de partage vers l’emplacement d’origine ou vers un autre emplacement qui comprend le compte de stockage ou un autre compte de stockage, dans la même région ou dans des régions différentes. Vous pouvez également copier des fichiers vers un emplacement local ou vers n’importe quel autre cloud.    
  
### <a name="clean-up-share-snapshots"></a>Nettoyer des instantanés de partage
* <a id="delete-share-keep-snapshots"></a>
**Puis-je supprimer mon partage sans supprimer mes instantanés de partage ?**  
    Si vous avez des instantanés de partage actifs sur votre partage, vous ne pouvez pas le supprimer. Vous pouvez utiliser une API pour supprimer des instantanés de partage, ainsi que le partage. Vous pouvez également supprimer les instantanés de partage et le partage dans le portail Azure.

* <a id="delete-share-with-snapshots"></a>
**Qu’advient-il de mes instantanés de partage si je supprime mon compte de stockage ?**  
    Si vous supprimez votre compte de stockage, les instantanés de partage sont également supprimés.

## <a name="billing-and-pricing"></a>Facturation et tarification
* <a id="vm-file-share-network-traffic"></a>
**Le trafic réseau entre une machine virtuelle Azure et un partage de fichiers Azure est-il considéré comme de la bande passante externe facturée dans le cadre de l’abonnement ?**  
    Si le partage de fichiers et la machine virtuelle sont dans la même région Azure, il n’y a aucun frais supplémentaire pour le trafic entre le partage de fichiers et la machine virtuelle. Si le partage de fichiers et la machine virtuelle se trouvent dans des régions différentes, le trafic entre eux est facturé en tant que bande passante externe.

* <a id="share-snapshot-price"></a>
**Quel est le coût des instantanés de partage ?**  
     Durant la période de préversion, il n’y a aucun frais pour la capacité d’instantané de partage. Les coûts de sortie standard et de transaction liés au stockage s’appliquent. Après la disponibilité générale, la capacité et les transactions sur les instantanés de partage seront facturées aux abonnements.
     
     Les instantanés de partage sont incrémentiels par nature. L’instantané de partage de base est le partage lui-même. Tous les instantanés de partage suivants sont incrémentiels et ne stockent que la différence par rapport à l’instantané de partage précédent. Vous n’êtes facturé que pour le contenu changé. Si vous disposez d’un partage de 100 Go de données, mais que seulement 5 Go ont changé depuis le dernier instantané de partage, l’instantané de partage consomme seulement 5 Go supplémentaires ; ainsi, 105 Go vous sont facturés. Pour plus d’informations sur les frais de sortie standard et de transaction, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Scalabilité et performances
* <a id="files-scale-limits"></a>
**Quelles sont les limites d’extensibilité d’Azure Files ?**  
    Pour plus d’informations sur les objectifs de scalabilité et de performances d’Azure Files, consultez [Objectifs de performance et d’extensibilité d’Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**J’ai besoin d’un partage de fichiers plus volumineux que celui offert actuellement par Azure Files. Puis-je augmenter la taille de mon partage de fichiers Azure ?**  
    Non. La taille maximale d’un partage de fichiers Azure est de 5 To. Actuellement, il s’agit d’une limite inconditionnelle que nous ne pouvons pas ajuster. Nous travaillons sur une solution permettant d’augmenter la taille de partage à 100 To, mais nous ne sommes pas en mesure de vous indiquer quand elle sera disponible pour l’instant.

* <a id="open-handles-quota"></a>
**Combien de clients peuvent accéder simultanément au même fichier ?**   
    Il existe un quota de 2 000 handles ouverts sur un seul fichier. Quand vous avez 2 000 handles ouverts, un message d’erreur s’affiche pour signaler que le quota est atteint.

* <a id="zip-slow-performance"></a>
**Les performances sont lentes quand je décompresse des fichiers dans Azure Files. Que dois-je faire ?**  
    Pour transférer un grand nombre de fichiers dans Azure Files, nous vous recommandons d’utiliser AzCopy (pour Windows, en préversion pour Linux/Unix) ou Azure Powershell. Ces outils ont été optimisés pour le transfert réseau.

* <a id="slow-perf-windows-81-2012r2"></a>
**Pourquoi les performances sont-elles lentes depuis que j’ai monté mon partage de fichiers Azure sur Windows Server 2012 R2 ou Windows 8.1 ?**  
    Il existe un problème connu lors du montage d’un partage de fichiers Azure sur Windows Server 2012 R2 et Windows 8.1. Ce problème a été corrigé dans la mise à jour cumulative d’avril 2014 pour Windows 8.1 et Windows Server 2012 R2. Pour des performances optimales, vérifiez que ce correctif est appliqué sur toutes les instances de Windows Server 2012 R2 et Windows 8.1. (Vous devez toujours recevoir les correctifs Windows par le biais de Windows Update). Pour plus d’informations, consultez l’article de la Base de connaissances associé [Ralentissement des performances lors de l’accès à Azure Files à partir de Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Fonctionnalités et interopérabilité avec d’autres services
* <a id="cluster-witness"></a>
**Puis-je utiliser mon partage de fichiers Azure en tant que *témoin de partage de fichiers* pour mon cluster de basculement Windows Server ?**  
    Actuellement, cette configuration n’est pas prise en charge pour un partage de fichiers Azure. Pour plus d’informations sur la façon d’obtenir cette configuration pour le stockage Blob Azure, consultez [Déployer un témoin cloud pour un cluster de basculement](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Puis-je monter un partage de fichiers Azure dans une instance Azure Container ?**  
    Oui, les partages de fichiers Azure sont une bonne option quand vous souhaitez conserver les informations au-delà de la durée de vie d’une instance de conteneur. Pour plus d’informations, consultez [Montage d’un partage de fichiers Azure avec Azure Container Instances](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Existe-t-il une opération de renommage dans l’API REST ?**  
    Pas pour l'instant.

* <a id="nested-shares"></a>
**Puis-je configurer des partages imbriqués ? Autrement dit, un partage sous un partage ?**  
    Non. Le partage de fichiers *est* le pilote virtuel que vous pouvez monter, et les partages imbriqués ne sont donc pas pris en charge.

* <a id="ibm-mq"></a>
**Comment faire pour utiliser Azure Files avec IBM MQ ?**  
    IBM a publié un document qui aide les clients IBM MQ à configurer Azure Files avec son service IBM. Pour plus d’informations, consultez [How to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Guide pratique pour configurer le gestionnaire de file d’attente multi-instance IBM MQ avec le service Microsoft Azure Files).

## <a name="see-also"></a>Voir aussi
* [Résoudre les problèmes d’Azure Files dans Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Résoudre les problèmes d’Azure Files dans Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Résoudre les problèmes de synchronisation de fichiers Azure (préversion)](storage-sync-files-troubleshoot.md)
