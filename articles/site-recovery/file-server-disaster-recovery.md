---
title: "Protéger un serveur de fichiers avec Azure Site Recovery"
description: "Cet article explique comment protéger un serveur de fichiers avec Azure Site Recovery."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 8c9d8dadcd6181d9894ab6ee7110841afdec5708
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Protéger un serveur de fichiers avec Azure Site Recovery 

Le service [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de reprise et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération de plusieurs charges de travail.

Cet article explique comment protéger un serveur de fichiers avec Azure Site Recovery, et fournit d’autres recommandations adaptées à différents environnements.     

- [Répliquer les ordinateurs serveurs de fichiers Azure IaaS](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Répliquer un serveur de fichiers local avec Azure Site Recovery](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Architecture du serveur de fichiers
L’objectif d’un système de partage de fichiers distribués est de fournir un environnement dans lequel un groupe d’utilisateurs dispersés géographiquement peut collaborer pour travailler efficacement sur des fichiers et avoir la garantie que ses spécifications relatives à l’intégrité sont appliquées. Un écosystème de serveur de fichiers local classique qui prend en charge un grand nombre d’utilisateurs simultanés et un grand nombre d’éléments de contenu utilise la réplication du système de fichiers DFS (DFSR) pour la planification de la réplication et la limitation de bande passante. La réplication DFS utilise un algorithme appelé « RPC (compression différentielle à distance) », qui peut être utilisé pour mettre à jour efficacement les fichiers sur un réseau à bande passante limitée. Il détecte les insertions, les suppressions et les réorganisations de données dans les fichiers, permettant à DFSR de répliquer uniquement les blocs de fichiers modifiés quand des fichiers sont mis à jour. Il existe également des environnements de serveur de fichiers dans lesquels des sauvegardes quotidiennes sont effectuées pendant les heures creuses, qui répondent aux besoins en cas de sinistre, et il n’y a pas d’implémentation de la réplication DFS.

La topologie suivante illustre l’environnement de serveur de fichiers avec la réplication DFS implémentée.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

Dans la référence ci-dessus, plusieurs serveurs de fichiers, appelés « membres », participent activement à la réplication des fichiers dans un groupe de réplication. Le contenu du dossier répliqué sera accessible à tous les clients qui enverront des requêtes à l’un des membres, même en cas de mise hors connexion de l’un des membres.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Recommandations relatives à la récupération d’urgence pour les serveurs de fichiers :

1.  Répliquez un serveur de fichiers avec Azure Site Recovery : Les serveurs de fichiers peuvent être répliqués sur Azure à l’aide d’Azure Site Recovery. Quand un ou plusieurs des serveurs de fichiers locaux ne sont pas accessibles, les machines virtuelles de récupération peuvent être mises en place dans Azure. Elles peuvent alors traiter les demandes des clients, en local, à condition qu’il existe une connectivité VPN de site à site et qu’Active Directory soit configuré dans Azure. Cela est possible dans le cas d’un environnement DFSR configuré ou d’un environnement de serveur de fichiers simple sans DFSR. 

2.  Étendez DFSR à une machine virtuelle Azure IaaS : Dans un environnement de serveur de fichiers en cluster avec DFSR implémenté, l’approche conseillée consiste à étendre le service DFSR local à Azure. Une machine virtuelle Azure est alors activée pour jouer le rôle de serveur de fichiers. 

    Une fois les dépendances de connectivité VPN de site à site et Active Directory gérées et DFSR appliqué, quand un ou plusieurs des serveurs de fichiers locaux ne sont pas accessibles, les clients peuvent toujours se connecter à la machine virtuelle Azure, qui traitera les demandes.

    Cette approche est conseillée dans le cas où les configurations de vos machines virtuelles ne sont pas prises en charge par Azure Site Recovery, par exemple pour un disque de cluster partagé (qui est parfois couramment utilisé dans les environnements de serveur de fichiers).  DFSR fonctionne également bien dans des environnements à faible bande passante avec un taux de variation moyen. Le coût supplémentaire que représente le fait d’avoir une machine virtuelle Azure opérationnelle en permanence doit également être compensé ainsi.  

3.  Utilisez le service Azure File Sync pour répliquer vos fichiers : Si vous préparez votre migration vers le cloud, ou que vous utilisez déjà une machine virtuelle Azure, nous vous suggérons d’utiliser le service Azure File Sync, qui offre une synchronisation de partages de fichiers entièrement managés dans le cloud qui sont accessibles par le biais du protocole [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard. Les partages de fichiers Azure peuvent ensuite être montés simultanément par des déploiements cloud ou locaux de Windows, Linux et MacOS. 

Le schéma suivant donne une représentation graphique destinée à simplifier le choix de la stratégie à utiliser dans votre environnement de serveur de fichiers.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Facteurs à prendre en compte quand vous prenez des décisions sur la récupération d’urgence vers Azure

|Environnement  |Recommandation  |Éléments à prendre en considération |
|---------|---------|---------|
|Environnement de serveur de fichiers avec/sans DFSR|   [Utiliser Azure Site Recovery pour la réplication](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery ne prend pas en charge le cluster de disque partagé, NAS. Si votre environnement utilise l’une de ces configurations, utilisez l’une des autres approches de façon appropriée. <br> Azure Site Recovery ne prend pas en charge SMB 3.0, ce qui signifie que la machine virtuelle répliquée intègre les modifications apportées aux fichiers uniquement quand celles-ci sont mises à jour dans l’emplacement d’origine du fichier.
|Environnement de serveur de fichiers avec DFSR     |  [Étendre DFSR à une machine virtuelle Azure IaaS :](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR fonctionne bien dans les environnements à bande passante extrêmement réduite. Cette approche exige toutefois une machine virtuelle Azure opérationnelle en permanence. Vous devez tenir compte du coût de la machine virtuelle dans votre planification.         |
|Machine virtuelle Azure IaaS     |     [Azure File Sync](#use-azure-file-sync-service-to-replicate-your-files)   |     Dans un scénario de récupération d’urgence, si vous utilisez Azure File Sync, vous devez effectuer des actions manuelles pendant le basculement pour garantir que les partages de fichiers sont accessibles de manière transparente à l’ordinateur client. AFS requiert que le port 445 soit ouvert à partir de l’ordinateur client.     |


### <a name="site-recovery-support"></a>Prise en charge de Site Recovery
Étant donné que la réplication Site Recovery est indépendante des applications, les recommandations indiquées ici sont censées être vraies pour les scénarios suivants :
| Source    |Vers un site secondaire    |Vers Azure
|---------|---------|---------|
|Azure| -|Oui|
|Hyper-V|   Oui |Oui
|VMware |Oui|   Oui
|Serveur physique|   Oui |Oui
 

> [!IMPORTANT]
> Avant de passer à l’une des trois approches ci-dessous, vérifiez que les dépendances suivantes sont prises en charge :

**Connectivité de site à site** : une connexion directe entre le site local et le réseau Azure doit être établie pour permettre la communication entre les serveurs.  Cela peut être assuré par une connexion VPN de site à site sécurisée à un réseau virtuel Microsoft Azure qui sera utilisé comme site de récupération d’urgence.  
Reportez-vous à [Établir une connexion VPN site à site entre un site local et un réseau Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory** : DFSR dépend d’Active Directory.  Cela signifie que la forêt Active Directory avec des contrôleurs de domaine locaux est étendue au site de récupération d’urgence dans Azure. Même si vous n’utilisez pas DFSR quand un accès doit être accordé/vérifié pour les utilisateurs autorisés comme dans la plupart des organisations, ces étapes doivent être effectuées.
Reportez-vous à [Étendre Active Directory local à Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recommandations relatives à la récupération d’urgence pour les machines virtuelles Azure IaaS

Si vous configurez et gérez la récupération d’urgence de serveurs de fichiers hébergés sur des machines virtuelles Azure IaaS, vous avez le choix entre deux options, selon que vous souhaitez ou non passer à [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Utiliser Azure File Sync](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Utiliser Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Utiliser le service Azure File Sync pour répliquer des fichiers hébergés sur une machine virtuelle IaaS

**Azure Files** peut être utilisé pour remplacer complètement ou compléter des serveurs de fichiers locaux classiques ou des appareils NAS. Azure Files peut également être répliqué avec la synchronisation de fichiers Azure vers des serveurs Windows, localement ou dans le cloud, pour une mise en cache performante et distribuée des données là où elles sont utilisées. Les étapes suivantes décrivent en détail la recommandation de récupération d’urgence pour les machines virtuelles Azure qui exécutent les mêmes fonctionnalités que les serveurs de fichiers classiques :
1.  Protégez les machines utilisant Azure Site Recovery à l’aide des étapes mentionnées [ici](azure-to-azure-quickstart.md).
2.  Utilisez Azure File Sync pour répliquer dans le cloud des fichiers provenant de la machine virtuelle qui fait office de serveur de fichiers.
3.  Utilisez la fonctionnalité de [plan de récupération](site-recovery-create-recovery-plans.md) Azure Site Recovery pour ajouter des scripts afin de [monter le partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) et d’accéder au partage dans votre machine virtuelle.

Les étapes ci-dessous décrivent brièvement comment utiliser le service Azure File Sync :

1. [Créez un compte de stockage dans Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Si vous avez choisi le stockage géoredondant avec accès en lecture (RA-GRS) pour vos comptes de stockage, vous obtenez un accès en lecture à vos données à partir de la région secondaire en cas de sinistre. Pour plus d’informations, reportez-vous aux [stratégies de récupération d’urgence des partages de fichiers Azure](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Créer un partage de fichiers](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Déployez Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) sur votre serveur de fichiers Azure.
4. Créez un groupe de synchronisation : les points de terminaison se trouvant dans un groupe de synchronisation sont synchronisés entre eux. Un groupe de synchronisation doit contenir au moins un point de terminaison cloud, qui représente un partage de fichiers Azure, et un point de terminaison de serveur, qui représente un chemin d’accès sur un serveur Windows Server.
5.  Vos fichiers restent désormais synchronisés sur le partage de fichiers Azure et sur votre serveur local.
6.  En cas de sinistre dans votre environnement local, effectuez un basculement à l’aide d’un [plan de récupération](site-recovery-create-recovery-plans.md), puis ajoutez le script afin de [monter le partage de fichiers Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) et d’accéder au partage dans votre machine virtuelle.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Répliquer une machine virtuelle de serveur de fichiers IaaS avec Azure Site Recovery

Si vous avez des clients locaux qui accèdent à la machine virtuelle de serveur de fichiers IaaS, effectuez également les deux premières étapes. Sinon, passez à l’étape 3.

1. Établissez une connexion VPN site à site entre un site local et un réseau Azure.
1. Étendez Active Directory local.
1. [Configurez la récupération d’urgence](azure-to-azure-tutorial-enable-replication.md) pour l’ordinateur serveur de fichiers IaaS dans une région secondaire.


Pour plus d’informations sur la récupération d’urgence dans une région secondaire, reportez-vous [ici](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Répliquer un serveur de fichiers local avec Azure Site Recovery

Les étapes suivantes décrivent en détail la réplication pour une machine virtuelle VMware. Pour connaître la procédure de réplication d’une machine virtuelle Hyper-V, consultez [cet article](tutorial-hyper-v-to-azure.md).

1.  [Préparez les ressources Azure](tutorial-prepare-azure.md) pour la réplication de machines locales.
2.  Établissez une connexion VPN site à site entre un site local et un réseau Azure.  
3. Étendez Active Directory local.
4.  [Préparez les serveurs VMware locaux](tutorial-prepare-on-premises-vmware.md).
5.  [Configurez la récupération d’urgence](tutorial-vmware-to-azure.md) vers Azure pour des machines virtuelles locales.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Étendre DFSR à une machine virtuelle Azure IaaS

1.  Établissez une connexion VPN site à site entre un site local et un réseau Azure. 
2.  Étendez Active Directory local.
3.  [Créez et provisionnez une machine virtuelle de serveur de fichiers](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) sur le réseau virtuel Windows Azure.
Vérifiez que la machine virtuelle est ajoutée au même réseau virtuel Windows Azure, lequel dispose de l’inter-connectivité avec l’environnement local. 
4.  Installez et [configurez la réplication DFS](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) sur Windows Server.
5.  [Implémentez un espace de noms DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Une fois l’espace de noms DFS implémenté, il est possible d’effectuer le basculement de dossiers partagés de sites de production vers des sites de récupération d’urgence en mettant à jour les cibles de dossiers de l’espace de noms DFS.  Une fois ces modifications de l’espace de noms DFS répliquées via Active Directory, les utilisateurs sont connectés de manière transparente aux cibles de dossiers appropriées.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Utiliser le service Azure File Sync pour répliquer vos fichiers locaux
Le service Azure File Sync vous permet de répliquer les fichiers souhaités dans le cloud. Ainsi, en cas de sinistre et d’indisponibilité de votre serveur de fichiers local, vous pouvez monter les emplacements de fichier souhaités à partir du cloud et continuer à traiter les demandes provenant des ordinateurs clients.
L’approche conseillée pour l’intégration d’Azure File Sync à Azure Site Recovery est la suivante :
1.  Protégez les ordinateurs serveurs de fichiers avec Azure Site Recovery à l’aide des étapes mentionnées [ici](tutorial-vmware-to-azure.md).
2.  Utilisez Azure File Sync pour répliquer dans le cloud des fichiers provenant de la machine qui fait office de serveur de fichiers.
3.  Utilisez la fonctionnalité de plan de récupération Azure Site Recovery pour ajouter des scripts destinés à monter le partage de fichiers Azure sur la machine virtuelle de serveur de fichiers basculée dans Azure.

Les étapes suivantes décrivent en détail l’utilisation du service Azure File Sync :

1. [Créez un compte de stockage dans Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Si vous avez choisi le stockage géoredondant avec accès en lecture (RA-GRS) (recommandé) pour vos comptes de stockage, vous bénéficiez d’un accès en lecture à vos données à partir de la région secondaire en cas de sinistre. Pour plus d’informations, reportez-vous aux [stratégies de récupération d’urgence des partages de fichiers Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Créer un partage de fichiers](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Déployez Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) dans votre serveur de fichiers local.
4. Créez un groupe de synchronisation : les points de terminaison se trouvant dans un groupe de synchronisation sont synchronisés entre eux. Un groupe de synchronisation doit contenir au moins un point de terminaison cloud, qui représente un partage de fichiers Azure, et un point de terminaison de serveur, qui représente un chemin sur le serveur Windows Server local.
1. Vos fichiers restent désormais synchronisés sur le partage de fichiers Azure et sur votre serveur local.
6.  En cas de sinistre dans votre environnement local, effectuez un basculement à l’aide d’un [plan de récupération](site-recovery-create-recovery-plans.md), puis ajoutez le script afin de monter le partage de fichiers Azure et d’accéder au partage dans votre machine virtuelle.

> [!NOTE]
> Vérifiez que le port 445 est ouvert : Azure Files utilise le protocole SMB. SMB communique via le port TCP 445. Assurez-vous que votre pare-feu ne bloque pas les ports TCP 445 à partir de la machine cliente.


## <a name="doing-a-test-failover"></a>Exécution d’un test de basculement

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
2.  Cliquez sur le plan de récupération créé pour l’environnement de serveur de fichiers.
3.  Cliquez sur Test de basculement.
4.  Sélectionnez un point de récupération et un réseau virtuel Azure pour démarrer le test de basculement.
5.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.
6.  Une fois les validations terminées, vous pouvez cliquer sur « Nettoyage du test de basculement » sur le plan de récupération. L’environnement de test de basculement est alors nettoyé.

Pour plus d’informations sur l’exécution d’un test de basculement, reportez-vous [ici](site-recovery-test-failover-to-azure.md).

Pour obtenir des conseils sur l’exécution d’un test de basculement pour Active Directory et DNS, reportez-vous à [Considérations relatives au test de basculement pour Active Directory et DNS](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Exécution d’un basculement

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
2.  Cliquez sur le plan de récupération créé pour l’environnement de serveur de fichiers.
3.  Cliquez sur « Basculement ».
4.  Sélectionnez un point de récupération pour démarrer le processus de basculement.

Pour plus d’informations sur l’exécution d’un basculement, consultez [cet article](site-recovery-failover.md).
