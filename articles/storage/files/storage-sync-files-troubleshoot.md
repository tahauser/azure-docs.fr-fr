---
title: "Résoudre les problèmes de synchronisation de fichiers Azure (préversion) | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes courants avec Azure File Sync."
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
ms.openlocfilehash: 5558a69756075dd83f890d5e9e00c9944d841591
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="troubleshoot-azure-file-sync-preview"></a>Résoudre les problèmes de synchronisation de fichiers Azure (préversion)
Utilisez Azure File Sync (préversion) pour centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article est destiné à vous aider à dépanner et à résoudre les problèmes que vous pouvez rencontrer avec le déploiement d’Azure File Sync. Nous vous y expliquons également comment collecter des journaux du système qui sont utiles pour analyser les problèmes rencontrés de manière plus approfondie. Si vous ne trouvez pas de réponse à votre question ici, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

1. La section Commentaires de cet article
2. [Forum du Stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Support Microsoft Pour créer une demande de support, dans le portail Azure, sous l’onglet **Aide**, sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.

## <a name="storage-sync-service-object-management"></a>Gestion d’un objet de service de synchronisation de stockage
Si vous déplacez des ressources d’un abonnement à un autre, les ressources de synchronisation de fichiers (service de synchronisation de stockage) sont bloquées et ne peuvent être déplacées. 

## <a name="agent-installation-and-server-registration"></a>Installation de l’agent et inscription du serveur
<a id="agent-installation-failures"></a>**Résoudre les problèmes d’installation de l’agent**  
Si l’installation de l’agent Azure File Sync échoue, à partir d’une invite de commandes avec élévation de privilèges, exécutez la commande suivante pour activer la journalisation durant l’installation de l’agent :

```
StorageSyncAgent.msi /l*v Installer.log
```

Examinez le fichier installer.log pour déterminer la cause de l’échec de l’installation. 

> [!Note]  
> L’installation de l’agent échoue si votre machine est configurée pour utiliser le service Microsoft Update et que celui-ci n’est pas en cours d’exécution.

<a id="agent-installation-on-DC"></a>**L’installation de l’agent échoue sur le contrôleur de domaine Active Directory** si vous essayez d’installer l’agent de synchronisation sur un contrôleur de domaine Active Directory où le propriétaire du rôle contrôleur de domaine principal est sur Windows Server 2008 R2 ou un système d’exploitation antérieur, l’agent de synchronisation peut échouer.

Pour résoudre cela, transférez le rôle de contrôleur de domaine principal à un autre contrôleur de domaine avec Windows Server 2012 R2 ou une version plus récente, puis synchronisez.

<a id="agent-installation-websitename-failure"></a>**L’installation de l’agent échoue avec l’erreur : « L’Assistant Agent de synchronisation de stockage s’est terminé prématurément »**  
Ce problème peut se produire si le nom par défaut du site web IIS est changé. Pour contourner ce problème, renommez le site web par défaut IIS en « Site web par défaut » et réessayez l’installation. Ce problème sera résolu dans une mise à jour ultérieure de l’agent. 

<a id="server-registration-missing"></a>**Le serveur n’est pas listé sous Serveurs inscrits sur le Portail Azure**  
Si un serveur n’est pas listé sous **Serveurs inscrits** pour un service de synchronisation de stockage :
1. Connectez-vous au serveur que vous souhaitez inscrire.
2. Ouvrez l’Explorateur de fichiers, puis accédez au répertoire d’installation de l’agent de synchronisation de stockage (l’emplacement par défaut est C:\Program Files\Azure\StorageSyncAgent). 
3. Exécutez ServerRegistration.exe, puis effectuez l’Assistant pour inscrire le serveur auprès d’un service de synchronisation de stockage.



<a id="server-already-registered"></a>**L’inscription du serveur affiche le message suivant pendant l’installation de l’agent Azure File Sync : « Ce serveur est déjà inscrit ».** 

![Capture d’écran de la boîte de dialogue d’inscription du serveur avec le message d’erreur « Ce serveur est déjà inscrit »](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ce message s’affiche si le serveur a déjà été inscrit auprès d’un service de synchronisation de stockage. Pour désinscrire le serveur du service de synchronisation de stockage actuel et l’inscrire ensuite auprès d’un nouveau service de synchronisation de stockage, suivez les étapes décrites dans [Désinscrire un serveur d’Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si le serveur n’est pas répertorié sous **Serveurs inscrits** dans le service de synchronisation de stockage, sur le serveur à désinscrire, exécutez les commandes PowerShell suivantes :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si le serveur fait partie d’un cluster, vous pouvez utiliser le paramètre facultatif *Reset-StorageSyncServer -CleanClusterRegistration* pour annuler aussi l’inscription du cluster.

<a id="web-site-not-trusted"></a>**Quand j’inscris un serveur, je vois de nombreuses réponses indiquant que le site web n’est pas approuvé. Pourquoi ?**  
Ce problème se produit quand la stratégie **Sécurité renforcée d’Internet Explorer** est activée pendant l’inscription du serveur. Pour plus d’informations sur la façon de désactiver correctement la stratégie **Sécurité renforcée d’Internet Explorer**, consultez [Préparer Windows Server pour une utilisation avec Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) et [Déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestion du groupe de synchronisation
<a id="cloud-endpoint-using-share"></a>**La création du point de terminaison cloud échoue, avec cette erreur : « Le partage de fichiers Azure spécifié est déjà en cours d’utilisation par un autre point de terminaison cloud »**  
Ce problème se produit si le partage de fichiers Azure est déjà en cours d’utilisation par un autre point de terminaison cloud. 

Si vous voyez ce message et que le partage de fichiers Azure n’est pas en cours d’utilisation par un point de terminaison cloud, effectuez les étapes suivantes pour supprimer les métadonnées Azure File Sync sur le partage de fichiers Azure :

> [!Warning]  
> La suppression des métadonnées sur un partage de fichiers Azure en cours d’utilisation par un point de terminaison cloud entraîne l’échec des opérations Azure File Sync. 

1. Dans le portail Azure, accédez au partage de fichiers Azure.  
2. Cliquez avec le bouton droit sur le partage de fichiers Azure, puis sélectionnez **Modifier les métadonnées**.
3. Cliquez avec le bouton droit sur **SyncService**, puis sélectionnez **Supprimer**.

<a id="cloud-endpoint-authfailed"></a>**La création du point de terminaison cloud échoue, avec cette erreur : « AuthorizationFailed »**  
Ce problème se produit si votre compte d’utilisateur ne dispose pas des droits suffisants pour créer un point de terminaison cloud. 

Pour créer un point de terminaison cloud, votre compte d’utilisateur doit disposer des autorisations Microsoft suivantes :  
* Lecture : Obtenir la définition de rôle
* Écriture : Créer ou mettre à jour la définition de rôle personnalisée
* Lecture : Obtenir l’attribution de rôle
* Écriture : Créer l’attribution de rôle

Les rôles intégrés suivants ont les autorisations Microsoft nécessaires :  
* Propriétaire
* Administrateur de l'accès utilisateur Pour déterminer si votre rôle de compte d’utilisateur a les autorisations nécessaires :  
1. Dans le portail Azure, sélectionnez **Groupes de ressources**.
2. Sélectionnez le groupe de ressources dans lequel se trouve le compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)**.
3. Sélectionnez le **rôle** (par exemple, Propriétaire ou Contributeur) pour votre compte d’utilisateur.
4. Dans la liste **Fournisseur de ressources**, sélectionnez **Autorisation Microsoft**. 
    * **Attribution de rôle** doit avoir les autorisations **Lecture** et **Écriture**.
    * **Définition de rôle** doit avoir les autorisations **Lecture** et **Écriture**.

<a id="server-endpoint-createjobfailed"></a>**La création du point de terminaison de serveur a échoué avec l’erreur : « MgmtServerJobFailed » (code d’erreur :-2134375898)**                                                                                                                    
Ce problème se produit si le chemin du point de terminaison de serveur se trouve sur le volume système et que la hiérarchisation cloud est activée. La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.

<a id="server-endpoint-deletejobexpired"></a>**La suppression du point de terminaison de serveur échoue avec cette erreur : « MgmtServerJobExpired »**                
Ce problème se produit si le serveur est hors connexion ou n’a pas de connectivité réseau. Si le serveur n’est plus disponible, désinscrivez le serveur dans le portail pour supprimer les points de terminaison de serveur. Pour supprimer les points de terminaison de serveur, suivez les étapes décrites dans [Désinscrire un serveur dans Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Impossible d’ouvrir la page de propriétés du point de terminaison serveur ou de mettre à jour de la stratégie de hiérarchisation du cloud**

Ce problème peut se produire si une opération de gestion sur le point de terminaison serveur échoue. Si la page de propriétés de point de terminaison serveur ne s’ouvre pas dans le portail Azure, la mise à jour du point de terminaison serveur à l’aide des commandes PowerShell à partir du serveur peut résoudre ce problème. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint -SubscriptionId mysubguid -ResourceGroupName myrgname -StorageSyncServiceName storagesvcname -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint -Id serverendpointid -CloudTiering true -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>Synchronisation
<a id="afs-change-detection"></a>**Après avoir créé un fichier directement dans mon partage de fichiers Azure sur SMB ou par le biais du portail, combien de temps faut-il pour synchroniser le fichier sur les serveurs du groupe de synchronisation ?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>**La synchronisation échoue sur un serveur**  
Si la synchronisation échoue sur un serveur :
1. Vérifiez qu’il existe un point de terminaison de serveur dans le Portail Azure pour le répertoire à synchroniser sur un partage de fichiers Azure :
    
    ![Capture d’écran d’un groupe de synchronisation avec un point de terminaison cloud et un point de terminaison de serveur dans le Portail Azure](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

2. Dans l’observateur d’événements, examinez les journaux des événements opérationnels et de diagnostic, situés sous Applications et Services\Microsoft\FileSync\Agent.
    1. Vérifiez la connectivité Internet du serveur.
    2. Vérifiez que le service Azure File Sync est en cours d’exécution sur le serveur. Pour cela, ouvrez le composant logiciel enfichable MMC des services et vérifiez que le service Storage Sync Agent (FileSyncSvc) est en cours d’exécution.

<a id="replica-not-ready"></a>**La synchronisation échoue, avec cette erreur : « 0x80c8300f - Le réplica n’est pas prêt à effectuer l’opération requise »**  
Ce problème peut se produire si vous créez un point de terminaison cloud et que vous utilisez un partage de fichiers Azure contenant des données. En principe, la synchronisation démarre à la fin du travail de détection des modifications exécuté sur le partage de fichiers Azure (ce travail peut prendre jusqu’à 24 heures).


    > [!NOTE]
    > Azure File Sync periodically takes VSS snapshots to sync files that have open handles.

Le déplacement de ressources vers un autre abonnement et le déplacement vers un autre locataire Azure AD ne sont actuellement pas pris en charge.  Si l’abonnement est déplacé vers un autre locataire, le partage de fichiers Azure devient inaccessible à notre service en raison du changement de propriété. Si le locataire est modifié, vous devez supprimer les points de terminaison de serveur et le point de terminaison cloud (consultez la section « Gestion du groupe de synchronisation » pour savoir comment nettoyer le partage de fichiers Azure en vue de sa réutilisation), puis recréer le groupe de synchronisation.

<a id="doesnt-have-enough-free-space"></a>**Erreur « This PC doesn’t have enough free space » (Ce PC n’a pas assez d’espace libre)**  
Si le portail affiche l’état « This PC doesn’t have enough free space » (Ce PC n’a pas suffisamment d’espace libre), le problème provient peut-être du fait qu’il reste moins de 1 Go d’espace libre sur le volume.  Par exemple, pour un volume de 1,5 Go, la synchronisation pourra seulement utiliser 0,5 Go. Si vous rencontrez ce problème, augmentez la taille du volume utilisé pour le point de terminaison du serveur.

## <a name="cloud-tiering"></a>Hiérarchisation cloud 
Il existe deux chemins d’accès dédiés aux défaillances dans la hiérarchisation cloud :

- La hiérarchisation des fichiers peut être mise en échec, auquel cas la tentative d’Azure File Sync de hiérarchiser un fichier sur Azure Files est elle aussi avortée.
- Le rappel des fichiers peut être mis en échec, ce qui signifie que le filtre du système de fichiers Azure File Sync (StorageSync.sys) ne parvient pas à télécharger des données lorsqu’un utilisateur tente d’accéder à un fichier hiérarchisé.

Il existe deux classes principales de défaillances pouvant se produire par le biais des deux chemins d’accès dédiés :

- Défaillances de stockage cloud
    - *Problèmes de disponibilité du service de stockage temporaire*. Pour plus d’informations, consultez [Service Level Agreement (SLA) for Azure Storage (Contrat de niveau de service pour Azure Storage)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Inaccessibilité du partage de fichiers Azure*. Cet échec se produit généralement lorsque vous supprimez un partage de fichiers Azure qui est toujours un point de terminaison cloud d’un groupe de synchronisation.
    - *Inaccessibilité d’un compte de stockage*. Cet échec se produit généralement lorsque vous supprimez un compte de stockage comportant un partage de fichiers Azure qui est un point de terminaison cloud d’un groupe de synchronisation. 
- Défaillances de serveur 
    - *Défaut de chargement du filtre du système de fichiers Azure File Sync (StorageSync.sys)*. Pour répondre aux requêtes de hiérarchisation/de rappel, il est nécessaire que le filtre du système de fichiers Azure File Sync soit chargé. Ce défaut de chargement peut s’expliquer de différentes manières. La raison la plus courante est le déchargement manuel par un administrateur. Le filtre du système de fichiers Azure File Sync doit être chargé à tout moment pour qu’un bon fonctionnement d’Azure File Sync soit garanti.
    - *Défaut, corruption ou endommagement de point d’analyse*. Un point d’analyse est une structure de données spécifique d’un fichier qui est composée de deux parties distinctes :
        1. Une balise d’analyse, qui indique au système d’exploitation que le filtre du système de fichiers Azure File Sync (StorageSync.sys) doit éventuellement effectuer une action sur les E/S du fichier. 
        2. Les données d’analyse, qui indiquent au filtre du système de fichiers l’URI du fichier sur le point de terminaison associé du cloud (le partage de fichiers Azure). 
        
        La raison la plus courante de la corruption d’un point d’analyse est la tentative par un administrateur de modifier la balise ou ses données. 
    - *Problèmes de connectivité réseau*. Pour hiérarchiser ou rappeler un fichier, le serveur doit disposer d’une connectivité Internet.

Les sections suivantes vous indiquent comment résoudre les problèmes de hiérarchisation cloud et déterminer si un problème est lié au stockage cloud ou au serveur.

<a id="files-fail-tiering"></a>**Résoudre les problèmes de hiérarchisation de fichiers**  
Si la hiérarchisation des fichiers dans Azure Files échoue :

1. Vérifiez l’existence des fichiers dans le partage de fichiers Azure.

    > [!NOTE]
    > Un fichier doit être synchronisé dans un partage de fichiers Azure pour pouvoir ensuite être hiérarchisé.
2. Dans l’observateur d’événements, examinez les journaux des événements opérationnels et de diagnostic, situés sous Applications et Services\Microsoft\FileSync\Agent.
    1. Vérifiez la connectivité Internet du serveur. 
    2. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
        - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

<a id="files-fail-recall"></a>**Résoudre les problèmes de rappel de fichiers**  
Si le rappel de fichiers échoue :
1. Dans l’observateur d’événements, examinez les journaux des événements opérationnels et de diagnostic, situés sous Applications et Services\Microsoft\FileSync\Agent.
    1. Vérifiez l’existence des fichiers dans le partage de fichiers Azure.
    2. Vérifiez la connectivité Internet du serveur. 
    3. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
        - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

<a id="files-unexpectedly-recalled"></a>**Résoudre les problèmes de rappel de fichiers inattendu sur un serveur**  
Les antivirus, applications de sauvegarde et autres applications qui lisent un grand nombre de fichiers provoquent des rappels inattendus, sauf s’ils ont été configurés pour ignorer la lecture du contenu des fichiers hors connexion. Ignorer les fichiers hors connexion pour les produits qui prennent en charge cette option permet d’éviter des rappels inattendus pendant les opérations telles que les analyses antivirus ou les travaux de sauvegarde.

Contactez votre éditeur de logiciel pour savoir comment configurer la solution de façon à ignorer la lecture des fichiers hors connexion.

Des rappels inattendus peuvent également se produire dans d’autres scénarios, par exemple, quand vous parcourez des fichiers dans l’Explorateur de fichiers. L’ouverture d’un dossier contenant des fichiers cloud hiérarchisés dans l’Explorateur de fichiers sur le serveur peut provoquer des rappels inattendus. Le risque est d’autant plus grand si une solution antivirus est activée sur le serveur.

## <a name="general-troubleshooting"></a>Résolution générale des problèmes
Si vous rencontrez des problèmes avec Azure File Sync sur un serveur, commencez par effectuer les étapes suivantes :
1. Dans l’observateur d’événements, examinez les journaux des événements opérationnels et de diagnostic.
    - Les problèmes de synchronisation, de hiérarchisation et de rappel sont enregistrés dans ces journaux sous Applications et Services\Microsoft\FileSync\Agent.
    - Les problèmes liés à la gestion d’un serveur (par exemple, les paramètres de configuration) sont enregistrés dans ces journaux sous Applications et Services\Microsoft\FileSync\Management.
2. Vérifiez que le service Azure File Sync est en cours d’exécution sur le serveur :
    - Ouvrez le composant logiciel enfichable MMC des services et vérifiez que le service Storage Sync Agent (FileSyncSvc) est en cours d’exécution.
3. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
    - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

Si le problème n’est toujours pas résolu, exécutez l’outil AFSDiag :
1. Créez un répertoire où la sortie AFSDiag doit être enregistrée (par exemple, C:\Output).
2. Ouvrez une fenêtre PowerShell avec élévation de privilèges, puis exécutez les commandes suivantes (appuyez sur Entrée après chaque commande) :

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Pour le niveau de trace du mode noyau d’Azure File Sync, entrez **1** (sauf indication contraire, pour créer des traces plus détaillées), puis appuyez sur Entrée.
4. Pour le niveau de trace du mode utilisateur d’Azure File Sync, entrez **1** (sauf indication contraire, pour créer des traces plus détaillées), puis appuyez sur Entrée.
5. Reproduisez le problème. Quand vous avez terminé, entrez **D**.
6. Un fichier .zip contenant les journaux et les fichiers de trace est enregistré dans le répertoire de sortie que vous avez spécifié.

## <a name="see-also"></a>Voir aussi
- [Forum Aux Questions Azure Files](storage-files-faq.md)
- [Résoudre les problèmes liés à Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Résoudre les problèmes liés à Azure Files dans Linux](storage-troubleshoot-linux-file-connection-problems.md)
