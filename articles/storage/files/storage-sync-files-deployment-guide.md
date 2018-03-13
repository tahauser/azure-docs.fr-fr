---
title: "Déployer Azure File Sync (préversion) | Microsoft Docs"
description: "Découvrez comment déployer Azure File Sync, du début à la fin."
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
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d5864b8df85a5b3cec086d4cb2edc6d288f1639a
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="deploy-azure-file-sync-preview"></a>Déployer Azure File Sync (préversion)
Utilisez Azure File Sync (préversion) pour centraliser les partages de fichiers de votre organisation dans Azure Files, tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Nous vous recommandons fortement de lire les articles [Planification d’un déploiement Azure Files](storage-files-planning.md) et [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md) avant d’effectuer les étapes décrites dans cet article.

## <a name="prerequisites"></a>Conditions préalables
* Avoir un compte de stockage Azure et un partage de fichiers Azure dans la même région où vous souhaitez déployer Azure File Sync. Pour plus d'informations, consultez les pages suivantes :
    - [Disponibilité des régions](storage-sync-files-planning.md#region-availability) pour Azure File Sync.
    - [Créer un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour obtenir une procédure pas à pas de la création d’un compte de stockage.
    - [Créer un partage de fichiers](storage-how-to-create-file-share.md) pour obtenir une procédure pas à pas de la création d’un partage de fichiers.
* Avoir au moins une instance de Windows Server ou d’un cluster Windows Server prise en charge pour la synchronisation avec Azure File Sync. Pour plus d’informations sur les versions de Windows Server prises en charge, consultez [Interopérabilité avec Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Déployer le service de synchronisation de stockage 
Le service de synchronisation de stockage est la ressource Azure de niveau supérieur pour Azure File Sync. Pour déployer un service de synchronisation de stockage, accédez au [portail Azure](https://portal.azure.com/), cliquez sur *Nouveau*, puis recherchez Azure File Sync. Dans les résultats de la recherche, sélectionnez **Azure File Sync (préversion)**, puis sélectionnez **Créer** pour ouvrir l’onglet **Déployer la synchronisation du stockage**.

Dans le volet qui s’ouvre, entrez les informations suivantes :

- **Nom** : nom unique (par abonnement) du service de synchronisation de stockage.
- **Abonnement** : abonnement dans lequel vous voulez créer le service de synchronisation de stockage. Selon la stratégie de configuration de votre organisation, vous avez accès à un ou plusieurs abonnements. Un abonnement Azure est le conteneur de base pour la facturation de chaque service cloud (par exemple, Azure Files).
- **Groupe de ressources** : un groupe de ressources est un groupe logique de ressources Azure, tel qu’un compte de stockage ou un service de synchronisation de stockage. Vous pouvez créer un groupe de ressources ou utiliser un groupe de ressources existant pour Azure File Sync. (Nous vous recommandons d’utiliser des groupes de ressources comme conteneurs pour isoler les ressources logiquement dans votre organisation, par exemple, en regroupant les ressources humaines ou les ressources d’un projet spécifique.)
- **Emplacement** : région dans laquelle vous souhaitez déployer Azure File Sync. Seuls les régions prises en charge sont disponibles dans cette liste.

Quand vous avez terminé, sélectionnez **Créer** pour déployer le service de synchronisation de stockage.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Préparer Windows Server pour une utilisation avec Azure File Sync
Pour chaque serveur que vous comptez utiliser avec Azure File Sync, y compris les nœuds serveur dans un cluster de basculement, effectuez les étapes suivantes :

1. Désactivez la **configuration de sécurité renforcée d’Internet Explorer**. Cela est nécessaire uniquement pour l’inscription initiale du serveur. Vous pouvez réactiver cette configuration une fois que le serveur a été inscrit.
    1. Ouvrez le Gestionnaire de serveurs.
    2. Cliquez sur **Serveur Local** :  
        ![« Serveur local » sur le côté gauche de l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Dans le sous-volet **Propriétés**, sélectionnez le lien vers **Configuration de sécurité renforcée d’Internet Explorer**.  
        ![Volet « Configuration de sécurité renforcée d’Internet Explorer » dans l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Dans la boîte de dialogue **Configuration de sécurité renforcée d’Internet Explorer**, sélectionnez **Désactivé** pour **Administrateurs** et **Utilisateurs** :  
        ![Fenêtre indépendante Configuration de sécurité renforcée d’Internet Explorer avec l’option « Désactivé » sélectionnée](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Assurez-vous d’exécuter au moins PowerShell 5.1.\* (PowerShell 5.1 est la valeur par défaut sur Windows Server 2016). Vous pouvez vérifier que vous utilisez PowerShell 5.1.\* en examinant la valeur de la propriété **PSVersion** de l’objet **$PSVersionTable** :

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Si la valeur PSVersion est inférieure à 5.1.\*, comme cela est le cas avec la plupart des installations de Windows Server 2012 R2, vous pouvez facilement effectuer la mise à niveau en téléchargeant et en installant [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Le package correct à télécharger et à installer pour Windows Server 2012 R2 est **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Installez et configurez Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Nous vous recommandons d’utiliser la dernière version des modules Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Installer l’agent Azure File Sync
L’agent Azure File Sync est un package téléchargeable qui permet à Windows Server de rester synchronisé avec un partage de fichiers Azure. Vous pouvez télécharger l’agent à partir du [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). À la fin du téléchargement, double-cliquez sur le package MSI pour démarrer l’installation de l’agent Azure File Sync.

> [!Important]  
> Si vous comptez utiliser Azure File Sync avec un cluster de basculement, l’agent Azure File Sync doit être installé sur chaque nœud du cluster.


Le package d’installation de l’agent Azure File Sync doit normalement s’installer relativement vite et sans afficher beaucoup d’invites supplémentaires. Nous vous recommandons de procéder comme suit :
- Conservez le chemin d’installation par défaut (C:\Program Files\Azure\StorageSyncAgent), pour simplifier le dépannage et la maintenance du serveur.
- Activez Microsoft Update pour garantir la mise à jour d’Azure File Sync. Toutes les mises à jour de l’agent Azure File Sync, y compris les correctifs logiciels et les mises à jour de fonctionnalités, s’effectuent à partir de Microsoft Update. Nous vous recommandons d’installer la dernière mise à jour d’Azure File Sync. Pour plus d’informations, consultez [Stratégie de mise à jour d’Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Au terme de l’installation de l’agent Azure File Sync, l’interface utilisateur d’inscription du serveur s’ouvre automatiquement. Pour savoir comment inscrire ce serveur auprès d’Azure File Sync, consultez la section suivante.

## <a name="register-windows-server-with-storage-sync-service"></a>Inscrire un serveur Windows Server au service de synchronisation de stockage
Le fait d’inscrire Windows Server auprès d’un service de synchronisation de stockage établit une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. L’interface utilisateur d’inscription du serveur s’ouvre normalement automatiquement après l’installation de l’agent Azure File Sync. Si ce n’est pas le cas, vous pouvez l’ouvrir manuellement à partir de son emplacement de fichier : C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quand l’interface utilisateur d’inscription du serveur est ouverte, sélectionnez **Se connecter** pour commencer.

Une fois que vous êtes connecté, vous êtes invité à fournir les informations suivantes :

![Capture d’écran de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Abonnement Azure** : abonnement qui contient le service de synchronisation de stockage (consultez [Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service)). 
- **Groupe de ressources** : groupe de ressources qui contient le service de synchronisation de stockage.
- **Service de synchronisation de stockage** : nom du service de synchronisation de stockage auquel vous souhaitez vous inscrire.

Après avoir sélectionné les informations appropriées, sélectionnez **S’inscrire** pour effectuer l’inscription du serveur. Dans le cadre du processus d’inscription, vous êtes invité à effectuer une nouvelle connexion.

## <a name="create-a-sync-group"></a>Créer un groupe de synchronisation
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Un groupe de synchronisation doit contenir au moins un point de terminaison cloud, qui représente un partage de fichiers Azure, et un point de terminaison de serveur, qui représente un chemin d’accès sur Windows Server. Pour créer un groupe de synchronisation, dans le [portail Azure](https://portal.azure.com/), accédez à votre service de synchronisation de stockage, puis sélectionnez **+ Groupe de synchronisation** :

![Créer un groupe de synchronisation dans le portail Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Dans le volet qui s’ouvre, entrez les informations suivantes pour créer un groupe de synchronisation avec un point de terminaison cloud :

- **Nom du groupe de synchronisation** : nom du groupe de synchronisation à créer. Ce nom doit être unique au sein du service de synchronisation de stockage, mais vous pouvez choisir tout nom ayant un sens pour vous.
- **Abonnement** : abonnement dans lequel vous avez déployé le service de synchronisation de stockage, comme décrit dans [Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service).
- **Compte de stockage** : si vous sélectionnez **Sélectionner un compte de stockage**, un autre volet s’affiche dans lequel vous pouvez sélectionner le compte de stockage qui contient le partage de fichiers Azure avec lequel effectuer la synchronisation.
- **Partage de fichiers Azure** : nom du partage de fichiers Azure avec lequel vous souhaitez effectuer la synchronisation.

Pour ajouter un point de terminaison de serveur, accédez au nouveau groupe de synchronisation, puis sélectionnez **Ajouter un point de terminaison de serveur**.

![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Dans le volet **Ajouter un point de terminaison de serveur**, entrez les informations suivantes pour créer un point de terminaison de serveur :

- **Serveur inscrit** : nom du serveur ou du cluster où vous voulez créer le point de terminaison de serveur.
- **Chemin d’accès** : chemin d’accès Windows Server à synchroniser en tant qu’élément du groupe de synchronisation.
- **Hiérarchisation cloud** : commutateur pour activer ou désactiver la hiérarchisation cloud. La hiérarchisation cloud permet de hiérarchiser les fichiers rarement utilisés dans Azure Files.
- **Espace libre du volume** : quantité d’espace libre à réserver sur le volume sur lequel se trouve le point de terminaison de serveur. Par exemple, si l’espace libre du volume est défini à 50 % sur un volume ayant un seul point de terminaison de serveur, environ la moitié de la quantité de données est hiérarchisée dans Azure Files. Que la hiérarchisation cloud soit activée ou non, le partage de fichiers Azure dispose toujours d’une copie complète des données dans le groupe de synchronisation.

Pour ajouter le point de terminaison de serveur, sélectionnez **Créer**. Vos fichiers sont maintenant synchronisés entre le partage de fichiers Azure et Windows Server. 

> [!Important]  
> Vous pouvez apporter des modifications à un point de terminaison cloud ou un point de terminaison de serveur dans le groupe de synchronisation, et synchroniser vos fichiers avec les autres points de terminaison du groupe de synchronisation. Si vous apportez une modification au point de terminaison cloud (partage de fichiers Azure) directement, cette modification doit être détectée au préalable par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud toutes les 24 heures uniquement. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur Azure Files](storage-files-faq.md#afs-change-detection).

## <a name="onboarding-with-azure-file-sync"></a>Intégrer Azure File Sync
Voici les étapes recommandées pour intégrer Azure File Sync sans aucun temps d’arrêt tout en préservant toute la fidélité du fichier et la liste de contrôle d’accès (ACL) :
 
1.  Déployez un service de synchronisation du stockage.
2.  Créez un groupe de synchronisation.
3.  Installez l’agent Azure File Sync sur le serveur comportant le jeu de données complet.
4.  Inscrivez ce serveur et créez un point de terminaison de serveur sur le partage. 
5.  Laissez la synchronisation effectuer la totalité du chargement sur le partage de fichiers Azure (point de terminaison cloud).  
6.  Une fois le chargement initial terminé, installez l’agent Azure File Sync sur chacun des serveurs restants.
7.  Créez de nouveaux partages de fichiers sur tous les serveurs restants.
8.  Créez des points de terminaison de serveur sur les nouveaux partages de fichiers avec une stratégie de hiérarchisation cloud, si vous le souhaitez. (Cette étape requiert du stockage supplémentaire disponible pour la configuration initiale.)
9.  Laissez l’agent Azure File Sync effectuer une restauration rapide de l’espace de noms complet sans transfert de données réel. Après la synchronisation de l’espace de noms complet, le moteur de synchronisation remplira l’espace disque local en fonction de la stratégie de hiérarchisation cloud du point de terminaison de serveur. 
10. Vérifiez la synchronisation est terminée et testez votre topologie comme vous le souhaitez. 
11. Redirigez les utilisateurs et les applications vers ce nouveau partage.
12. Il est possible quoique non obligatoire de supprimer les partages en double sur les serveurs.
 
Si vous n’avez pas de stockage supplémentaire pour l’intégration initiale et que vous souhaitez utiliser les partages existants, vous pouvez préamorcer les données dans les partages de fichiers Azure. Cette approche est proposée si et seulement si vous êtes en mesure d’accepter des temps d’arrêt et de garantir l’absence totale de modification des données sur les partages du serveur pendant le processus d’intégration initial. 
 
1.  Assurez-vous que les données des différents serveurs ne changeront pas au cours du processus d’intégration.
2.  Préamorcez les partages de fichiers Azure avec les données du serveur à l’aide d’un outil de transfert de données sur le SMB, par exemple, Robocopy ou SMB Direct. AzCopy ne chargeant pas les données sur le SMB, il ne peut pas être utilisé pour le préamorçage.
3.  Créez une topologie Azure File Sync avec les points de terminaison de serveur souhaités pointant sur les partages existants.
4.  Laissez la synchronisation terminer le processus de rapprochement sur tous les points de terminaison. 
5.  Une fois le rapprochement terminé, vous pourrez ouvrir les partages pour les modifier.
 
Notez que l’approche par préamorçage possède actuellement quelques limitations. 
- La fidélité optimale sur les fichiers n’est pas conservée. Par exemple, les fichiers perdent les ACL et les timestamps.
- Les modifications de données effectuées sur le serveur avant que la topologie de synchronisation ne soit entièrement opérationnelle risquent de provoquer des conflits sur les points de terminaison de serveur.  
- Une fois le point de terminaison cloud créé, Azure File Sync exécute un processus de détection des fichiers dans le cloud avant de démarrer la synchronisation initiale. Le temps nécessaire à ce processus varie en fonction de différents facteurs, comme la vitesse du réseau, la bande passante disponible et le nombre de fichiers et de dossiers. Pour donner une estimation approximative dans la préversion, le processus de détection s’exécute approximativement à une vitesse de 10 fichiers/s. Par conséquent, même si le préamorçage est rapide, le délai global nécessaire pour obtenir un système entièrement opérationnel peut se révéler beaucoup plus long lorsque les données sont préamorcées dans le cloud.


## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrer un déploiement de la réplication DFS (DFS-R) vers Azure File Sync
Pour migrer un déploiement de DFS-R vers Azure File Sync :

1. Créez un groupe de synchronisation pour représenter la topologie DFS-R que vous remplacez.
2. Démarrez sur le serveur contenant le jeu de données complet de votre topologie DFS-R à migrer. Installez Azure File Sync sur ce serveur.
3. Inscrivez ce serveur et créez un point de terminaison de serveur pour le premier serveur à migrer. N’activez pas la hiérarchisation cloud.
4. Laissez l’ensemble de la synchronisation des données sur votre partage de fichiers Azure (point de terminaison de cloud).
5. Installez et inscrivez l’agent Azure File Sync sur tous les autres serveurs DFS-R.
6. Désactivez DFS-R. 
7. Créez un point de terminaison de serveur sur tous les serveurs DFS-R. N’activez pas la hiérarchisation cloud.
8. Vérifiez la synchronisation est terminée et testez votre topologie comme vous le souhaitez.
9. Mettez DFS-R hors service.
10. La hiérarchisation cloud peut maintenant être activée sur n’importe quel point de terminaison de serveur souhaité.

Pour plus d’informations, consultez [Interopérabilité d’Azure File Sync avec le système de fichiers DFS](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter ou supprimer un point de terminaison de serveur pour Azure File Sync](storage-sync-files-server-endpoint.md)
- [Inscrire ou désinscrire un serveur auprès d’Azure File Sync](storage-sync-files-server-registration.md)
