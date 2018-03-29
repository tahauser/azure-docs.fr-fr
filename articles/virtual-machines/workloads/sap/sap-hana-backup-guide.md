---
title: Guide de sauvegarde pour SAP HANA sur Machines Virtuelles Azure | Microsoft Docs
description: Le guide de sauvegarde pour SAP HANA couvre deux méthodes de sauvegarde clés pour SAP HANA sur machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guide de sauvegarde pour SAP HANA sur Machines Virtuelles Azure

## <a name="getting-started"></a>Mise en route

Le guide de sauvegarde pour SAP HANA exécuté sur des machines virtuelles Azure aborde uniquement les sujets spécifiques à Azure. Pour ce qui concerne les éléments généraux associés à la sauvegarde SAP HANA, consultez la documentation sur SAP HANA (voir la section _Documentation sur la sauvegarde SAP HANA_ plus loin dans cet article).

Cet article couvre deux méthodes de sauvegarde clés pour SAP HANA sur machines virtuelles Azure :

- Sauvegarde HANA sur le système de fichiers dans une machine virtuelle Azure Linux (voir [Sauvegarde SAP HANA sur Azure au niveau fichier](sap-hana-backup-file-level.md))
- Sauvegarde HANA à partir de captures instantanées de stockage en utilisant la fonctionnalité de capture instantanée d’Azure Storage Blob manuellement ou le service Azure Backup (voir [Sauvegarde SAP HANA à partir de captures instantanées de stockage](sap-hana-backup-storage-snapshots.md))

SAP HANA offre une API de sauvegarde qui permet l’intégration directe d’outils de sauvegarde tiers à SAP HANA. (Cet aspect n’est pas couvert dans ce guide.) Actuellement, il n’existe aucune intégration directe de SAP HANA au service Azure Backup basée sur cette API.

SAP HANA est officiellement pris en charge sur les machines virtuelles Azure de type GS5 en tant qu’instance unique avec une restriction supplémentaire pour les charges de travail OLAP (voir [Find Certified IaaS Platforms](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Trouver les plateformes IaaS certifiées) sur le site web SAP). Cet article sera mis à jour lors de la mise à disposition de nouvelles offres pour SAP HANA sur Azure.

Une solution SAP HANA hybride est également disponible sur Azure. Dans le cadre de cette solution, SAP HANA s’exécute de façon non virtualisée sur des serveurs physiques. Toutefois, ce guide de sauvegarde SAP HANA pour Azure couvre un environnement Azure pur où SAP HANA s’exécute dans une machine virtuelle Azure et non sur de &quot;grandes instances&quot;. Consultez l’article [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](hana-overview-architecture.md) pour plus d’informations sur cette solution de sauvegarde sur de &quot;grandes instances&quot;, basée sur des captures instantanées de stockage.

