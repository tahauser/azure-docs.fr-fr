---
title: Migration de machines virtuelles vers le stockage Azure Premium | Microsoft Docs
description: "Migrez vos machines virtuelles existantes vers le stockage Azure Premium. Premium Storage offre une prise en charge très performante et à faible latence des disques pour les charges de travail utilisant beaucoup d'E/S exécutées sur les machines virtuelles Azure."
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: 36ff73d36c752fb342dcfff2360b4f6f7013740e
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migration vers le stockage Azure Premium (disques non gérés)

> [!NOTE]
> Cet article explique comment migrer une machine virtuelle qui utilise des disques standard non gérés vers une machine virtuelle qui utilise des disques premium non gérés. Nous vous recommandons d’utiliser Azure Managed Disks pour les nouvelles machines virtuelles et de convertir vos anciens disques non gérés en disques gérés. Azure Managed Disks gère les comptes de stockage sous-jacents à votre place. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).
>

Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Vous pouvez migrer les disques de machine virtuelle de votre application dans Azure Premium Storage pour tirer parti de la vitesse et des performances de ces disques.

L’objectif de ce guide est d’aider les nouveaux utilisateurs d’Azure Premium Storage à mieux se préparer pour effectuer une transition en douceur de leur système actuel vers Premium Storage. Le guide traite des trois composants clés inclus dans ce processus :

