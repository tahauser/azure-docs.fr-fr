---
title: "Copie ou déplacement des données vers le stockage Azure avec AzCopy sur Windows | Microsoft Docs"
description: "Utilisez l’utilitaire AzCopy sur Windows pour déplacer ou copier des données vers ou à partir de contenu de blob, de table et de fichier. Copiez des données vers Azure Storage à partir de fichiers locaux ou copiez des données dans ou entre des comptes de stockage. Migrez facilement vos données vers Azure Storage."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: seguler
ms.openlocfilehash: 7d875a1e43908f49424f4e40fe923639cfa02385
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transférer des données avec AzCopy sur Windows
AzCopy est un utilitaire de ligne de commande conçu pour copier des données à partir de/vers un stockage Blob, Fichier et Table Microsoft Azure en utilisant des commandes simples avec des performances optimales. Vous pouvez copier des données entre un système de fichiers et un compte de stockage, ou entre comptes de stockage.  

Il existe deux versions d’AzCopy que vous pouvez télécharger. AzCopy sur Windows est intégré à .NET Framework et offre des options en ligne de commande de style Windows. [AzCopy sur Linux](storage-use-azcopy-linux.md) est intégré à .NET Core Framework, qui cible les plateformes Linux en offrant des options en ligne de commande de style POSIX. Cet article est consacré à AzCopy sur Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Téléchargement et installation d’AzCopy sur Windows