Vous trouverez des informations générales sur les produits SAP pris en charge par Azure dans la [note SAP 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Les trois schémas suivants fournissent une vue d’ensemble des options de sauvegarde SAP HANA utilisant actuellement les fonctionnalités natives d’Azure. Ils montrent également les trois scénarios de sauvegarde futurs potentiels. Les articles connexes [Sauvegarde SAP HANA sur Azure au niveau fichier](sap-hana-backup-file-level.md) et [Sauvegarde SAP HANA à partir de captures instantanées de stockage](sap-hana-backup-storage-snapshots.md) décrivent ces options plus en détail et abordent notamment les considérations de taille et de performance des sauvegardes SAP HANA multi-téraoctets.

![Ce schéma illustre deux possibilités d’enregistrement de l’état actuel de la machine virtuelle.](media/sap-hana-backup-guide/image001.png)

Ce schéma illustre la possibilité d’enregistrer l’état actuel de la machine virtuelle par le biais du service Azure Backup ou à l’aide d’une capture instantanée manuelle des disques de machine virtuelle. Cette approche permet de ne pas avoir à gérer les sauvegardes SAP HANA. Ce scénario impliquant des captures instantanées de disque pose les défis de la cohérence du système de fichiers et de l’état du disque cohérent au niveau applicatif. L’aspect cohérence est couvert dans la section _Cohérence des données SAP HANA lors de la création de captures instantanées de stockage_ plus loin dans cet article. Les fonctionnalités et restrictions du service Azure Backup liées aux sauvegardes SAP HANA sont également abordées plus loin dans cet article.

![Ce schéma illustre les possibilités de sauvegarde de fichiers SAP HANA dans la machine virtuelle.](media/sap-hana-backup-guide/image002.png)

Ce schéma illustre les possibilités de sauvegarde de fichiers SAP HANA dans la machine virtuelle et de stockage des fichiers de sauvegarde HANA à un autre emplacement à l’aide de différents outils. Une sauvegarde HANA nécessite plus de temps qu’une solution de sauvegarde basée sur des captures instantanées. Cependant, elle présente certains avantages en termes d’intégrité et de cohérence. Vous trouverez des informations plus détaillées à ce sujet plus loin dans cet article.

![Ce schéma illustre un scénario de sauvegarde SAP HANA futur potentiel.](media/sap-hana-backup-guide/image003.png)

Ce schéma illustre un scénario de sauvegarde SAP HANA futur potentiel. Si SAP HANA autorisait à effectuer des sauvegardes à partir d’une réplication secondaire, cela offrirait des possibilités supplémentaires en matière de stratégies de sauvegarde. D’après une publication dans le Wiki SAP HANA, cela n’est pas possible actuellement :

_&quot;Est-il est possible d’effectuer des sauvegardes du côté secondaire ?_

_Non, actuellement vous pouvez uniquement effectuer des sauvegardes de données et fichiers journaux du côté principal. Si la sauvegarde de fichier journal automatique est activée, après la prise de contrôle sur le côté secondaire, les sauvegardes de fichiers journaux sont automatiquement écrites ici.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Ressources SAP pour la sauvegarde HANA

### <a name="sap-hana-backup-documentation"></a>Documentation sur la sauvegarde SAP HANA

- [Introduction to SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US) (Introduction à l’administration de HANA SAP)
- [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planification de votre stratégie de sauvegarde et de récupération)
- [Schedule HANA Backup using ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html) (Planifier la sauvegarde HANA avec ABAP DBACOCKPIT)
- [Schedule Data Backups (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm) (Planifier des sauvegardes de données (cockpit SAP HANA))
- FAQ sur la sauvegarde SAP HANA dans la [note SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sur les captures instantanées de base de données et de stockage SAP HANA dans la [note SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Systèmes de fichiers en réseau inappropriés pour la sauvegarde et la récupération dans la [note SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Pourquoi utiliser la sauvegarde SAP HANA ?

Le stockage Azure offre dès le départ des performances élevées en termes de disponibilité et de fiabilité (voir [Présentation du stockage Microsoft Azure](../../../storage/common/storage-introduction.md) pour plus d’informations sur le stockage Azure).

Pour la &quot;sauvegarde&quot;, il convient au minimum de s’appuyer sur les SLA Azure en conservant les fichiers journaux et données SAP HANA sur des VHD Azure attachés à la machine virtuelle du serveur SAP HANA. Cette approche couvre les échecs de la machine virtuelle, mais pas les dommages potentiellement occasionnés aux fichiers journaux et données SAP HANA, ni les erreurs logiques comme la suppression accidentelle de données ou de fichiers. Les sauvegardes sont également requises pour des raisons juridiques ou de conformité. En bref, il est toujours nécessaire d’effectuer des sauvegardes SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Comment vérifier qu’une sauvegarde SAP HANA est correcte
Lorsque vous utilisez des captures instantanées de stockage, il est recommandé d’effectuer un test de restauration sur un autre système. Cette approche permet de s’assurer qu’une sauvegarde est correcte et que les processus internes de sauvegarde et de restauration fonctionnent comme prévu. S’il s’agit d’un obstacle majeur en local, il est beaucoup plus facile de procéder dans le cloud en fournissant temporairement les ressources nécessaires.

Gardez à l’esprit qu’il ne suffit pas d’effectuer une simple restauration et de vérifier si HANA est opérationnel et en cours d’exécution. Dans l’idéal, il convient d’exécuter une vérification de cohérence de table pour s’assurer que la base de données restaurée est correcte. SAP HANA offre plusieurs types de vérifications de cohérence. Ceux-ci sont décrits dans la [note SAP 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Vous trouverez également des informations sur la vérification de cohérence de table sur le site web SAP à la page [Table and Catalog Consistency Checks](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Vérifications de cohérence de table et de catalogue).

Pour les sauvegardes de fichiers standard, il n’est pas nécessaire d’effectuer un test de restauration. Deux outils SAP HANA permettent de vérifier la sauvegarde qui peut être utilisée pour la restauration : hdbbackupdiag et hdbbackupcheck. Consultez la page [Manually Checking Whether a Recovery is Possible](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) (Vérifier manuellement si une récupération est possible) pour plus d’informations sur ces outils.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Avantages et inconvénients de la sauvegarde HANA par rapport à la capture instantanée de stockage

SAP ne privilégie pas la sauvegarde HANA par rapport à la capture instantanée de stockage. Il répertorie leurs avantages et inconvénients, et chacun peut ainsi déterminer la méthode à utiliser en fonction de la situation et de la technologie de stockage disponible (voir [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planification de votre stratégie de sauvegarde et de récupération) pour plus d’informations).

Sur Azure, tenez compte du fait que la fonctionnalité de capture instantanée d’objets blob Azure ne garantit pas la cohérence du système de fichiers (voir la page [Using blob snapshots with PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) (Utilisation des captures instantanées d’objets blob avec PowerShell) pour plus d’informations). La section suivante, _Cohérence des données SAP HANA lors de la création de captures instantanées de stockage_, aborde certaines considérations relatives à cette fonctionnalité.

Il faut également comprendre les implications liées à la facturation en cas d’utilisation fréquente de captures instantanées d’objets blob, comme décrit dans l’article : [Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) (Comment les captures instantanées augmentent les frais). Cet aspect n’est pas aussi évident que lors de l’utilisation des disques virtuels Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Cohérence des données SAP HANA lors de la création de captures instantanées de stockage

Dans le cadre de la création de captures instantanées de stockage, la cohérence du système de fichiers et des applications représente un problème complexe. Pour éviter les problèmes, le plus simple consisterait à arrêter SAP HANA, voire la machine virtuelle entière. Un arrêt pourrait être effectué avec une démonstration ou un prototype, voire avec un système de développement. Cependant, ce procédé n’est pas envisageable avec un système de production.

Sur Azure, gardez à l’esprit que la fonctionnalité de capture instantanée d’objets blob Azure ne garantit pas la cohérence du système de fichiers. Toutefois, cela fonctionne correctement avec la fonctionnalité de capture instantanée SAP HANA lorsqu’un seul disque virtuel est impliqué. Cependant, même avec un seul disque, des éléments supplémentaires doivent être vérifiés. La [note SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) fournit des informations importantes sur les sauvegardes SAP HANA effectuées avec des captures instantanées de stockage. Par exemple, elle mentionne qu’avec le système de fichiers XFS, il est nécessaire d’exécuter **xfs\_freeze** avant de démarrer une capture instantanée de stockage pour garantir la cohérence (voir la page [xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) pour plus d’informations sur **xfs\_freeze**).

Le problème de la cohérence devient encore plus complexe lorsqu’un système de fichiers unique s’étend sur plusieurs disques/volumes, par exemple, lors de l’utilisation de mdadm ou de LVM et de l’entrelacement. La note SAP mentionnée plus haut indique ce qui suit :

_&quot;N’oubliez pas que le système de stockage doit garantir la cohérence des E/S lors de la création d’une capture instantanée de stockage par volume de données SAP HANA. Ainsi, la création d’une capture instantanée d’un volume de données spécifique au service SAP HANA doit être une opération atomique.&quot;_

En supposant qu’un système de fichiers XFS couvre quatre disques virtuels Azure, les étapes suivantes permettent d’obtenir une capture instantanée cohérente qui représente la zone de données HANA :

- Préparer la capture instantanée HANA
- Geler le système de fichiers (par exemple, à l’aide de **xfs\_freeze**)
- Créer toutes les captures instantanées d’objets blob nécessaires sur Azure
- Dégeler le système de fichiers
- Confirmer la capture instantanée HANA

Pour des raisons de sécurité, il est recommandé d’utiliser la procédure ci-dessus dans tous les cas, quel que soit le système de fichiers. S’il s’agit d’un seul disque ou d’un entrelacement, il convient d’utiliser mdadm ou LVM sur plusieurs disques.

Il est important de confirmer la capture instantanée HANA. En raison de la &quot;copie pour écriture&quot;, il se peut que SAP HANA ne nécessite pas d’espace disque supplémentaire en mode de préparation de capture instantanée. Par ailleurs, il n’est pas possible de démarrer de nouvelles sauvegardes tant que la capture instantanée SAP HANA n’a pas été confirmée.

Le service Azure Backup utilise des extensions de machine virtuelle Azure pour gérer la cohérence du système de fichiers. Ces extensions de machine virtuelle ne sont pas disponibles pour une utilisation autonome. Il restera nécessaire de gérer la cohérence de SAP HANA. Consultez l’article connexe [Sauvegarde SAP HANA sur Azure au niveau fichier](sap-hana-backup-file-level.md) pour plus d’informations.

### <a name="sap-hana-backup-scheduling-strategy"></a>Stratégie de planification de sauvegarde SAP HANA

L’article SAP HANA [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planification de votre stratégie de sauvegarde et de récupération) fournit un plan de base pour effectuer les sauvegardes :

- Capture instantanée de stockage (quotidienne)
- Sauvegarde de données complète dans un fichier ou au format bacint (hebdomadaire)
- Sauvegardes de fichiers journaux automatiques

Si vous le souhaitez, vous pouvez procéder sans captures instantanées de stockage. Celles-ci peuvent être remplacées par des sauvegardes HANA différentielles ou incrémentielles (voir [Delta Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Sauvegardes différentielles) pour plus d’informations).

Le guide d’administration HANA fournit un exemple de liste. Il suggère de récupérer SAP HANA à un point spécifique dans le temps à l’aide de la séquence de sauvegardes suivante :

1. Sauvegarde de données complète
2. Sauvegarde différentielle
3. Sauvegarde incrémentielle 1
4. Sauvegarde incrémentielle 2
5. Sauvegardes de fichiers journaux

Il n’est pas possible de fournir une recommandation générale quant à la planification exacte et à la fréquence d’un type de sauvegarde spécifique. Cela dépend beaucoup du client et du nombre de modifications de données se produisant sur le système. SAP fournit cependant une recommandation de base pouvant être considérée comme une directive générale. Celle-ci consiste à créer une sauvegarde HANA complète une fois par semaine.
Pour ce qui concerne les sauvegardes de fichiers journaux, consultez la documentation SAP HANA [Log Backups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm) (Sauvegardes de fichiers journaux).

SAP recommande également de nettoyer le catalogue de sauvegarde pour ne pas le laisser croître indéfiniment (voir [Housekeeping for Backup Catalog and Backup Storage](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm) (Nettoyage pour le catalogue et le stockage de sauvegarde) pour plus d’informations).

### <a name="sap-hana-configuration-files"></a>Fichiers de configuration SAP HANA

Comme indiqué dans le FAQ de la [note SAP 1642148](https://launchpad.support.sap.com/#/notes/1642148), les fichiers de configuration SAP HANA ne sont pas inclus dans une sauvegarde HANA standard. Ils ne sont pas essentiels à la restauration d’un système. La configuration HANA peut être modifiée manuellement après la restauration. Si vous souhaitez obtenir la même configuration personnalisée pendant le processus de restauration, il est nécessaire de sauvegarder les fichiers de configuration HANA séparément.

Si des sauvegardes HANA standard sont effectuées dans un système de fichiers HANA dédié, vous pouvez également copier les fichiers de configuration dans le même système de fichiers de sauvegarde, puis copier l’ensemble dans la destination de stockage finale, comme un Stockage Blob froid.

### <a name="sap-hana-cockpit"></a>Cockpit SAP HANA

Le cockpit SAP HANA offre la possibilité de surveiller et gérer SAP HANA via un navigateur. Il permet également de gérer les sauvegardes SAP HANA et, par conséquent, peut être utilisé comme alternative à SAP HANA Studio et ABAP DBACOCKPIT (voir [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) (Cockpit SAP HANA) pour plus d’informations).

![Cette illustration montre l’écran d’administration de base de données du cockpit SAP HANA.](media/sap-hana-backup-guide/image004.png)

Cette illustration montre l’écran d’administration de base de données du cockpit SAP HANA, avec la vignette de sauvegarde sur la gauche. L’affichage de la vignette de sauvegarde requiert des autorisations utilisateur appropriées pour le compte de connexion.

![Les sauvegardes peuvent être surveillées dans le cockpit SAP HANA lors de leur réalisation.](media/sap-hana-backup-guide/image005.png)

Les sauvegardes peuvent être surveillées dans le cockpit SAP HANA lors de leur réalisation. Toutes les informations de sauvegarde sont disponibles une fois celles-ci terminées.

![Exemple utilisant Firefox sur une machine virtuelle Azure SLES 12 avec le bureau Gnome](media/sap-hana-backup-guide/image006.png)

Les captures d’écran précédentes ont été effectuées à partir d’une machine virtuelle Microsoft Azure. Celle-ci illustre l’utilisation de Firefox sur une machine virtuelle Azure SLES 12 avec le bureau Gnome. Elle montre la possibilité de définir des planifications de sauvegarde SAP HANA dans le cockpit SAP HANA. La date et l’heure sont suggérées en tant que préfixes pour les fichiers de sauvegarde. Dans SAP HANA Studio, le préfixe par défaut est &quot;COMPLETE\_DATA\_BACKUP&quot; en cas de sauvegarde de fichiers complète. L’utilisation d’un préfixe unique est recommandée.

### <a name="sap-hana-backup-encryption"></a>Chiffrement de sauvegarde SAP HANA

SAP HANA assure le chiffrement des données et du journal. Si le journal et les données SAP HANA ne sont pas chiffrés, les sauvegardes ne le sont pas non plus. Il incombe au client d’utiliser une solution tierce pour chiffrer les sauvegardes SAP HANA. Consultez la page [Data and Log Volume Encryption](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) (Chiffrement des volumes de journaux et de données) pour en savoir plus sur le chiffrement SAP HANA.

Sur Microsoft Azure, un client peut utiliser la fonctionnalité de chiffrement de machine virtuelle IaaS pour réaliser le chiffrement. Par exemple, il peut utiliser des disques de données dédiés attachés à la machine virtuelle, qui sont exploités pour stocker les sauvegardes SAP HANA, puis effectuer des copies de ces disques.

Le service Azure Backup peut gérer des machines virtuelles/disques chiffrés (voir [Guide pratique de sauvegarde et restauration des machines virtuelles chiffrées avec Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Une autre option consiste à mettre à jour la machine virtuelle HANA SAP et ses disques sans chiffrement et à stocker les fichiers de sauvegarde SAP HANA dans un compte de stockage pour lequel le chiffrement a été activé (voir [Azure Storage Service Encryption pour les données au repos](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Configuration des tests

### <a name="test-virtual-machine-on-azure"></a>Machine virtuelle de test sur Azure

Une installation SAP HANA dans une machine virtuelle Azure GS5 a été utilisée pour les tests de sauvegarde/restauration suivants.

![Cette illustration montre une partie de la vue d’ensemble du Portail Azure pour la machine virtuelle de test HANA.](media/sap-hana-backup-guide/image007.png)

Cette illustration montre une partie de la vue d’ensemble du Portail Azure pour la machine virtuelle de test HANA.

### <a name="test-backup-size"></a>Taille de la sauvegarde de test

![Cette illustration est une capture de la console de sauvegarde de HANA Studio. Elle montre la taille du fichier de sauvegarde de 229 Go pour le serveur d’indexation HANA.](media/sap-hana-backup-guide/image008.png)

Une table fictive a été remplie de façon à obtenir une taille totale de sauvegarde de données de plus de 200 Go et ainsi traiter des données de performance réalistes. Cette illustration est une capture de la console de sauvegarde de HANA Studio. Elle montre la taille du fichier de sauvegarde de 229 Go pour le serveur d’indexation HANA. Pour les tests, le préfixe de sauvegarde par défaut « COMPLETE_DATA_BACKUP » dans SAP HANA Studio a été utilisé. Dans des systèmes de production réels, un préfixe plus utile doit être défini. Le cockpit SAP HANA suggère la date et l’heure.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Outil de test pour copier les fichiers directement dans le stockage Azure

L’outil blobxfer a été utilisé pour transférer des fichiers de sauvegarde SAP HANA directement dans le Stockage Blob Azure ou vers des partages de fichiers Azure. En effet, il prend en charge les deux cibles et peut être facilement intégré à des scripts d’automatisation grâce à son interface de ligne de commande. L’outil blobxfer est disponible sur [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Estimation de la taille de la sauvegarde de test

Il est important d’estimer la taille de la sauvegarde de SAP HANA. En raison du parallélisme lors d’une copie de fichiers, cette estimation permet d’améliorer les performances en définissant la taille maximale des fichiers de sauvegarde pour un certain nombre de fichiers de sauvegarde. (Ces aspects particuliers sont expliqués plus loin dans cet article.) Il convient également de décider si la sauvegarde doit être complète ou si elle doit être différentielle ou incrémentielle.

Heureusement, il existe une instruction SQL simple qui estime la taille des fichiers de sauvegarde : **select \* from M\_BACKUP\_SIZE\_ESTIMATIONS** (voir [Estimate the Space Needed in the File System for a Data Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm) (Estimer l’espace requis dans le système de fichiers pour une sauvegarde de données) pour plus d’informations).

![La sortie de cette instruction SQL correspond de façon quasiment identique à la taille réelle de la sauvegarde de données complète sur disque.](media/sap-hana-backup-guide/image009.png)

Pour le système de test, la sortie de cette instruction SQL correspond de façon quasiment identique à la taille réelle de la sauvegarde de données complète sur disque.

### <a name="test-hana-backup-file-size"></a>Taille du fichier de la sauvegarde de test HANA

![La console de sauvegarde HANA Studio permet de limiter la taille maximale des fichiers de sauvegarde HANA.](media/sap-hana-backup-guide/image010.png)

La console de sauvegarde HANA Studio permet de limiter la taille maximale des fichiers de sauvegarde HANA. Dans l’exemple d’environnement, cette fonctionnalité permet d’obtenir plusieurs fichiers de sauvegarde de taille inférieure au lieu d’un fichier de sauvegarde unique de 230 Go. Cette taille de fichier inférieure a un impact significatif sur les performances (consultez l’article connexe [Sauvegarde SAP HANA sur Azure au niveau fichier](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Résumé

Les tableaux suivants s’appuient sur les résultats de test pour présenter les avantages et inconvénients des solutions permettant de sauvegarder une base de données SAP HANA s’exécutant sur des machines virtuelles Azure.

**Sauvegarder SAP HANA sur le système de fichiers, puis copier les fichiers de sauvegarde dans la destination de sauvegarde finale**

|Solution                                           |Avantages                                 |Inconvénients                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Conservation des sauvegardes HANA sur des disques de machine virtuelle                      |Aucun effort de gestion supplémentaire     |Occupation d’espace sur le disque de machine virtuelle local           |
|Outil blobxfer pour copier les fichiers de sauvegarde dans le Stockage Blob |Parallélisme pour copier plusieurs fichiers ; possibilité d’utiliser le Stockage Blob froid | Maintenance de l’outil supplémentaire et scripts personnalisés | 
|Copie d’objets blob avec Powershell ou l’interface CLI                    |Aucun outil supplémentaire nécessaire ; possibilité d’utiliser Azure Powershell ou l’interface CLI |Processus manuel : le client doit gérer les scripts et les objets blob copiés pour la restauration|
|Copie vers un partage NFS                                  |Post-traitement des fichiers de sauvegarde sur une autre machine virtuelle sans impact sur le serveur HANA|Lenteur du processus de copie|
|Copie avec blobxfer vers le service Azure File                |Pas d’occupation d’espace sur les disques de machine virtuelle locaux|Pas de prise en charge de l’écriture directe par la sauvegarde HANA ; restriction de la taille du partage de fichiers (actuellement 5 To)|
|Agent Azure Backup                                 | Serait la solution privilégiée         | Actuellement non disponible sur Linux    |



**Sauvegarde de SAP HANA à partir de captures instantanées de stockage**

|Solution                                           |Avantages                                 |Inconvénients                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Service Azure Backup                               | Possibilité d’effectuer une sauvegarde de machine virtuelle à partir de captures instantanées d’objets blob | Lorsque vous n’utilisez ne pas la restauration au niveau fichier, cette solution requiert la création d’une machine virtuelle pour le processus de restauration, ce qui nécessite une nouvelle clé de licence SAP HANA.|
|Captures instantanées d’objets blob manuelles                              | Flexibilité pour créer et restaurer des disques de machine virtuelle spécifiques sans modifier l’ID de machine virtuelle unique|Processus entièrement manuel qui doit être effectué par le client|

## <a name="next-steps"></a>Étapes suivantes
* L’article [Sauvegarde SAP HANA sur Azure au niveau fichier](sap-hana-backup-file-level.md) décrit l’option de sauvegarde basée sur des fichiers.
* L’article [Sauvegarde SAP HANA à partir de captures instantanées de stockage](sap-hana-backup-storage-snapshots.md) décrit l’option de sauvegarde à partir de captures instantanées de stockage.
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
