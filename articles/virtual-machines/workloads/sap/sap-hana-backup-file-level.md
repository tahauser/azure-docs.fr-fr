---
title: Sauvegarde SAP HANA sur Azure au niveau fichier | Microsoft Docs
description: Il existe deux grandes méthodes permettant d’effectuer des sauvegardes SAP HANA sur des machines virtuelles Azure. Cet article aborde la sauvegarde SAP HANA sur Azure au niveau fichier.
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
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Sauvegarde SAP HANA sur Azure au niveau fichier

## <a name="introduction"></a>Introduction

Ce document fait partie d’une série d’articles connexes en trois parties dédiés à la sauvegarde SAP HANA. L’article [Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure](./sap-hana-backup-guide.md) fournit une vue d’ensemble et des informations sur la mise en route, et l’article [Sauvegarde SAP HANA à partir de captures instantanées de stockage](./sap-hana-backup-storage-snapshots.md) couvre l’option de sauvegarde à partir de captures instantanées de stockage.

En étudiant les tailles de machine virtuelle Azure, vous pouvez voir qu’une machine virtuelle GS5 permet d’associer 64 disques de données. Pour les grands systèmes SAP HANA, un nombre important de disques peut déjà être utilisé pour les données et les fichiers journaux, éventuellement en association avec un RAID logiciel pour un débit d’E/S de disque optimal. Dans ce cas, la question est de savoir où stocker les fichiers de sauvegarde SAP HANA, qui risqueraient de saturer les disques de données attachés au fil du temps. Pour consulter les tableaux des tailles de machine virtuelle Azure, voir [Tailles des machines virtuelles Linux dans Azure](../../linux/sizes.md).