Téléchargez la [dernière version d’AzCopy sur Windows](http://aka.ms/downloadazcopy).

Après avoir installé AzCopy sur Windows via le programme d’installation, ouvrez une fenêtre de commande, puis naviguez jusqu’au répertoire d’installation d’AzCopy sur votre ordinateur, où se trouve l’exécutable `AzCopy.exe`. Si vous le souhaitez, vous pouvez ajouter l’emplacement d’installation d’AzCopy au chemin de votre système. Par défaut, AzCopy est installé dans `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` ou `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Écriture de votre première commande AzCopy

La syntaxe de base d’une commande AzCopy est :

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Les exemples suivants montrent différents scénarios permettant de copier des données vers et à partir d’objets blob Microsoft Azure, les fichiers et les tables. Reportez-vous à la section [Paramètres AzCopy](#azcopy-parameters) pour obtenir une explication détaillée des paramètres utilisés dans chaque échantillon.

## <a name="download-blobs-from-blob-storage"></a>Télécharger des objets blob depuis un stockage d’objets blob

Examinons plusieurs façons de télécharger des objets blob à l’aide d’AzCopy.

### <a name="download-a-single-blob"></a>Télécharger un seul objet blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Remarque : Si le dossier `C:\myfolder` n’existe pas encore, AzCopy le crée dans le système de fichiers et télécharge `abc.txt ` dans le nouveau dossier.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Télécharger un seul objet blob depuis la région secondaire

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Notez que vous devez avoir un accès en lecture activé pour le stockage géoredondant.

### <a name="download-all-blobs-in-a-container"></a>Téléchargement de tous les objets blob d’un conteneur

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Si les objets blob suivants se trouvent dans le conteneur spécifié :  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Après l’opération de téléchargement, le répertoire `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Si vous ne spécifiez pas l’option `/S`, aucun objet blob n’est téléchargé.

### <a name="download-blobs-with-a-specific-prefix"></a>Téléchargement d’objets blob avec un préfixe spécifique

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Si les objets blob suivants se trouvent dans le conteneur spécifié, Tous les blobs commençant par le préfixe `a` sont téléchargés :

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Après l’opération de téléchargement, le dossier `C:\myfolder` inclut les fichiers suivants :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Le préfixe s’applique au répertoire virtuel, qui forme la première partie du nom de l’objet blob. Dans l’exemple ci-dessus, le répertoire virtuel ne correspond pas au préfixe spécifié ; il n’est donc pas téléchargé. En outre, si l’option `\S` n’est pas spécifiée, AzCopy ne télécharge pas les blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Définition de l’heure de la dernière modification des fichiers exportés pour qu’elle soit identique à celle des objets blob source

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

Vous pouvez également exclure des objets blob de l’opération de téléchargement en vous basant sur l’heure de leur dernière modification Par exemple, si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus récente que celle du fichier de destination `/XN` :

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus ancienne que celle du fichier de destination `/XO` :

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Chargement d’objets blob vers un stockage d'objets blob

Examinons plusieurs façons de charger des objets blob à l’aide d’AzCopy.

### <a name="upload-a-single-blob"></a>Charger un seul objet blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Si le conteneur de destination spécifié n’existe pas, AzCopy le crée et y charge le fichier.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Charger un seul objet blob dans un répertoire virtuel

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Si le répertoire virtuel spécifié n’existe pas, AzCopy charge le fichier pour y inclure le répertoire virtuel dans son nom (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### <a name="upload-all-blobs-in-a-folder"></a>Charger tous les objets blob dans un dossier

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

La spécification de l’option `/S` engendre le chargement des contenus du répertoire spécifié vers le stockage Blob récursivement, ce qui implique également la copie de tous les sous-dossiers et de leurs fichiers. Par exemple, si les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l’opération de chargement, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy ne charge pas de manière récursive. Après l’opération de chargement, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Chargement des objets blob correspondant à un modèle spécifique

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Si les fichiers suivants se trouvent dans le dossier `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Après l’opération de chargement, le conteneur inclut les fichiers suivants :

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si vous ne spécifiez pas l’option `/S`, AzCopy télécharge uniquement les objets blob qui ne se trouvent pas dans un répertoire virtuel :

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Spécifier le type de contenu MIME d’un objet blob de destination

Par défaut, AzCopy définit le type de contenu d'un objet blob de destination comme `application/octet-stream`. Depuis la version 3.1.0, vous pouvez spécifier explicitement le type de contenu via l'option `/SetContentType:[content-type]`. Cette syntaxe définit le type de contenu de tous les objets blob dans une opération de téléchargement.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Si vous spécifiez `/SetContentType` sans valeur, AzCopy définit chaque type de contenu d’objet blob ou de fichier en fonction de son extension de fichier.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copie d’objets blob dans un stockage d’objets blob

Examinons maintenant plusieurs méthodes pour copier des objets blob d’un emplacement vers un autre à l’aide d’AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copie d’un seul objet blob d’un conteneur vers un autre dans le même compte de stockage 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Lorsque vous copiez un objet blob au sein d’un compte de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copie d’un seul objet blob d’un compte de stockage à un autre

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Lorsque vous copiez un objet blob sur plusieurs comptes de stockage, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copie d’un seul objet blob de la région secondaire vers la région primaire

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Notez que vous devez avoir un accès en lecture activé pour le stockage géoredondant pour accéder au stockage secondaire.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copie d’un seul objet blob et de ses captures instantanées d’un compte de stockage vers un autre

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Après l’opération de copie, le conteneur cible inclut le blob et ses captures instantanées. Si on part du principe que l’exemple ci-dessus comprend deux captures instantanées, le conteneur inclut l’objet blob et les captures instantanées suivants :

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copie de tous les objets blob d’un conteneur vers un autre compte de stockage 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

L’option /S charge de manière récursive les données du conteneur spécifié. Consultez [Charger tous les objets blob dans un dossier](#upload-all-blobs-in-a-folder) pour obtenir plus d’informations et afficher un exemple.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Copie d’objets blob de façon synchrone d’un compte de stockage vers un autre

AzCopy copie par défaut les données entre deux points de terminaison de stockage de façon asynchrone. Par conséquent, l’opération de copie s’exécute en arrière-plan grâce à la capacité de la bande passante disponible, non soumise à un SLA en matière de vitesse de copie d’un objet blob. AzCopy vérifie périodiquement l’état de copie jusqu’à ce que la copie soit terminée ou ait échoué.

L’option `/SyncCopy` garantit que l’opération de copie a une vitesse constante. AzCopy effectue la copie synchrone en téléchargeant les objets blob à copier à partir de la source spécifiée dans la mémoire locale, puis en les téléchargeant sur la destination de stockage d’objets blob.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone, l’approche recommandée consiste à utiliser cette option dans une machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

## <a name="download-files-from-file-storage"></a>Téléchargement de fichiers à partir d’un stockage de fichiers

Examinons plusieurs façons de télécharger des fichiers à l’aide d’AzCopy.

### <a name="download-a-single-file"></a>Téléchargement d’un seul fichier

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Si la source spécifiée est un partage de fichier Azure, vous devez soit spécifier le nom de fichier exact, (*par exemple,* `abc.txt`) pour télécharger un fichier unique, soit spécifier l’option `/S` pour télécharger tous les fichiers dans le partage de manière récursive. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l’option `/S`.

### <a name="download-all-files-in-a-directory"></a>Téléchargement de tous les fichiers d’un répertoire

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Notez que les dossiers vides ne sont pas téléchargés.

## <a name="upload-files-to-an-azure-file-share"></a>Charger des fichiers dans un partage de fichiers Azure

Examinons plusieurs façons de charger des fichiers à l’aide d’AzCopy.

### <a name="upload-a-single-file"></a>Chargement d’un seul fichier

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Chargement de tous les fichiers d’un dossier

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Notez que les dossiers vides ne sont pas chargés.

### <a name="upload-files-matching-a-specific-pattern"></a>Téléchargement des fichiers correspondant à un modèle spécifique

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copie des fichiers dans un stockage de fichiers

Examinons plusieurs façons de copier des fichiers dans un partage de fichiers Azure à l’aide d’AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copie d’un partage de fichiers vers un autre

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Lorsque vous copiez un fichier sur plusieurs partage de fichiers, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copie d’un partage de fichiers Azure vers un stockage d’objets blob

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Lorsque vous copiez un fichier d’un partage de fichiers vers un objet blob, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copie d’un objet blob d’un stockage d’objets blob vers un partage de fichiers Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Lorsque vous copiez un fichier d’un objet blob vers un partage de fichiers, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="synchronously-copy-files"></a>Copier les fichiers de façon synchrone

Vous pouvez spécifier l’option `/SyncCopy` pour copier d’un stockage de fichiers vers un autre stockage de fichiers, d’un stockage de fichiers vers un stockage d’objet blob et d’un stockage d’objet blob o un stockage de fichiers de façon synchrone. Pour ce faire, AzCopy télécharge les données sources dans la mémoire locale, puis les charge à nouveau vers la destination. Dans ce cas, des coûts de sortie standard s’appliquent.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Pendant la copie d’un stockage de fichiers vers un stockage d'objets blob, le type d'objet blob par défaut est l'objet blob de blocs : l'utilisateur peut spécifier l'option `/BlobType:page` pour modifier le type d'objet blob de destination.

Notez que `/SyncCopy` peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone. L’approche recommandée consiste à utiliser cette option dans la machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

## <a name="export-data-from-table-storage"></a>Exportation de données à partir d’un stockage de tables

Examinons l’exportation de données à partir d’un stockage de tables Azure à l’aide d’AzCopy.

### <a name="export-a-table"></a>Exporter une table

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy écrit un fichier manifeste dans le dossier de destination spécifié. Le processus d’importation utilise ce fichier manifeste pour localiser les fichiers de données nécessaires et effectuer la validation des données. Le fichier manifeste utilise la convention de noms suivante par défaut :

    <account name>_<table name>_<timestamp>.manifest

L'utilisateur peut également spécifier l'option `/Manifest:<manifest file name>` pour définir le nom du fichier manifeste.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>Fractionnement d’une exportation d’un stockage de tables en plusieurs fichiers

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy utilise un *index de volume* dans les noms des fichiers de données fractionnés pour distinguer les fichiers. L’index de volume se compose de deux parties : un *index de plage de clés de partition* et un *index de fichier fractionné*. Ces deux index commencent à zéro.

L’index de plage de clés de partition est égal à 0 si l’utilisateur ne spécifie pas l’option `/PKRS`.

Exemple : supposons qu'AzCopy crée deux fichiers de données après que l'utilisateur a spécifié l'option `/SplitSize`. Les noms des fichiers de données qui en résultent peuvent être :

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Remarque : la valeur minimale possible pour l’option `/SplitSize` est 32 Mo. Si la destination spécifiée est un stockage Blob, AzCopy fractionne le fichier de données lorsque sa taille atteint la limite de taille des objets blob (200 Go), que l’utilisateur ait spécifié ou non l’option `/SplitSize` .

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exporter une table dans un format de fichier de données CSV ou JSON

Par défaut, AzCopy exporte des tables dans des fichiers de données JSON. Vous pouvez spécifier l’option `/PayloadFormat:JSON|CSV` pour exporter les tables en tant que JSON ou CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Lorsque vous spécifiez le format de charge utile CSV, AzCopy génère également un fichier de schéma avec l’extension `.schema.csv` pour chaque fichier de données.

### <a name="export-table-entities-concurrently"></a>Exportation simultanée d’entités de table

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy lance des opérations simultanées d’exportation d’entités lorsque l’utilisateur spécifie l’option `/PKRS`. Chaque opération exporte une plage de clés de partition.

Remarque : l'option `/NC`contrôle également le nombre d'opérations simultanées. AzCopy utilise le nombre de processeurs Core comme valeur par défaut de `/NC` pendant la copie d'entités de table, même si l'option `/NC` n'a pas été spécifiée. Lorsque l'utilisateur spécifie l'option `/PKRS`, AzCopy utilise la plus petite des deux valeurs (plages de clés de partition par rapport aux opérations simultanées implicitement ou explicitement spécifiées) pour déterminer le nombre d'opérations simultanées à démarrer. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

### <a name="export-a-table-to-blob-storage"></a>Exportation d’une table vers un stockage d’objets blob

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy crée un fichier de données JSON dans le conteneur d’objets blob en respectant la convention de noms suivante :

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

Le fichier de données JSON créé respecte le format de charge utile pour les métadonnées minimales. Pour des informations sur le format de charge utile, consultez la page [Format de charge utile pour les opérations du service de Table](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Notez que lors de l’exportation des tables vers les objets blob, AzCopy télécharge les entités de Table vers les fichiers de données temporaires locaux et télécharge ensuite ces entités dans le blob. Ces fichiers de données temporaires sont placés dans le dossier du fichier journal avec le chemin par défaut « <code>%LocalAppData%\Microsoft\Azure\AzCopy</code> ». Vous pouvez spécifier l’option /Z:[dossier du fichier journal] pour modifier l’emplacement du dossier du fichier et changer ainsi l’emplacement des fichiers de données temporaires. La taille des fichiers de données temporaires est définie par la taille de vos entités de table et la taille spécifiée avec l’option /SplitSize, bien que le fichier de données temporaire dans le disque local soit supprimé instantanément une fois qu’il a été chargé vers le blob. Vérifiez que vous disposez de suffisamment d’espace sur le disque local pour stocker ces fichiers de données temporaires avant qu’ils soient supprimés.

## <a name="import-data-into-table-storage"></a>Importation de données vers un stockage de tables

Examinons l’importation de données dans un stockage de tables Azure à l’aide d’AzCopy.

### <a name="import-a-table"></a>Importer une table

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

L'option `/EntityOperation` indique comment insérer des entités dans la table. Les valeurs possibles sont les suivantes :

* `InsertOrSkip`: ignore une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
* `InsertOrMerge`: fusionne une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.
* `InsertOrReplace` : remplace une entité existante ou insère une nouvelle entité si elle n'existe pas dans la table.

Remarque : vous ne pouvez pas spécifier l'option `/PKRS` dans le scénario d'importation. À la différence du scénario d’exportation dans lequel vous devez spécifier l’option `/PKRS` pour démarrer des opérations simultanées, AzCopy lance des opérations simultanées par défaut lorsque vous importez une table. Le nombre par défaut d’opérations simultanées démarrées est égal au nombre de processeurs Core. Cependant, vous pouvez spécifier un nombre différent d’opérations simultanées avec l’option `/NC`. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

Notez qu’AzCopy ne prend en charge que l’importation pour JSON, et non CSV. AzCopy ne prend pas en charge les importations de table à partir de fichiers JSON créés par l’utilisateur et de fichiers manifeste. Ces deux types de fichiers doivent provenir d’une exportation de table AzCopy. Pour éviter les erreurs, ne modifiez pas le fichier JSON ou le fichier manifeste exporté.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importation d’entités dans une table à partir d’un stockage d’objets blob

Supposons qu’un conteneur d’objets blob contient les éléments suivants : fichier JSON représentant une table Azure et le fichier manifeste associé.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Vous pouvez exécuter la commande suivante pour importer des entités dans une table en utilisant le fichier manifeste dans le conteneur d’objets blob :

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Autres fonctionnalités AzCopy

Examinons d’autres fonctionnalités AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copier uniquement les données qui n’existent pas dans la destination

Les paramètres `/XO` et `/XN` vous permettent d’exclure les ressources source plus anciennes ou plus récentes d’être copiées respectivement. Si vous souhaitez copier uniquement des ressources de code source qui n’existent pas dans la destination, vous pouvez spécifier les deux paramètres dans la commande AzCopy :

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Notez que cela n’est pas pris en charge lorsque la source ou la destination est une table.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Utilisation d’un fichier réponse pour spécifier les paramètres de ligne de commande

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Vous pouvez inclure n’importe quels paramètres de ligne de commande AzCopy dans un fichier réponse. AzCopy traite les paramètres du fichier comme s’ils avaient été spécifiés sur la ligne de commande, réalisant une substitution directe avec les contenus du fichier.

Si un fichier réponse nommé `copyoperation.txt`, qui contient les lignes suivantes : Chaque paramètre AzCopy peut être spécifié sur une seule ligne

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

ou sur des lignes distinctes :

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy échoue si vous écrivez le paramètre sur deux lignes, comme démontré ici pour le paramètre `/sourcekey` :

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Utilisation de plusieurs fichiers réponse pour spécifier les paramètres de ligne de commande

Si un fichier réponse dénommé `source.txt` qui spécifie un conteneur source :

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Et un fichier réponse nommé `dest.txt` qui spécifie un dossier de destination dans le système de fichiers :

    /Dest:C:\myfolder

Et un fichier réponse nommé `options.txt` qui spécifie les options pour AzCopy :

    /S /Y

Appellent AzCopy avec ces fichiers réponse résidant tous dans un répertoire `C:\responsefiles`, utilisez la commande suivante :

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy traite cette commande comme si vous aviez inclus tous les paramètres individuels de la ligne de commande :

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Spécification d’une signature d’accès partagé (SAP)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

Vous pouvez également spécifier une SAP sur l’URI du conteneur :

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Dossier du fichier journal

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe à aucun de ces emplacements, AzCopy considère l’opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération incomplète. Si elles ne correspondent pas, il vous est demandé soit d’écraser le fichier journal pour démarrer une nouvelle opération, soit d’annuler l’opération actuelle.

Si vous souhaitez utiliser l’emplacement par défaut pour le fichier journal :

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Si vous omettez l'option `/Z`, ou spécifiez l'option `/Z` sans le chemin du dossier, comme démontré ci-dessus, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez spécifier un emplacement personnalisé pour le fichier journal :

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Cet exemple crée le fichier journal s’il n’existe pas déjà. S’il existe, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez reprendre une opération AzCopy :

```azcopy
AzCopy /Z:C:\journalfolder\
```

Cet exemple reprend la dernière opération, qui est susceptible de ne pas avoir abouti.

### <a name="generate-a-log-file"></a>Génération d’un fichier journal

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Si vous spécifiez l’option `/V` sans fournir de chemin de fichier pour le journal détaillé, AzCopy crée le fichier journal à l’emplacement par défaut, qui est `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

Autrement, vous pouvez créer un fichier journal dans un emplacement personnalisé :

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Remarque : si vous spécifiez un chemin relatif suivant l'option `/V`, tel que `/V:test/azcopy1.log`, le journal détaillé est alors créé dans le répertoire en cours d'utilisation dans un sous-dossier nommé `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Spécification du nombre d’opérations simultanées pour démarrer

L'option `/NC` spécifie le nombre d'opérations de copie simultanées. Par défaut, AzCopy lance un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert des données. Pour les opérations sur les tables, le nombre d’opérations simultanées est égal au nombre de processeurs dont vous disposez. Pour les opérations sur les objets blob et les fichiers, le nombre d’opérations simultanées est égal à 8 fois le nombre de processeurs dont vous disposez. Si vous exécutez AzCopy sur un réseau à bande passante étroite, vous pouvez spécifier un nombre inférieur pour /NC afin d’éviter l’échec causé par la compétition de ressources.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Exécution d’AzCopy sur l’émulateur de stockage Azure

Vous pouvez exécuter AzCopy sur l’[émulateur de stockage Azure](storage-use-emulator.md) pour les objets blob :

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

Vous pouvez également l’exécuter pour des tables :

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>Paramètres AzCopy

Les paramètres d’AzCopy sont décrits ci-dessous. Vous pouvez également taper une des commandes suivantes dans la ligne de commande pour obtenir de l’aide sur l’utilisation d’AzCopy :

* Pour l'aide détaillée sur la ligne de commande AzCopy : `AzCopy /?`
* Pour l'aide détaillée sur un paramètre AzCopy : `AzCopy /?:SourceKey`
* Pour des exemples de ligne de commande : `AzCopy /?:Samples`

### <a name="sourcesource"></a>/Source:"source"

Spécifie les données sources à partir desquelles la copie peut s’effectuer. La source peut être un répertoire du système de fichiers, un conteneur d’objets blob, un répertoire virtuel d’objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**S’applique à :** objets blob, fichiers, tables

### <a name="destdestination"></a>/Dest:"destination"

Spécifie la destination vers laquelle la copie va s’effectuer. La destination peut être un répertoire du système de fichiers, un conteneur d’objets blob, un répertoire virtuel d’objets blob, un partage de fichiers de stockage, un répertoire de fichiers de stockage ou une table Azure.

**S’applique à :** objets blob, fichiers, tables

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"

Spécifie un modèle de fichier qui indique les fichiers à copier. Le comportement du paramètre /Pattern est déterminé par l’emplacement des données sources et la présence de l’option mode récursif. Le mode récursif est spécifié via l’option /S.

Si la source spécifiée est un répertoire dans le système de fichiers, les caractères génériques standard sont appliqués et le modèle de fichier fourni est comparé aux fichiers présents dans le répertoire. Si l’option /S est spécifiée, AzCopy compare également le modèle spécifié à tous les fichiers présents dans les sous-dossiers du répertoire.

Si la source spécifiée est un conteneur d’objets blob ou un répertoire virtuel, les caractères génériques ne sont pas appliqués. Si l’option /S est spécifiée, AzCopy interprète le modèle de fichier spécifié comme un préfixe d’objet blob. Si l’option /S n’est pas spécifiée, AzCopy compare le modèle de fichier aux noms exacts d’objets blob.

Si la source spécifiée est un partage de fichiers Azure, vous devez soit spécifier le nom exact du fichier (abc.txt) pour copier un seul fichier, soit spécifier l’option /S pour copier récursivement tous les fichiers dans le partage. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l’option /S.

AzCopy tient compte de la casse uniquement quand la /Source est un conteneur d’objets blob ou un répertoire virtuel d’objets blob.

Le modèle de fichier par défaut utilisé lorsqu’aucun modèle de fichier n’est spécifié est *.* pour un emplacement de système de fichiers, ou un préfixe vide pour un emplacement Azure Storage. La spécification de plusieurs modèles de fichiers n’est pas prise en charge.

**S’applique à :** objets blob, fichiers

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Spécifie la clé du compte de stockage pour la ressource de destination.

**S’applique à :** objets blob, fichiers, tables

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Spécifie une signature d’accès partagé (SAP) avec les autorisations de lecture et d’écriture pour la destination (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.

Si la ressource de destination est un conteneur d’objets blob, un partage de fichiers ou une table, vous pouvez spécifier soit cette option suivie du jeton SAP, soit la SAP comme élément d’URI de l’objet blob, du partage de fichiers ou de la table de destination, sans cette option.

Si la source et la destination sont toutes les deux des objets blob, l’objet blob de destination doit se trouver dans le même compte de stockage que l’objet blob source.

**S’applique à :** objets blob, fichiers, tables

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Spécifie la clé du compte de stockage pour la ressource source.

**S’applique à :** objets blob, fichiers, tables

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Spécifie une signature d’accès partagé avec les autorisations de lecture et de listing pour la source (le cas échéant). Ajoutez des guillemets à la SAP, car elle peut contenir des caractères spéciaux de ligne de commande.

Si la ressource source est un conteneur d’objets blob et si aucune clé ou SAP n’est fournie, le conteneur est lu via un accès anonyme.

Si la source est un partage de fichiers ou une table, une clé ou une SAP doit être fournie.

**S’applique à :** objets blob, fichiers, tables

### <a name="s"></a>/S

Spécifie le mode récursif pour les opérations de copie. En mode récursif, AzCopy copie tous les fichiers ou objets blob correspondant au modèle de fichier spécifié, incluant ceux qui se trouvent dans les sous-dossiers.

**S’applique à :** objets blob, fichiers

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Spécifie si la destination est un objet blob de blocs, un objet blob de pages ou un objet blob d’ajout. Cette option s’applique uniquement lorsque vous téléchargez un objet blob. Sinon, une erreur se produit. Si la destination est un objet blob et si cette option n’est pas spécifiée, AzCopy crée par défaut un objet blob de blocs.

**S’applique à :** objets blob

### <a name="checkmd5"></a>/CheckMD5

Calcule un hachage MD5 pour les données téléchargées et vérifie que le hachage MD5 stocké dans la propriété Content-MD5 de l'objet blob ou du fichier correspond au hachage calculé. La vérification MD5 est désactivée par défaut ; vous devez donc spécifier cette option pour lancer la vérification MD5 lorsque vous téléchargez des données.

Remarque : Azure Storage ne garantit pas que le hachage MD5 stocké pour l’objet blob ou le fichier est à jour. Il est de la responsabilité du client de mettre à jour le MD5 lorsque l’objet blob ou le fichier est modifié.

AzCopy établit toujours la propriété Content-MD5 pour un objet blob ou fichier Azure après l’avoir chargé sur le service.  

**S’applique à :** objets blob, fichiers

### <a name="snapshot"></a>/Snapshot

Indique si le transfert de captures instantanées est activé ou non. Cette option est valide uniquement lorsque la source est un objet blob.

Les captures instantanées d’objets blob transférées sont renommées de cette façon : nom_d’objet_Blob (durée de capture instantanée).extension

Par défaut, les captures instantanées ne sont pas copiées.

**S’applique à :** objets blob

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Stocke les messages de statut détaillés dans un fichier journal.

Par défaut, le fichier journal détaillé est nommé dans `%LocalAppData%\Microsoft\Azure\AzCopy`. Si vous spécifiez un emplacement de fichier existant pour cette option, le journal détaillé est ajouté à ce fichier.  

**S’applique à :** objets blob, fichiers, tables

### <a name="zjournal-file-folder"></a>/Z:[journal-file-folder]

Spécifie un dossier de fichier journal pour reprendre une opération.

AzCopy peut toujours reprendre une opération qui a été interrompue.

Si cette option n’est pas spécifiée ou est spécifiée sans chemin de dossier, AzCopy crée le fichier journal à l’emplacement par défaut, qui est %LocalAppData%\Microsoft\Azure\AzCopy.

Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe à aucun de ces emplacements, AzCopy considère l’opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération incomplète. Si elles ne correspondent pas, il vous est demandé soit d’écraser le fichier journal pour démarrer une nouvelle opération, soit d’annuler l’opération actuelle.

Le fichier journal est supprimé lorsque l’opération est achevée avec succès.

Remarque : reprendre une opération à partir d’un fichier journal créé par une version précédente d’AzCopy n’est pas pris en charge.

**S’applique à :** objets blob, fichiers, tables

### <a name="parameter-file"></a>/@:"parameter-file"

Spécifie un fichier qui contient des paramètres. AzCopy traite les paramètres dans le fichier comme s’ils avaient été spécifiés dans la ligne de commande.

Dans un fichier réponse, vous pouvez soit spécifier de multiples paramètres sur une seule ligne, soit spécifier chaque paramètre sur sa propre ligne. Remarque : un paramètre individuel ne peut pas couvrir plusieurs lignes.

Les fichiers réponse peuvent inclure des lignes de commentaires qui commencent par le symbole #.

Vous pouvez spécifier plusieurs fichiers réponse. Toutefois, AzCopy ne prend pas en charge les fichiers réponse imbriqués.

**S’applique à :** objets blob, fichiers, tables

### <a name="y"></a>/Y

Supprime toutes les invites de confirmation d’AzCopy. Cette option autorise également l’utilisation de jetons SAS en écriture seule pour les scénarios de chargement de données, quand /XO et /XN ne sont pas spécifiés.

**S’applique à :** objets blob, fichiers, tables

### <a name="l"></a>/L

Spécifie une opération de listing uniquement : aucune donnée n’est copiée.

AzCopy interprète l’utilisation de cette option comme une simulation de l’exécution de la ligne de commande sans cette option /L et compte le nombre d’objets copiés. Vous pouvez spécifier l’option /V en même temps pour déterminer les objets destinés à être copiés dans le journal détaillé.

Le comportement de cette option est également déterminé par l’emplacement des données sources et la présence de l’option mode récursif /S et de l’option modèle de fichier /Pattern.

AzCopy nécessite les autorisations de listing et de lecture sur cet emplacement source quand cette option est utilisée.

**S’applique à :** objets blob, fichiers

### <a name="mt"></a>/MT

Définit l’heure de la dernière modification du fichier pour qu’elle soit identique à celle de l’objet blob ou du fichier source.

**S’applique à :** objets blob, fichiers

### <a name="xn"></a>/XN

Exclut une ressource de source plus récente. La ressource n’est pas copiée si la dernière heure de modification de la source est identique ou plus récente que celle de la destination.

**S’applique à :** objets blob, fichiers

### <a name="xo"></a>/XO
Exclut une ressource de source plus ancienne. La ressource n’est pas copiée si la dernière heure de modification de la source est identique ou moins récente que celle de la destination.

**S’applique à :** objets blob, fichiers

### <a name="a"></a>/A

Charge uniquement les fichiers dont l’attribut Archive est défini.

**S’applique à :** objets blob, fichiers

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Télécharge uniquement les fichiers qui ont le jeu d’attributs spécifiés.

Les attributs disponibles incluent :

* R = Fichiers en lecture seule
* A = Fichiers prêts à être archivés
* S = Fichiers système
* H = Fichiers masqués
* C = Fichiers compressés
* N = Fichiers normaux
* E = Fichiers cryptés
* T = Fichiers temporaires
* O = Fichiers hors ligne
* I = Fichiers non indexés

**S’applique à :** objets blob, fichiers

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Exclut les fichiers dont l’un des attributs spécifiés est défini.

Les attributs disponibles incluent :

* R = Fichiers en lecture seule
* A = Fichiers prêts à être archivés
* S = Fichiers système
* H = Fichiers masqués
* C = Fichiers compressés
* N = Fichiers normaux
* E = Fichiers cryptés
* T = Fichiers temporaires
* O = Fichiers hors ligne
* I = Fichiers non indexés

**S’applique à :** objets blob, fichiers

### <a name="delimiterdelimiter"></a>/Delimiter:"délimiteur"

Indique le caractère délimiteur utilisé pour délimiter les répertoires virtuels dans un nom d’objet blob.

Par défaut, AzCopy utilise / comme caractère délimiteur. Toutefois, AzCopy prend en charge n’importe quel caractère commun (tel que @, #, ou %) comme délimiteur. Si vous avez besoin d’inclure l’un de ces caractères spéciaux dans la ligne de commande, ajoutez des guillemets doubles au nom du fichier.

Cette option est applicable uniquement au téléchargement d’objets blob.

**S’applique à :** objets blob

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"nombre-d’opérations-simultanées"

Spécifie le nombre d’opérations simultanées.

AzCopy lance par défaut un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert des données. Remarque : un grand nombre d’opérations simultanées dans un environnement à faible bande passante peut surcharger la connexion réseau et entraver le bon déroulement des opérations. Limitez les opérations simultanées en fonction de la bande passante de réseau qui est disponible.

Le nombre maximal d’opérations simultanées est égal à 512.

**S’applique à :** objets blob, fichiers, tables

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Spécifie que la ressource `source` est un objet blob disponible dans l’environnement de développement local, exécuté sur l’émulateur de stockage.

**S’applique à :** objets blob, tables

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Spécifie que la ressource `destination` est un objet blob disponible dans l’environnement de développement local, exécuté sur l’émulateur de stockage.

**S’applique à :** objets blob, tables

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Fractionne la plage de clés de partition pour activer l’exportation des données de la table en parallèle, ce qui augmente la vitesse d’exportation.

Si cette option n’est pas spécifiée, AzCopy utilise un seul thread pour exporter des entités de table. Exemple : si l’utilisateur spécifie /PKRS:"aa#bb", AzCopy lance trois opérations simultanées.

Chaque opération exporte une des trois plages de clés de partition (voir ci-dessous) :

  [first-partition-key, aa)

  [aa, bb)

  [bb, dernière-clé-de-partition]

**S’applique à :** tables

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Spécifie la taille de fractionnement du fichier exporté en Mo. La valeur minimale autorisée est de 32.

Si cette option n’est pas spécifiée, AzCopy exporte les données de la table dans un seul fichier.

Si les données de la table sont exportées dans un objet blob et si la taille du fichier exporté atteint la limite de 200 Go pour la taille de l’objet blob, AzCopy fractionne le fichier exporté, même si cette option n’est pas spécifiée.

**S’applique à :** tables

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Spécifie le comportement pour l’importation des données d’une table.

* InsertOrSkip - Ignore une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.
* InsertOrMerge - Fusionne une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.
* InsertOrReplace - Remplace une entité existante ou insère une nouvelle entité si elle n’existe pas dans la table.

**S’applique à :** tables

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Spécifie le fichier manifeste pour l’importation et l’exportation de la table.

Cette option est facultative pendant l’exportation ; AzCopy génère un fichier manifeste avec un nom prédéfini si cette option n’est pas spécifiée.

Cette option est nécessaire pendant l’importation pour localiser les fichiers de données.

**S’applique à :** tables

### <a name="synccopy"></a>/SyncCopy

Indique s’il faut copier de manière synchronisée les objets blob ou les fichiers entre deux points de terminaison Azure Storage.

AzCopy utilise par défaut la copie asynchrone du côté serveur. Spécifiez cette option pour effectuer une copie synchrone, qui télécharge les objets blob ou les fichiers vers la mémoire locale et les télécharge Azure Storage.

Vous pouvez utiliser cette option pour la copie de fichiers dans le stockage d’objets blob, le stockage de fichiers ou depuis le stockage d’objets blob vers le stockage de fichiers ou vice versa.

**S’applique à :** objets blob, fichiers

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Spécifie le type de contenu MIME pour les fichiers ou les objets blob de destination.

AzCopy définit le type de contenu d’un objet blob ou un fichier sur application/octet-stream par défaut. Vous pouvez définir le type de contenu pour tous les objets blob ou les fichiers en spécifiant explicitement une valeur pour cette option.

Si vous spécifiez cette option sans valeur, AzCopy définit chaque type de contenu d’objet blob ou de fichier en fonction de son extension de fichier.

**S’applique à :** objets blob, fichiers

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Spécifie le format du fichier de données de table exporté.

Si cette option n’est pas spécifiée, AzCopy exporte le fichier de données de table au format JSON par défaut.

**S’applique à :** tables

## <a name="known-issues-and-best-practices"></a>Problèmes connus et les meilleures pratiques

Examinons quelques problèmes connus et meilleures pratiques.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitation des écritures simultanées lors de la copie des données

Lorsque vous copiez des objets blob ou des fichiers avec AzCopy, gardez en tête qu’une autre application peut être en train de modifier les données pendant que vous les copiez. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées pendant l’opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel (VHD) associé à une machine virtuelle Azure, assurez-vous qu’aucune autre application n’est en train d’écrire sur le disque VHD. Un bon moyen pour ce faire consiste à louer la ressource à copier. Sinon, vous pouvez commencer par créer une capture instantanée du disque VHD et copier ensuite la capture instantanée.

Si vous ne pouvez pas empêcher d’autres applications d’écrire sur les objets blob ou les fichiers pendant qu’ils sont copiés, gardez en tête qu’au moment où la tâche sera terminée, les ressources copiées n’auront peut-être plus une parité complète avec les ressources source.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Exécuter une instance de AzCopy sur un même ordinateur.

AzCopy est conçu pour optimiser l'utilisation de votre ressource de l'ordinateur afin d’accélérer le transfert de données. Nous vous recommandons d'exécuter une seule instance de AzCopy sur un même ordinateur et de spécifier l'option `/NC` si vous avez besoin de plus d'opérations simultanées. Pour plus d'informations, tapez `AzCopy /?:NC` dans la ligne de commande.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Activer les algorithmes MD5 compatibles FIPS pour AzCopy quand vous « utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature ».

Par défaut, AzCopy utilise l’implémentation MD5 .NET pour calculer le hachage MD5 pendant la copie d’objets, mais en raison de certaines exigences de sécurité, AzCopy doit activer le paramètre MD5 compatible FIPS.

Vous pouvez créer un fichier app.config `AzCopy.exe.config` avec la propriété `AzureStorageUseV1MD5` et le mettre à part avec AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Pour la propriété « AzureStorageUseV1MD5 » :

* Si la propriété MD5 a pour valeur true (la valeur par défaut), AzCopy utilise l’implémentation MD5 .NET.
* Si elle a pour valeur false, AzCopy utilise l’algorithme MD5 compatible FIPS.

Les algorithmes compatibles FIPS sont désactivés par défaut sur Windows. Vous pouvez modifier ce paramètre de stratégie sur votre ordinateur. Dans la fenêtre Exécuter (Windows+R), tapez secpol.msc pour ouvrir la fenêtre **Stratégie de sécurité locale**. Dans la fenêtre **Paramètres de sécurité**, sélectionnez **Paramètres de sécurité** > **Stratégies locales** > **Options de sécurité**. Recherchez la stratégie **Chiffrement système : utilisez des algorithmes compatibles FIPS pour le chiffrement, le hachage et la signature**. Double-cliquez sur la stratégie pour montrer la valeur affichée dans la colonne **Paramètre de sécurité**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage Azure et AzCopy, consultez les ressources suivantes :

### <a name="azure-storage-documentation"></a>Documentation d’Azure Storage :
* [Introduction à Azure Storage](../storage-introduction.md)
* [Utilisation du stockage d’objets blob à partir de .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Utilisation du stockage de fichiers à partir de .NET](../storage-dotnet-how-to-use-files.md)
* [Utilisation du stockage de tables à partir de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account.md)
* [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Billets de blog Azure Storage :
* [Présentation de la bibliothèque de déplacement des données dans Azure Storage en version préliminaire](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy : Présentation de la copie synchrone et du type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy : Annonce de la disponibilité générale d'AzCopy 3.0 plus version préliminaire d'AzCopy 4.0 avec prise en charge de fichier et de table](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy : Optimisation pour les scénarios de copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy – Transfer data with re-startable mode and SAS Token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx) (AzCopy : Transfert des données avec mode reprise et jeton SAP)
* [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)