* [Planification de la migration vers Premium Storage](#plan-the-migration-to-premium-storage)
* [Préparation et copie de disques durs virtuels (VHD) vers le stockage Premium](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Création d’une machine virtuelle Azure utilisant Premium Storage](#create-azure-virtual-machine-using-premium-storage)

Vous pouvez migrer des machines virtuelles d’autres plateformes vers Azure Premium Storage ou migrer des machines virtuelles Azure existantes du stockage Standard vers le stockage Premium. Ce guide décrit les étapes pour les deux scénarios. Suivez les étapes spécifiées dans la section appropriée selon votre scénario.

> [!NOTE]
> Vous pouvez consulter un aperçu des fonctionnalités et de la tarification dans [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../../virtual-machines/windows/premium-storage.md). Nous vous recommandons de migrer les disques de machine virtuelle nécessitant un nombre élevé d’IOPS dans Azure Premium Storage pour que votre application bénéficie de performances optimales. Si votre disque ne nécessite pas un nombre élevé d'IOPS, vous pouvez limiter les coûts en le conservant dans le stockage Standard qui stocke les données de disque de machine virtuelle sur des disques durs et non des disques SSD.
>

L’exécution du processus de migration dans son intégralité peut nécessiter des actions supplémentaires précédant et suivant les étapes fournies dans ce guide. Par exemple, la configuration de réseaux virtuels ou de points de terminaison ou l’intégration de modifications de code dans l’application elle-même, ce qui peut nécessiter un certain temps d’indisponibilité dans votre application. Ces actions sont propres à chaque application, et vous devez les exécuter en plus des étapes indiquées dans ce guide pour effectuer la transition complète vers Premium Storage de manière aussi transparente que possible.

## <a name="plan-the-migration-to-premium-storage"></a>Planification de la migration vers Premium Storage
Cette section vous permet de vous assurer que vous êtes prêt à suivre les étapes de migration de cet article et vous permet de prendre la bonne décision sur les types de machines virtuelles et de disques.

### <a name="prerequisites"></a>Prérequis
* Vous aurez besoin d’un abonnement Azure. Si vous n’en avez pas, vous pouvez souscrire un abonnement pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/) d’un mois ou visiter [Tarification Azure](https://azure.microsoft.com/pricing/) pour plus d’options.
* Pour exécuter les applets de commande PowerShell, vous avez besoin du module Microsoft Azure PowerShell. Consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) pour des instructions sur l’installation et le point d’installation.
* Lorsque vous prévoyez d’utiliser des machines virtuelles Azure exécutées sur Premium Storage, vous devez utiliser les machines virtuelles compatibles Premium Storage. Vous pouvez utiliser des disques de Stockage Standard et Premium avec les machines virtuelles compatibles avec Premium Storage. Les disques de stockage Premium seront bientôt disponibles avec plusieurs types de machines virtuelles. Pour plus d’informations sur les tailles et les types de disque de machine virtuelle Azure disponibles, consultez [Tailles des machines virtuelles](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et [Tailles des services cloud](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considérations
Une machine virtuelle Azure prend en charge l’association de plusieurs disques Premium Storage afin que vos applications puissent avoir jusqu'à 256 To de stockage par machine virtuelle. Avec Premium Storage, vos applications peuvent atteindre jusqu'à 80 000 IOPS (opérations d'E/S par seconde) par machine virtuelle et un débit de disque de 2 000 Mo par seconde, avec une latence extrêmement faible pour les opérations de lecture. Vous disposez de diverses options de machines virtuelles et disques. Cette section est là pour vous aider à trouver l’option qui correspond le mieux à votre charge de travail.

#### <a name="vm-sizes"></a>Tailles de machine virtuelle
Les spécifications des tailles des machines virtuelles Azure sont répertoriées dans la section [Tailles des machines virtuelles](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Passez en revue les caractéristiques de performances des machines virtuelles fonctionnant avec Premium Storage et choisissez la taille de machine virtuelle la mieux adaptée à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

#### <a name="disk-sizes"></a>Tailles du disque
Il existe cinq types de disque qui peuvent être utilisés avec votre machine virtuelle et chacun d’eux présentant des limites d’E/S par seconde et de débits spécifiques. Prenez en compte ces limites lors de la sélection du type de disque pour votre machine virtuelle en fonction des besoins de votre application en termes de capacité, de performances, d’extensibilité et de charges maximales.

| Type de disque Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Taille du disque           | 128 Go| 512 Go| 1024 Go (1 To) | 2 048 Go (2 To) | 4 095 Go (4 To) | 
| IOPS par disque       | 500   | 2 300  | 5 000           | 7500           | 7500           | 
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde | 250 Mo par seconde | 250 Mo par seconde |

En fonction de votre charge de travail, déterminez si les disques de données supplémentaires sont nécessaires pour votre machine virtuelle. Vous pouvez joindre plusieurs disques de données persistantes à votre machine virtuelle. Si nécessaire, vous pouvez répartir les données sur les disques pour augmenter la capacité et les performances du volume. (Découvrez ce qu’est l’entrelacement de disques [ici](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Si vous équilibrez les disques de données Stockage Premium à l’aide des [espaces de stockage][4], vous devez les configurer avec une colonne pour chaque disque utilisé. Dans le cas contraire, les performances globales du volume agrégé par bandes peuvent être limitées, en raison d'une distribution inégale du trafic sur les disques. Pour les machines virtuelles Linux, vous pouvez utiliser l’utilitaire *mdadm* pour obtenir le même résultat. Consultez l’article [Configuration d’un RAID logiciel sur Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations.

#### <a name="storage-account-scalability-targets"></a>Objectifs d’évolutivité de compte de stockage
Les comptes de stockage Premium ont les objectifs d’évolutivité suivants en plus des [Objectifs d’évolutivité et de performances de stockage Azure](storage-scalability-targets.md). Si les besoins de votre application dépassent les objectifs d’évolutivité d’un compte de stockage unique, générez votre application pour qu’elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes.

| Capacité totale des comptes | Bande passante totale pour un compte de stockage localement redondant |
|:--- |:--- |
| Capacité du disque : 35 To<br />Capacité d’instantané : 10 To |Jusqu'à 50 Go par seconde pour les données entrantes/sortantes |

Pour plus d’informations sur les spécifications de Premium Storage, consultez la page [Objectifs d’évolutivité et de performances lors de l’utilisation de Premium Storage](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Stratégie de mise en cache du disque
Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application. Les paramètres du cache pour les disques de données existants peuvent être mis à jour à l’aide du [portail Azure Portal](https://portal.azure.com) ou du paramètre *-HostCaching* de l’applet de commande *Set-AzureDataDisk*.

#### <a name="location"></a>Lieu
Choisissez un emplacement où le stockage Azure Premium est disponible. Pour obtenir des informations à jour sur les emplacements disponibles, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services) . Les machines virtuelles situées dans la même région que le compte de stockage qui stocke les disques de la machine virtuelle offrent des performances bien meilleures que si elles se trouvent dans des régions distinctes.

#### <a name="other-azure-vm-configuration-settings"></a>Autres paramètres de configuration de machine virtuelle Azure
Lorsque vous créez une machine virtuelle Azure, vous devez en configurer certains paramètres. N’oubliez pas : certains paramètres sont fixes pour la durée de vie de la machine virtuelle, tandis que d’autres peuvent être modifiés ou ajoutés ultérieurement. Passez en revue les paramètres de configuration des machines virtuelles Azure et assurez-vous qu’ils sont correctement configurés pour répondre aux besoins de votre charge de travail.

### <a name="optimization"></a>Optimisation
[Azure Premium Storage : conception sous le signe de la haute performance](../../virtual-machines/windows/premium-storage-performance.md) fournit des instructions pour la création d’applications hautes performances avec Azure Premium Storage. Vous pouvez suivre les instructions parallèlement aux bonnes pratiques de performances applicables aux technologies utilisées par votre application.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Préparation et copie de disques durs virtuels (VHD) vers le stockage Premium
La section suivante fournit des instructions pour la préparation des disques durs virtuels à partir de votre machine virtuelle et la copie des disques durs virtuels vers le stockage Azure.

* [Scénario 1 : « Migration des machines virtuelles Azure existantes vers Azure Premium Storage. »](#scenario1)
* [Scénario 2 : « Migration de machines virtuelles depuis d’autres plateformes vers Azure Premium Storage ».](#scenario2)

### <a name="prerequisites"></a>Prérequis
Pour préparer les disques durs virtuels pour la migration, vous devez :

* Un abonnement Azure, un compte de stockage et un conteneur dans ce compte de stockage sur lequel vous pouvez copier votre disque dur virtuel. Notez que le compte de stockage de destination peut être un compte de stockage Standard ou Premium selon vos besoins.
* Un outil pour généraliser le disque dur virtuel si vous envisagez de créer plusieurs instances de machine virtuelle à partir de celui-ci. Par exemple, sysprep pour Windows ou virt-sysprep pour Ubuntu.
* Un outil pour télécharger le fichier de disque dur virtuel sur le compte de stockage. Consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) ou utilisez [l’explorateur Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Ce guide décrit la copie de votre disque dur virtuel à l’aide de l’outil AzCopy.

> [!NOTE]
> Si vous choisissez l’option de copie asynchrone pour AzCopy, pour des performances optimales, copiez votre disque dur virtuel en exécutant l’un de ces outils à partir d’une machine virtuelle Azure se trouvant dans la même région que le compte de stockage de destination. Si vous copiez un disque dur virtuel à partir d’une machine virtuelle Azure se trouvant dans une autre région, les performances risquent d’être ralenties.
>
> Pour copier une grande quantité de données avec une bande passante limitée, envisagez d’utiliser le [service Azure Import/Export pour transférer les données vers Blob Storage](../storage-import-export-service.md). Cela permet de transférer les données par expédition des disques durs à un centre de données Azure. Vous pouvez utiliser le service Azure Import/Export pour copier les données vers un compte de stockage Standard uniquement. Une fois les données dans votre compte de stockage Standard, utilisez [l’API copie d’objet blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy pour transférer les données vers votre compte de stockage Premium.
>
> Notez que Microsoft Azure prend uniquement en charge les fichiers de disque dur virtuel de taille fixe. Les fichiers VHDX ou les disques durs virtuels dynamiques ne sont pas pris en charge. Si vous avez un disque dur virtuel dynamique, vous pouvez le convertir à taille fixe à l’aide de l’applet de commande [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) .
>
>

### <a name="scenario1"></a>Scénario 1 : « Migration des machines virtuelles Azure existantes vers Azure Premium Storage. »
Si vous faites migrer des machines virtuelles Azure existantes, arrêtez la machine virtuelle, préparez les disques durs virtuels pour le type de disque dur virtuel souhaité, puis copiez le disque dur virtuel avec AzCopy ou PowerShell.

La machine virtuelle doit être complètement arrêtée pour migrer un état propre. L’interruption de service se poursuivra jusqu’à la fin de la migration.

#### <a name="step-1-prepare-vhds-for-migration"></a>Étape 1. Préparer des disques durs virtuels pour la migration
Si vous faites migrer des machines virtuelles Azure existantes vers le stockage Premium, votre disque dur Virtuel peut être :

* Une image du système d'exploitation généralisée
* Un disque de système d’exploitation unique
* Un disque de données

Nous vous présentons ci-dessous 3 scénarios pour la préparation de vos disques durs virtuels.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilisez un disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle
Si vous téléchargez un disque dur virtuel qui permet de créer plusieurs instances de machine virtuelle Azure génériques, vous devez tout d’abord généraliser un disque dur virtuel à l’aide d’un utilitaire sysprep. Cette procédure s’applique à un disque dur virtuel local ou dans le cloud. Sysprep supprime des informations spécifiques sur une machine à partir du disque dur virtuel.

> [!IMPORTANT]
> Réalisez un instantané ou une sauvegarde de votre machine virtuelle avant la généralisation. L’exécution de sysprep arrêtera et désallouera l’instance de machine virtuelle. Suivez les étapes ci-dessous pour exécuter sysprep sur un disque dur virtuel de système d’exploitation Windows. Notez que vous devez arrêter la machine virtuelle pour pouvoir exécuter la commande Sysprep. Pour plus d’informations sur Sysprep, consultez [Présentation de Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou le [Manuel de référence technique Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Entrez la commande suivante pour ouvrir Sypsrep :

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Dans l’outil de préparation du système, choisissez Entrer en mode OOBE (Out-of-Box Experience), activez la case à cocher Généraliser, sélectionnez **Arrêter**, puis cliquez sur **OK**, comme illustré dans l’image ci-dessous. Le système d’exploitation sera généralisé et le système arrêté par sysprep.

    ![][1]

Pour une machine virtuelle Ubuntu, utilisez virt-sysprep pour obtenir le même résultat. Pour plus d’informations, consultez la page [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) . Consultez également les informations relatives à certains des [logiciels de configuration du serveur Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) open source pour d’autres systèmes d’exploitation Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilisation d’un disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle
Si une application exécutée sur la machine virtuelle requiert les données spécifiques de la machine, ne généralisez pas le disque dur virtuel. Un disque dur virtuel non généralisé peut servir à créer une instance de machine virtuelle Azure unique. Par exemple, si vous avez un contrôleur de domaine sur votre disque dur virtuel, l’exécution de sysprep le rend inefficace comme contrôleur de domaine. Passez en revue les applications s’exécutant sur votre machine virtuelle et l’impact de sysprep s’exécutant sur ces dernières avant la généralisation du disque dur virtuel.

##### <a name="register-data-disk-vhd"></a>Enregistrement du disque dur virtuel de données
Si vous avez des disques de données dans Azure à migrer, vous devez vous assurer que les machines virtuelles qui utilisent ces disques de données sont arrêtées.

Suivez les étapes décrites ci-dessous pour copier un disque dur virtuel vers Azure Premium Storage et l’enregistrer en tant que disque de données configuré.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Création de la destination de votre disque dur virtuel
Créez un compte de stockage pour gérer vos disques durs virtuels. Prenez en compte les points suivants lors de la planification de l’emplacement où stocker vos disques durs virtuels :

* Le compte de stockage Premium cible.
* L’emplacement du compte de stockage doit être identique dans les machines virtuelles Azure compatibles Premium Storage que vous allez créer lors de l’étape finale. Vous pouvez copier vers un nouveau compte de stockage, ou envisager d’utiliser le même compte de stockage selon vos besoins.
* Copiez et enregistrez la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Pour les disques de données, vous pouvez choisir d’en conserver certains dans un compte de stockage Standard (par exemple, les disques qui ont un stockage de refroidissement), mais nous vous conseillons fortement de déplacer toutes vos données de charge de travail de production pour utiliser le compte de stockage Premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Étape 3. Copie du disque dur virtuel avec AzCopy ou PowerShell
Vous devez rechercher le chemin d’accès de votre conteneur et la clé du compte de stockage pour traiter une de ces deux options. Vous trouverez le chemin d’accès au conteneur et le compte de stockage dans le **Portail Azure** > **Stockage**. L’URL du conteneur sera au format « https://moncompte.blob.core.windows.net/monconteneur/ ».

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Option 1 : Copie d’un disque dur virtuel avec AzCopy (copie asynchrone)
À l’aide d’AzCopy, vous pouvez facilement télécharger le disque dur virtuel sur Internet. Selon la taille des disques durs virtuels, cela peut prendre du temps. N’oubliez pas de vérifier les limites d’entrées/sorties de compte de stockage lors de l’utilisation de cette option. Pour plus d’informations, consultez [Objectifs de performance et d’évolutivité d’Azure Storage](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à partir d’ici : [version la plus récente d’AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier où AzCopy est installé.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel à partir de « Source » vers « Destination ».

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemple :

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Les paramètres utilisés dans la commande AzCopy sont décrits ci-dessous :

   * **/Source: *&lt;&gt;source :*** emplacement du dossier ou URL du conteneur de stockage qui contient le disque dur virtuel.
   * **/SourceKey : *&lt;source-account-key&gt; :*** clé de compte de stockage du compte de stockage source.
   * **/Dest : *&lt;destination&gt; :*** URL du conteneur de stockage où copier le disque dur virtuel.
   * **/DestKey : *&lt;dest-account-key&gt; :*** clé de compte de stockage du compte de stockage de destination.
   * **/Pattern : *&lt;file-name&gt; :*** spécifie le nom du fichier du disque dur virtuel à copier.

Pour plus d’informations sur l’utilisation d’AzCopy, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Option 2 : Copie d’un disque dur virtuel avec PowerShell (copie synchronisée)
Vous pouvez également copier le fichier de disque dur virtuel à l’aide de l’applet de commande Start-AzureStorageBlobCopy. Utilisez la commande suivante sur Azure PowerShell pour copier le disque dur virtuel. Remplacez les valeurs entre <> par les valeurs correspondantes de votre compte de stockage source et de destination. Pour utiliser cette commande, vous devez disposer d’un conteneur appelé vhds (disques durs virtuels) dans votre compte de stockage de destination. Si le conteneur n’existe pas, créez-le avant d’exécuter la commande.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exemple :

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Scénario 2 : « Migration de machines virtuelles depuis d’autres plateformes vers Azure Premium Storage ».
Si vous migrez un disque dur virtuel d’un stockage cloud non Azure vers Azure, vous devez d’abord exporter le disque dur virtuel vers un répertoire local. Ayez à disposition le chemin d’accès source complet du répertoire local sur lequel le disque dur virtuel est stocké, et utilisez AzCopy pour le télécharger vers le stockage Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Étape 1. Exportation d’un disque dur virtuel vers un répertoire local
##### <a name="copy-a-vhd-from-aws"></a>Copie d’un disque dur virtuel depuis AWS
1. Si vous utilisez AWS, exportez l’instance EC2 vers un disque dur virtuel dans un compartiment S3 Amazon. Suivez les étapes décrites dans la section Exportation des instances Amazon EC2 de la documentation Amazon pour installer l’outil d’interface de ligne de commande (CLI) Amazon EC2 et exécutez la commande create-instance-export-task pour exporter l’instance EC2 vers un fichier de disque dur virtuel. Veillez à utiliser **VHD** pour la variable DISK&#95;IMAGE&#95;FORMAT lors de l’exécution de la commande **create-instance-export-task**. Le fichier de disque dur virtuel exporté est enregistré dans le compartiment Amazon S3 que vous désignez au cours de ce processus.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Téléchargez le fichier de disque dur virtuel depuis le compartiment S3. Sélectionnez le fichier de disque dur virtuel, puis **Actions** > **Télécharger**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copie d’un disque dur virtuel à partir d’autres cloud hors Azure
Si vous migrez un disque dur virtuel d’un stockage cloud non Azure vers Azure, vous devez d’abord exporter le disque dur virtuel vers un répertoire local. Copiez le chemin source complet du répertoire local où le disque dur virtuel est stocké.

##### <a name="copy-a-vhd-from-on-premises"></a>Copie d’un disque dur virtuel en local
Si vous migrez un disque dur virtuel à partir d’un environnement local, vous avez besoin du chemin source complet où le disque dur virtuel est stocké. Le chemin d’accès source peut être un partage de fichiers ou un emplacement de serveur.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Étape 2. Création de la destination de votre disque dur virtuel
Créez un compte de stockage pour gérer vos disques durs virtuels. Prenez en compte les points suivants lors de la planification de l’emplacement où stocker vos disques durs virtuels :

* Le compte de stockage cible peut être Standard ou Premium selon les besoins de votre application.
* La région du compte de stockage doit être identique dans les machines virtuelles Azure de série DS que vous allez créer lors de l’étape finale. Vous pouvez copier vers un nouveau compte de stockage, ou envisager d’utiliser le même compte de stockage selon vos besoins.
* Copiez et enregistrez la clé de compte de stockage du compte de stockage de destination pour l’étape suivante.

Nous vous recommandons fortement de déplacer toutes les données de charge de production pour utiliser le stockage premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Étape 3. Téléchargement du disque dur virtuel vers le stockage Azure
Maintenant que vous avez votre disque dur virtuel dans le répertoire local, vous pouvez utiliser AzCopy ou AzurePowerShell pour télécharger le fichier .vhd vers le stockage Azure. Les deux options sont fournies ici :

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Option 1 : Utilisation d’Add-AzureVhd d’Azure PowerShell pour télécharger le fichier .vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Un exemple <Uri>peut être ***« https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd »***. Un exemple <FileInfo>peut être ***« C:\path\to\upload.vhd »***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Option 2 : Utilisation d’AzCopy pour télécharger le fichier .vhd
À l’aide d’AzCopy, vous pouvez facilement télécharger le disque dur virtuel sur Internet. Selon la taille des disques durs virtuels, cela peut prendre du temps. N’oubliez pas de vérifier les limites d’entrées/sorties de compte de stockage lors de l’utilisation de cette option. Pour plus d’informations, consultez [Objectifs de performance et d’évolutivité d’Azure Storage](storage-scalability-targets.md) .

1. Téléchargez et installez AzCopy à partir d’ici : [version la plus récente d’AzCopy](http://aka.ms/downloadazcopy)
2. Ouvrez Azure PowerShell et accédez au dossier où AzCopy est installé.
3. Utilisez la commande suivante pour copier le fichier de disque dur virtuel à partir de « Source » vers « Destination ».

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemple :

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Les paramètres utilisés dans la commande AzCopy sont décrits ci-dessous :

   * **/Source: *&lt;&gt;source :*** emplacement du dossier ou URL du conteneur de stockage qui contient le disque dur virtuel.
   * **/SourceKey : *&lt;source-account-key&gt; :*** clé de compte de stockage du compte de stockage source.
   * **/Dest : *&lt;destination&gt; :*** URL du conteneur de stockage où copier le disque dur virtuel.
   * **/DestKey : *&lt;dest-account-key&gt; :*** clé de compte de stockage du compte de stockage de destination.
   * **/BlobType: page :** spécifie si la destination est un objet blob de pages.
   * **/Pattern : *&lt;file-name&gt; :*** spécifie le nom du fichier du disque dur virtuel à copier.

Pour plus d’informations sur l’utilisation d’AzCopy, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Autres options de téléchargement d’un disque dur virtuel
Vous pouvez également télécharger un disque dur virtuel sur votre compte de stockage en utilisant l’un des moyens suivants :

* [API de copie d’un objet blob de stockage Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Téléchargement d’objets blob dans Storage Explorer](https://azurestorageexplorer.codeplex.com/)
* [Référence sur l’API REST du service Import/Export Storage](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Nous recommandons l’utilisation de du service d’importation/exportation si l’estimation du temps de téléchargement est de plus de 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de la taille des données et de l’unité de transfert.
>
> Le service Import/Export permet de copier sur un compte de stockage Standard. Vous devez effectuer une copie d’un compte de stockage Standard vers un compte de stockage Premium à l’aide d’un outil tel qu’AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Créer des machines virtuelles Azure à l’aide du stockage Premium
Une fois le disque dur virtuel téléchargé ou copié vers le compte de stockage souhaité, suivez les instructions de cette section pour inscrire le disque dur virtuel en tant qu’image de système d’exploitation ou disque de système d’exploitation en fonction de votre scénario et créez ensuite une instance de machine virtuelle à partir de celui-ci. Le disque dur virtuel de disque de données peut être joint à la machine virtuelle qui a été créée.
Un exemple de script de migration est fourni à la fin de cette section. Ce script simple ne correspond pas à tous les scénarios. Vous devrez peut-être le mettre à jour pour qu’il corresponde à votre scénario spécifique. Pour voir si ce script s’applique à votre scénario, consultez la section [Un exemple de script de migration](#a-sample-migration-script) ci-dessous.

### <a name="checklist"></a>Liste de contrôle
1. Patientez jusqu'à ce que la copie de tous disques durs virtuels soit terminée.
2. Vérifiez que Premium Storage est disponible dans la région vers laquelle vous effectuez la migration.
3. Choisissez la nouvelle série de machines virtuelles que vous allez utiliser. La compatibilité avec Premium Storage est indispensable et la taille dépend de la disponibilité dans la région et de vos besoins.
4. Choisissez la taille exacte de machine virtuelle que vous allez utiliser. La taille de machine virtuelle doit être suffisante pour prendre en charge le nombre de disques de données dont vous disposez. Par exemple, Si vous disposez de 4 disques de données, la machine virtuelle doit disposer d’au moins 2 cœurs. Prenez également en considération les besoins en puissance, mémoire et bande passante réseau.
5. Créez un compte Premium Storage dans la région cible. C’est le compte que vous utiliserez pour la nouvelle machine virtuelle.
6. Gardez à portée de main les informations détaillées sur les machines virtuelles, notamment la liste des disques et des blobs de disques durs virtuels correspondants.

Préparez votre application pour les interruptions de service. Pour effectuer une migration sans erreur, vous devez arrêter tous les processus en cours d’exécution dans le système actuel. Ce n’est qu’à cette condition que le système présentera un état cohérent, permettant sa migration vers la nouvelle plateforme. La durée de l’interruption de service dépend de la quantité de données dans les disques à migrer.

> [!NOTE]
> Si vous créez une machine virtuelle Azure Resource Manager à partir d’un disque dur virtuel spécialisé, reportez-vous à [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pour le déploiement d’une machine virtuelle Resource Manager à l’aide du disque existant.
>
>

### <a name="register-your-vhd"></a>Enregistrez votre disque dur virtuel.
Pour créer une machine virtuelle à partir du disque dur virtuel de système d’exploitation ou joindre un disque de données à une nouvelle machine virtuelle, vous devez tout d’abord les inscrire. Suivez les étapes ci-dessous en fonction de votre scénario de disque dur virtuel.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle Azure
Une fois le disque dur virtuel d’image du système d’exploitation généralisé téléchargé vers le compte de stockage, inscrivez-le comme une **Image de machine virtuelle Azure** afin de pouvoir créer une ou plusieurs instances de machine virtuelle à partir de celui-ci. Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme image de système d’exploitation de la machine virtuelle Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copiez et enregistrez le nom de cette nouvelle image de machine virtuelle Azure. Dans l’exemple ci-dessus, il s’agit de *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle Azure
Une fois le disque dur virtuel de système d’exploitation unique téléchargé vers le compte de stockage, inscrivez-le comme **disque de système d’exploitation Azure** afin de pouvoir créer une instance de machine virtuelle à partir de celui-ci. Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme disque de système d’exploitation Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copiez et enregistrez le nom de ce nouveau disque de système d’exploitation Azure. Dans l’exemple ci-dessus, il s’agit de *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>Disque dur virtuel de disque de données à joindre aux nouvelles instances de machine virtuelle Azure
Une fois le disque dur virtuel de disque de données téléchargé vers le compte de stockage, inscrivez-le comme disque de données Azure afin qu’il puisse être joint à votre nouvelle instance de machine virtuelle Azure de série DS, DSv2 ou GS.

Utilisez les applets de commande PowerShell suivantes pour inscrire votre disque dur virtuel comme disque de données Azure. Fournissez l’URL de conteneur complet où le disque dur virtuel a été copié.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copiez et enregistrez le nom de ce nouveau disque de données Azure. Dans l’exemple ci-dessus, il s’agit de *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Création d’un compte compatible Premium Storage
Une fois l’image du système d’exploitation ou le disque du système d’exploitation inscrit, créez une nouvelle machine virtuelle série DS, DSv2 ou GS. Vous utiliserez l’image du système d’exploitation ou le nom de disque de système d’exploitation que vous avez inscrit. Sélectionnez le type de machine virtuelle à partir du niveau de stockage Premium. Dans l’exemple ci-dessous, nous utilisons la taille de machine virtuelle *Standard_DS2*.

> [!NOTE]
> Mettez à jour la taille du disque pour vous assurer qu’il correspond à votre capacité, à l’exigence de performance et aux tailles de disque Azure disponibles.
>
>

Suivez les applets de commande PowerShell étape par étape ci-dessous pour créer la machine virtuelle. Tout d’abord, définissez les paramètres communs :

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Commencez par créer un service cloud dans lequel vous hébergerez vos nouvelles machines virtuelles.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Ensuite, selon votre scénario, créez l’instance de machine virtuelle Azure à partir de l’image de système d’exploitation ou du disque de système d’exploitation que vous avez inscrit.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Disque dur virtuel de système d’exploitation généralisé pour créer plusieurs instances de machine virtuelle Azure
Créez la ou les nouvelles instances de machine virtuelle Azure de série DS à l’aide de l’ **Image de système d’exploitation Azure** que vous avez inscrite. Spécifiez ce nom d’image de système d’exploitation dans la configuration de la machine virtuelle lors de la création de nouvelles machines virtuelles comme indiqué ci-dessous.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>Disque dur virtuel de système d’exploitation unique pour créer une seule instance de machine virtuelle Azure
Créez une nouvelle instance de machine virtuelle Azure de série DS à l’aide du **disque de système d’exploitation Azure** que vous avez inscrit. Spécifiez ce nom de disque du système d’exploitation dans la configuration de la machine virtuelle lors de la création de la nouvelle machine virtuelle comme indiqué ci-dessous.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Spécifiez d’autres informations de machine virtuelle Azure, comme un service cloud, une région, un compte de stockage, un groupe à haute disponibilité et une stratégie de mise en cache. Notez que l’instance de machine virtuelle doit se trouver avec le système d’exploitation ou les disques de données associés ; le service cloud, la région et le compte de stockage sélectionnés doivent donc tous se trouver au même emplacement que les disques durs virtuels sous-jacents de ces disques.

### <a name="attach-data-disk"></a>Attacher un disque de données
Enfin, si vous avez inscrit les disques durs virtuels des disques de données, joignez-les à la nouvelle machine virtuelle Azure compatible Premium Storage.

Utilisez l’applet de commande PowerShell suivante pour joindre un disque de données à la nouvelle machine virtuelle et spécifiez la stratégie de mise en cache. Dans l’exemple ci-dessous, la stratégie de mise en cache est définie sur *Lecture seule*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Des étapes supplémentaires susceptibles de ne pas être exposées dans ce guide peuvent être nécessaires pour prendre en charge vos applications.
>
>

### <a name="checking-and-plan-backup"></a>Vérification et planification de sauvegarde
Une fois la nouvelle machine virtuelle opérationnelle, accédez-y en utilisant les mêmes ID de connexion et mot de passe que sur la machine virtuelle d’origine et vérifiez que tout fonctionne comme prévu. Tous les paramètres, y compris les volumes agrégés par bandes doivent être présents dans la nouvelle machine virtuelle.

La dernière étape consiste à planifier la sauvegarde et la maintenance de la nouvelle machine virtuelle en fonction des besoins de l’application.

### <a name="a-sample-migration-script"></a>Un exemple de script de migration
Si vous avez plusieurs machines virtuelles à migrer, il peut s’avérer utile d’automatiser ces tâches via des scripts PowerShell. Voici un exemple de script qui automatise la migration d’une machine virtuelle. Notez que le script ci-dessous n’est qu’un exemple, reposant sur des hypothèses concernant la configuration des disques de la machine virtuelle actuelle. Vous devrez peut-être le mettre à jour pour qu’il corresponde à votre scénario spécifique.

Nous supposons que :

* Vous créez des machines virtuelles Azure classiques.
* Vos disques de système d’exploitation source et disques de données source sont sur le même compte de stockage et le même conteneur. Si vos disques de système d’exploitation et disques de données ne sont pas au même emplacement, vous pouvez utiliser AzCopy ou Azure PowerShell pour copier les disques durs virtuels sur des conteneurs et comptes de stockage. Reportez-vous à l’étape précédente : [Copie du disque dur virtuel avec AzCopy ou PowerShell](#copy-vhd-with-azcopy-or-powershell). La modification de ce script pour répondre à votre scénario est une autre possibilité, mais nous recommandons l’utilisation d’AzCopy ou de PowerShell, car cela est plus facile et plus rapide.

Le script d’automatisation est fourni ci-dessous. Remplacez le texte par vos informations et mettez à jour le script pour correspondre à votre scénario spécifique.

> [!NOTE]
> L’utilisation du script existant ne conserve pas la configuration réseau de votre machine virtuelle source. Vous devrez reconfigurer les paramètres réseau sur vos machines virtuelles migrées.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Optimisation
La configuration de votre machine virtuelle actuelle peut être personnalisée spécifiquement pour fonctionner correctement avec des disques Standard. Par exemple, vous pouvez augmenter les performances en utilisant de nombreux disques dans un volume agrégé par bandes. Par exemple, au lieu d’utiliser 4 disques séparément sur Premium Storage, vous pourrez optimiser le coût en n’utilisant qu’un seul disque. Des optimisations comme celles-ci doivent être étudiées au cas par cas et requièrent l’exécution d’étapes personnalisées après la migration. Notez également que ce processus est susceptible de ne pas bien fonctionner pour les bases de données et les applications qui dépendent de la disposition du disque définie dans la configuration.

##### <a name="preparation"></a>Préparation
1. Terminez la migration simple comme décrit dans la section précédente. Les optimisations seront effectuées sur la nouvelle machine virtuelle après la migration.
2. Définissez les tailles requises pour les nouveaux disques pour une configuration optimisée.
3. Déterminez les spécifications du mappage des volumes/disques actuels aux nouveaux disques.

##### <a name="execution-steps"></a>Étapes d'exécution
1. Créez les nouveaux disques avec les tailles appropriées sur la machine virtuelle Premium Storage.
2. Connectez-vous à la machine virtuelle et copiez les données du volume actuel vers le nouveau disque mappé à ce volume. Utilisez cette même procédure pour tous les volumes actuels devant être mappés à un nouveau disque.
3. Ensuite, modifiez les paramètres d’application pour basculer vers les nouveaux disques et détachez les anciens volumes.

Pour optimiser l’application pour de meilleures performances de disque, reportez-vous à [Optimisation des performances applicatives](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrations des applications
Les bases de données et autres applications complexes peuvent nécessiter des étapes spéciales, telles que définies par le fournisseur de l’application pour la migration. Reportez-vous à la documentation correspondante de l’application. Par exemple, la migration des bases de données se fait généralement via des étapes de sauvegarde et de restauration.

## <a name="next-steps"></a>Étapes suivantes
Consultez les ressources suivantes pour des scénarios spécifiques de migration des machines virtuelles :

* [Migrer des machines virtuelles Azure entre les comptes de stockage](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure.](../../virtual-machines/windows/upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Création et chargement d’un disque VHD Linux dans Azure](../../virtual-machines/linux/create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Migration de machines virtuelles à partir d’Amazon AWS vers Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Consultez également les ressources suivantes pour en savoir plus sur Azure Storage et les machines virtuelles Azure :

* [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure.](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