Aucune intégration de sauvegarde SAP HANA n’est disponible avec le service Azure Backup pour l’instant. La méthode standard pour gérer la sauvegarde/restauration au niveau fichier est d’utiliser une sauvegarde basée sur les fichiers via SAP HANA Studio ou via les instructions SQL SAP HANA. Pour plus d’informations, voir [SQL SAP HANA et référence des vues système](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Cette illustration montre la boîte de dialogue de l’élément du menu de sauvegarde dans SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Cette illustration montre la boîte de dialogue de l’élément du menu de sauvegarde dans SAP HANA Studio. Lorsque vous choisissez le type &quot;fichier&quot;, vous devez indiquer le chemin d’accès de l’emplacement dans le système de fichiers où SAP HANA inscrit les fichiers de sauvegarde. La restauration fonctionne de la même façon.

Bien que ce choix semble simple et direct, certains points doivent être pris en compte. Comme indiqué précédemment, les machines virtuelles Azure ont une limite en termes de nombre de disques de données qui peuvent être liés. Les systèmes de fichiers de la machine virtuelle ne disposent peut-être pas de la capacité nécessaire pour stocker les fichiers de sauvegarde SAP HANA, en fonction de la taille de la base de données et des contraintes de débit du disque. Cela peut nécessiter que le RAID logiciel utilise un système d’entrelacement de plusieurs disques de données. Plus loin dans cet article, nous aborderons différentes options permettant de déplacer ces fichiers de sauvegarde et de gérer les restrictions concernant la taille des fichiers et les performances lors du traitement de plusieurs téraoctets de données.

Une autre possibilité, qui offre davantage de liberté en ce qui concerne la capacité totale, est le stockage Blob Azure. Bien que les objets blob soient également limités à 1 To, la capacité totale d’un conteneur d’objets blob unique est actuellement de 500 To. En outre, cela offre également la possibilité aux clients de sélectionner ce que l’on appelle le stockage Blob &quot;froid&quot;, qui représente un avantage en termes de coût. Pour plus d’informations sur le stockage Blob froid, voir [Stockage Blob Azure : niveaux de stockage chauds et froids](../../../storage/blobs/storage-blob-storage-tiers.md).

Pour plus de sécurité, utilisez un compte de stockage géo-répliqué pour stocker les sauvegardes SAP HANA. Pour plus d’informations sur la réplication des comptes de stockage, voir [Réplication Azure Storage](../../../storage/common/storage-redundancy.md).

Vous pouvez placer des disques durs virtuels dédiés aux sauvegardes SAP HANA dans un compte de stockage de sauvegarde dédié géo-répliqué. Vous pouvez également copier les disques durs virtuels sur lesquels sont enregistrées les sauvegardes SAP HANA vers un compte de stockage géo-répliqué, ou vers un compte de stockage qui se trouve dans une autre région.

## <a name="azure-backup-agent"></a>Agent Azure Backup

Azure Backup permet non seulement de sauvegarder les machines virtuelles dans leur intégralité, mais également les fichiers et répertoires via l’agent de sauvegarde, qui doit être installé sur le système d’exploitation invité. Mais depuis décembre 2016, cet agent est uniquement pris en charge sous Windows (voir [Sauvegarder un client Windows ou un serveur Windows dans Azure à l’aide du modèle de déploiement Resource Manager](../../../backup/backup-configure-vault.md)).

Une autre solution consiste à copier les fichiers de sauvegarde SAP HANA sur une machine virtuelle Windows dans Azure (par exemple, via le partage SAMBA) et à utiliser ensuite l’agent Azure Backup à partir de là. Bien que ce soit techniquement possible, cela augmenterait la complexité et ralentirait la sauvegarde ou la restauration en raison de la copie entre Linux et la machine virtuelle Windows. Il n’est donc pas recommandé d’opter pour cette approche.

## <a name="azure-blobxfer-utility-details"></a>Détails de l’utilitaire blobxfer Azure

Pour stocker les répertoires et les fichiers sur le stockage Azure, vous pouvez utiliser l’interface de ligne de commande ou PowerShell, ou encore développer un outil à l’aide d’un des [kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/). Il existe également un utilitaire prêt à l’emploi, AzCopy, qui permet de copier des données vers le stockage Azure, mais il est uniquement compatible avec Windows (voir [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../../../storage/common/storage-use-azcopy.md)).

Par conséquent, blobxfer a été utilisé pour copier les fichiers de sauvegarde SAP HANA. Il s’agit d’un outil Open Source utilisé par de nombreux clients dans leur environnement de production. Il est disponible sur [GitHub](https://github.com/Azure/blobxfer). Cet outil permet de copier des données directement sur le stockage Blob Azure ou sur le système de partage de fichiers Azure. Il inclut également de nombreuses fonctionnalités utiles, telles que le code de hachage md5 ou le parallélisme automatique lors de la copie d’un répertoire contenant plusieurs fichiers.

## <a name="sap-hana-backup-performance"></a>Performances de sauvegarde SAP HANA

![Cette capture d’écran provient de la console de sauvegarde SAP HANA dans SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Cette capture d’écran provient de la console de sauvegarde SAP HANA dans SAP HANA Studio. Environ 42 minutes ont été nécessaires pour effectuer la sauvegarde de 230 Go sur un seul disque de stockage Azure standard attaché à la machine virtuelle HANA à l’aide du système de fichiers XFS.

![Cette capture d’écran provient de YaST sur la machine virtuelle de test SAP HANA](media/sap-hana-backup-file-level/image024.png)

Cette capture d’écran provient de YaST sur la machine virtuelle de test SAP HANA. Vous pouvez voir le disque de 1 To utilisé pour la sauvegarde SAP HANA, comme indiqué précédemment. Il a fallu environ 42 minutes pour sauvegarder 230 Go. En outre, cinq disques de 200 Go ont été liés et le RAID logiciel md0 a été créé, avec un entrelacement sur ces cinq disques de données Azure.

![Répétition de la même sauvegarde sur le RAID logiciel avec entrelacement sur cinq disques de données de stockage Azure standard liés](media/sap-hana-backup-file-level/image025.png)

La répétition de la même sauvegarde sur le RAID logiciel avec entrelacement sur cinq disques de données de stockage Azure standard liés a fait passer le temps nécessaire à la sauvegarde de 42 minutes à 10 minutes. Les disques ont été liés sans mise en cache sur la machine virtuelle. L’importance du débit d’écriture sur le disque en matière de temps de sauvegarde est donc évidente. Vous pouvez ensuite passer au stockage Azure Premium afin d’accélérer encore plus le processus, pour des performances optimales. En général, le stockage Azure Premium doit être utilisé pour les systèmes de production.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copier les fichiers de sauvegarde SAP HANA vers le stockage Blob Azure

À compter de décembre 2016, la meilleure option pour stocker rapidement les fichiers de sauvegarde SAP HANA est le stockage Blob Azure. Un conteneur d’objets blob unique a une limite de 500 To, suffisante pour la plupart des systèmes SAP HANA s’exécutant sur une machine virtuelle GS5 dans Azure, afin de conserver les sauvegardes SAP HANA nécessaires. Les clients ont le choix entre le stockage Blob &quot;chaud&quot; et &quot;froid&quot; (voir [Stockage Blob Azure : niveaux de stockage chauds et froids](../../../storage/blobs/storage-blob-storage-tiers.md)).

Grâce à l’outil blobxfer, il est facile de copier les fichiers de sauvegarde SAP HANA directement dans le stockage Blob Azure.

![Vous pouvez voir ici les fichiers d’une sauvegarde de fichiers SAP HANA complète](media/sap-hana-backup-file-level/image026.png)

Vous pouvez voir ici les fichiers d’une sauvegarde de fichiers SAP HANA complète. Il existe quatre fichiers, dont le plus gros fait environ 230 Go.

![Il a fallu environ 3 000 secondes pour copier les 230 Go dans un conteneur d’objets blob du compte de stockage Azure standard](media/sap-hana-backup-file-level/image027.png)

En n’utilisant pas le code de hachage md5 lors du premier test, il a fallu environ 3 000 secondes pour copier les 230 Go dans un conteneur d’objets blob du compte de stockage Azure standard.

![Sur cette capture d’écran, vous pouvez voir son aspect sur le portail Azure](media/sap-hana-backup-file-level/image028.png)

Sur cette capture d’écran, vous pouvez voir son aspect sur le portail Azure. Un conteneur d’objets blob nommé &quot;sap hana-backups&quot; a été créé et inclut les quatre objets blob, qui correspondent aux fichiers de sauvegarde SAP HANA. L’un d’eux fait environ 230 Go.

La console de sauvegarde HANA Studio permet de limiter la taille de fichier maximale des fichiers de sauvegarde HANA. Dans l’environnement exemple, elle a amélioré les performances en permettant d’avoir plusieurs fichiers de sauvegarde plus petits, au lieu d’un gros fichier de 230 Go.

![La définition de la limite de taille de fichier de sauvegarde côté HANA n’améliore pas le temps de sauvegarde](media/sap-hana-backup-file-level/image029.png)

La définition de la limite de taille de fichier de sauvegarde côté HANA n’améliore pas le temps de sauvegarde, car les fichiers sont écrits de manière séquentielle, comme indiqué sur cette figure. La limite de taille de fichier a été définie sur 60 Go, donc la sauvegarde a créé quatre fichiers de données volumineux au lieu d’un seul fichier de 230 Go.

![Pour tester le parallélisme de l’outil blobxfer, la taille de fichier maximale pour les sauvegardes HANA a ensuite été définie sur 15 Go](media/sap-hana-backup-file-level/image030.png)

Pour tester le parallélisme de l’outil blobxfer, la taille de fichier maximale pour les sauvegardes HANA a ensuite été définie sur 15 Go, ce qui a engendré la création de 19 fichiers de sauvegarde. Cette configuration a fait passer le temps nécessaire pour que blobxfer copie les 230 Go sur le stockage Blob Azure de 3 000 secondes à 875 secondes.

Ce résultat est dû à la limite de 60 Mo/s pour l’écriture d’un objet blob Azure. Le parallélisme via plusieurs objets blob élimine le goulot d’étranglement, mais présente un inconvénient : l’augmentation des performances de l’outil blobxfer pour copier tous ces fichiers de sauvegarde HANA vers le stockage Blob Azure implique une charge plus importante sur la machine virtuelle HANA et sur le réseau. Le fonctionnement du système HANA s’en trouve affecté.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Copie blob des disques de données Azure dédiés dans le RAID logiciel de sauvegarde

Contrairement à la sauvegarde manuelle de disque de données de machine virtuelle, cette approche ne nécessite pas de sauvegarder tous les disques de données sur une machine virtuelle pour enregistrer l’installation SAP dans son ensemble, y compris les données HANA, les fichiers journaux HANA et les fichiers de configuration. Au lieu de cela, l’idée est de disposer de RAID logiciels dédiés avec entrelacement de plusieurs disques durs virtuels de données Azure pour stocker une sauvegarde de fichiers SAP HANA complète. Il suffit alors de copier uniquement ces disques, qui contiennent la sauvegarde SAP HANA. Ils pourraient facilement être conservés dans un compte de stockage de sauvegarde HANA dédié, ou attachés à une &quot;machine virtuelle de gestion de sauvegarde&quot; dédiée pour traitement ultérieur.

![Tous les disques durs virtuels impliqués ont été copiés à l’aide de la commande PowerShell **start-azurestorageblobcopy**](media/sap-hana-backup-file-level/image031.png)

Une fois la sauvegarde du RAID logiciel local effectuée, tous les disques durs virtuels impliqués ont été copiés à l’aide de la commande PowerShell **start-azurestorageblobcopy** (voir [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Dans la mesure où cela affecte uniquement le système de fichiers dédié à la conservation des fichiers de sauvegarde, il n’existe aucun problème de cohérence des fichiers journaux ou des données SAP HANA sur le disque. L’avantage de cette commande est qu’elle fonctionne lorsque la machine virtuelle est en ligne. Pour être certain qu’aucun processus n’écrit dans le groupe d’entrelacements de sauvegarde, veillez à le démonter avant la copie d’objets blob, puis à le remonter. Vous pouvez également utiliser une méthode appropriée pour &quot;figer&quot; le système de fichiers. Par exemple, via xfs\_freeze pour le système de fichiers XFS.

![Cette capture d’écran affiche la liste des objets blob dans le conteneur de disques durs virtuels sur le portail Azure](media/sap-hana-backup-file-level/image032.png)

Cette capture d’écran affiche la liste des objets blob dans le conteneur de &quot;disques durs virtuels&quot; sur le portail Azure. La capture d’écran montre les cinq disques durs virtuels qui ont été connectés à la machine virtuelle du serveur SAP HANA pour servir de RAID logiciel pour la conservation des fichiers de sauvegarde SAP HANA. Elle montre également les cinq copies qui ont été effectuées à l’aide de la commande de copie d’objets blob.

![À des fins de test, les copies des disques RAID logiciels de sauvegarde SAP HANA étaient attachées à la machine virtuelle du serveur d’application](media/sap-hana-backup-file-level/image033.png)

À des fins de test, les copies des disques RAID logiciels de sauvegarde SAP HANA étaient attachées à la machine virtuelle du serveur d’application.

![La machine virtuelle du serveur d’application a été arrêtée pour attacher les copies du disque](media/sap-hana-backup-file-level/image034.png)

La machine virtuelle du serveur d’application a été arrêtée pour attacher les copies du disque. Après le démarrage de la machine virtuelle, les disques et le RAID ont été correctement détectés (montés via UUID). Seul le point de montage qui a été créé via le partitionneur YaST est manquant. Les copies du fichier de sauvegarde SAP HANA sont ensuite devenues visibles au niveau du système d’exploitation.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copier les fichiers de sauvegarde SAP HANA vers le partage NFS

Pour réduire l’impact potentiel sur le système SAP HANA en termes de performances ou d’espace disque, vous pouvez envisager de stocker les fichiers de sauvegarde SAP HANA sur un partage NFS. Techniquement, cela fonctionne, mais cela signifie que vous devez utiliser une deuxième machine virtuelle Azure en tant qu’hôte du partage NFS. La taille de la machine virtuelle ne doit pas être faible, en raison de la bande passante réseau de la machine virtuelle. Il serait donc judicieux d’arrêter cette &quot;machine virtuelle de sauvegarde&quot; et de l’activer uniquement pour réaliser la sauvegarde SAP HANA. L’écriture sur un partage NFS augmente la charge sur le réseau et a un impact sur le système SAP HANA, tandis que la simple gestion des fichiers de sauvegarde par la suite sur la &quot;machine virtuelle de sauvegarde&quot; n’aurait aucune influence sur le système SAP HANA.

![Un partage NFS à partir d’une autre machine virtuelle Azure a été monté sur la machine virtuelle du serveur SAP HANA](media/sap-hana-backup-file-level/image035.png)

Pour vérifier le cas d’utilisation NFS, un partage NFS à partir d’une autre machine virtuelle Azure a été monté sur la machine virtuelle du serveur SAP HANA. Aucun réglage NFS spécifique n’a été effectué.

![Il a fallu 1 heure et 46 minutes pour procéder à la sauvegarde directe](media/sap-hana-backup-file-level/image036.png)

Le partage NFS constituait un entrelacement rapide, comme celui sur le serveur SAP HANA. Néanmoins, il a fallu 1 heure et 46 minutes pour procéder à la sauvegarde directe sur le partage NFS au lieu de 10 minutes en cas d’écriture sur un entrelacement local.

![L’alternative n’était pas beaucoup plus rapide : 1 heure et 43 minutes](media/sap-hana-backup-file-level/image037.png)

La possibilité d’effectuer une sauvegarde vers un entrelacement local et de copier sur le partage NFS au niveau du système d’exploitation (une simple commande **cp -avr**) n’a pas été beaucoup plus rapide. Elle a pris 1 heure et 43 minutes.

Cela fonctionne donc, mais les performances n’étaient pas bonnes pour le test de sauvegarde de 230 Go. La situation serait encore pire pour plusieurs téraoctets.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Copier les fichiers de sauvegarde SAP HANA vers le service de fichiers Azure

Il est possible de monter un partage de fichiers Azure dans une machine virtuelle Linux Azure. L’article [Utilisation du stockage de fichiers Azure avec Linux](../../../storage/files/storage-how-to-use-files-linux.md) fournit des détails sur la manière de procéder. N’oubliez pas qu’il existe actuellement une limite de 5 To pour un partage de fichiers Azure, et une limite de taille de fichier de 1 To. Pour plus d’informations sur les limites de stockage, voir [Objectifs de performance et évolutivité du stockage Azure](../../../storage/common/storage-scalability-targets.md).

Des tests ont toutefois démontré que la sauvegarde SAP HANA ne fonctionne pas directement avec ce type de montage CIFS. La [note SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529) indique également que CIFS n’est pas recommandé.

![Cette illustration montre une erreur dans la boîte de dialogue de sauvegarde dans SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Cette illustration montre une erreur dans la boîte de dialogue de sauvegarde dans SAP HANA Studio lorsque vous tentez d’effectuer une sauvegarde directement sur un partage de fichiers Azure monté sur CIFS. Par conséquent, vous devez d’abord effectuer une sauvegarde SAP HANA standard sur un système de fichiers de machine virtuelle, puis copier les fichiers de sauvegarde vers le service de fichiers Azure.

![Cette illustration montre qu’il a fallu environ 929 secondes pour copier 19 fichiers de sauvegarde SAP HANA](media/sap-hana-backup-file-level/image039.png)

Cette illustration montre qu’il a fallu environ 929 secondes pour copier vers le partage de fichiers Azure 19 fichiers de sauvegarde SAP HANA représentant une taille totale d’environ 230 Go.

![La structure du répertoire source sur la machine virtuelle SAP HANA a été copiée dans le partage de fichiers Azure](media/sap-hana-backup-file-level/image040.png)

Sur cette capture d’écran, vous pouvez voir que la structure du répertoire source sur la machine virtuelle SAP HANA a été copiée dans le partage de fichiers Azure : un répertoire (hana\_backup\_fsl\_15gb) et 19 fichiers de sauvegarde individuels.

Le stockage des fichiers de sauvegarde SAP HANA sur les fichiers Azure pourrait à l’avenir constituer une option intéressante, à partir du moment où les sauvegardes de fichiers SAP HANA le prendront en charge directement. Ou lorsqu’il deviendra possible de monter des fichiers Azure via NFS et que la limite maximale sera nettement supérieure à 5 To.

## <a name="next-steps"></a>Étapes suivantes
* L’article [Guide de sauvegarde pour SAP HANA sur les machines virtuelles Azure](sap-hana-backup-guide.md) fournit une vue d’ensemble et des informations sur la mise en route.
* L’article [Sauvegarde SAP HANA à partir de captures instantanées de stockage](sap-hana-backup-storage-snapshots.md) décrit l’option de sauvegarde à partir d’instantanés de stockage.
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur Azure (grandes instances), consultez [Haute disponibilité et récupération d’urgence de SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md).
