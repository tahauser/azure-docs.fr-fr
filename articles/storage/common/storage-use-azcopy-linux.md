---
title: "Copie ou déplacement des données vers le stockage Azure avec AzCopy sur Linux | Microsoft Docs"
description: "Utilisez l’utilitaire AzCopy sur Linux pour déplacer ou copier des données vers ou à partir de contenu de blob et de fichier. Copiez des données vers Azure Storage à partir de fichiers locaux ou copiez des données dans ou entre des comptes de stockage. Migrez facilement vos données vers Azure Storage."
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
ms.date: 12/11/2017
ms.author: seguler
ms.openlocfilehash: 2fd89684176cd832b656dae8c8f94a6f1ccbbbe8
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transférer des données avec AzCopy sur Linux

AzCopy est un utilitaire de ligne de commande conçu pour copier des données à partir de/vers un stockage Blob, Fichier et Table Microsoft Azure en utilisant des commandes simples avec des performances optimales. Vous pouvez copier des données entre un système de fichiers et un compte de stockage, ou entre comptes de stockage.  

Il existe deux versions d’AzCopy que vous pouvez télécharger. AzCopy sur Linux est intégré à .NET Core Framework, qui cible les plateformes Linux en offrant des options en ligne de commande de style POSIX. [AzCopy sur Windows](../storage-use-azcopy.md) est intégré à .NET Framework et offre des options en ligne de commande de style Windows. Cet article est consacré à AzCopy sur Linux.

## <a name="download-and-install-azcopy"></a>Téléchargement et installation d’AzCopy
### <a name="installation-on-linux"></a>Installation sur Linux

L’article inclut des commandes pour différentes versions d’Ubuntu.  Utilisez la commande `lsb_release -a` pour confirmer la version et le nom de code de votre distribution. 

AzCopy sur Linux nécessite .NET Core Framework (version 2.0) sur la plateforme. Consultez les instructions d’installation sur la page [.NET Core](https://www.microsoft.com/net/download/linux).

À titre d’exemple, nous allons installer .NET Core sur Ubuntu 16.04. Pour consulter le guide d’installation le plus récent, rendez-vous sur la page d’installation [.NET Core sur Linux](https://www.microsoft.com/net/download/linux).


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.2
```

Une fois que vous avez installé .NET Core, téléchargez et installez AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Vous pouvez supprimer les fichiers extraits une fois qu’AzCopy sur Linux est installé. Si vous n’avez pas de privilèges de super utilisateur, vous pouvez également exécuter AzCopy à l’aide du script d’interpréteur de commandes « azcopy » dans le fichier extrait. 


## <a name="writing-your-first-azcopy-command"></a>Écriture de votre première commande AzCopy
La syntaxe de base d’une commande AzCopy est :

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Les exemples suivants montrent différents scénarios permettant de copier des données vers et à partir de fichiers et blobs Microsoft Azure. Reportez-vous au menu `azcopy --help` pour obtenir une explication détaillée des paramètres utilisés dans chaque exemple.

## <a name="blob-download"></a>Blob : Téléchargement
### <a name="download-single-blob"></a>Télécharger un seul objet blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Si le dossier `/mnt/myfiles` n’existe pas, AzCopy le crée et télécharge `abc.txt ` dans le nouveau dossier. 

### <a name="download-single-blob-from-secondary-region"></a>Télécharger un objet blob unique depuis la région secondaire

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Notez que vous devez avoir un accès en lecture activé pour le stockage géo-redondant.

### <a name="download-all-blobs"></a>Télécharger tous les objets blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Si les objets blob suivants se trouvent dans le conteneur spécifié :  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Après l’opération de téléchargement, le répertoire `/mnt/myfiles` inclut les fichiers suivants :

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Si vous ne spécifiez pas l’option `--recursive`, aucun blob ne sera téléchargé.

### <a name="download-blobs-with-specified-prefix"></a>Téléchargement d’objets blob avec le préfixe spécifié

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Si les objets blob suivants se trouvent dans le conteneur spécifié, Tous les blobs commençant par le préfixe `a` sont téléchargés.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Après l’opération de téléchargement, le dossier `/mnt/myfiles` inclut les fichiers suivants :

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Le préfixe s’applique au répertoire virtuel, qui forme la première partie du nom de l’objet blob. Dans l’exemple ci-dessus, le répertoire virtuel ne correspond pas au préfixe spécifié ; le blob n’est donc pas téléchargé. En outre, si l’option `--recursive` n’est pas spécifiée, AzCopy ne télécharge pas les blobs.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Définition de l’heure de la dernière modification des fichiers exportés pour qu’elle soit identique à celle des objets blob source

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Vous pouvez également exclure des objets blob de l’opération de téléchargement en vous basant sur l’heure de leur dernière modification Par exemple, si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus récente que celle du fichier de destination `--exclude-newer` :

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Ou si vous souhaitez exclure les objets blob dont la dernière heure de modification est identique ou plus ancienne que celle du fichier de destination `--exclude-older` :

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Objet blob : Téléchargement
### <a name="upload-single-file"></a>Télécharger un fichier unique

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Si le conteneur de destination spécifié n’existe pas, AzCopy le crée et y charge le fichier.

### <a name="upload-single-file-to-virtual-directory"></a>Télécharger un fichier unique dans le répertoire virtuel

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Si le répertoire virtuel spécifié n’existe pas, AzCopy charge le fichier pour y inclure le répertoire virtuel dans le nom de blob (*par exemple*, `vd/abc.txt` dans l’exemple ci-dessus).

### <a name="upload-all-files"></a>Télécharger tous les fichiers

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

La spécification de l’option `--recursive` engendre le chargement des contenus du répertoire spécifié vers le stockage Blob récursivement, ce qui implique également la copie de tous les sous-dossiers et de leurs fichiers. Par exemple, si les fichiers suivants se trouvent dans le dossier `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Après l’opération de chargement, le conteneur inclut les fichiers suivants :

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Lorsque l’option `--recursive` n’est pas spécifiée, seuls les trois fichiers suivants sont chargés :

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Télécharger des fichiers correspondant au modèle spécifié

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Si les fichiers suivants se trouvent dans le dossier `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Après l’opération de chargement, le conteneur inclut les fichiers suivants :

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Lorsque l’option `--recursive` n’est pas spécifiée, AzCopy ignore les fichiers qui se trouvent dans les sous-répertoires :

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Spécifier le type de contenu MIME d’un objet blob de destination
Par défaut, AzCopy définit le type de contenu d'un objet blob de destination comme `application/octet-stream`. Toutefois, vous pouvez spécifier explicitement le type de contenu via l’option `--set-content-type [content-type]`. Cette syntaxe définit le type de contenu de tous les objets blob dans une opération de téléchargement.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Si l’option `--set-content-type` est spécifiée sans valeur, AzCopy définit chaque type de contenu de blob ou de fichier en fonction de son extension de fichier.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Objet blob : copie
### <a name="copy-single-blob-within-storage-account"></a>Copie d’un objet blob unique au sein d’un compte de stockage

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Lorsque vous copiez un blob sans l’option --sync-copy, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-single-blob-across-storage-accounts"></a>Copier un objet blob unique sur plusieurs comptes de stockage

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Lorsque vous copiez un blob sans l’option --sync-copy, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copier un objet blob unique de la région secondaire à la région principale

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Notez que vous devez avoir un accès en lecture activé pour le stockage géo-redondant.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copier un objet blob unique et ses captures instantanées sur plusieurs comptes de stockage

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Après l’opération de copie, le conteneur cible inclut le blob et ses captures instantanées. Le conteneur inclut le blob suivant et ses captures instantanées :

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copier des objets blob de façon synchrone dans des comptes de stockage
AzCopy copie par défaut les données entre deux points de terminaison de stockage de façon asynchrone. Par conséquent, l’opération de copie s’exécute en arrière-plan à l’aide de la capacité de bande passante de secours qui n’a aucun SLA pour la rapidité de copie de blob. 

L’option `--sync-copy` garantit que l’opération de copie a une vitesse constante. AzCopy effectue la copie synchrone en téléchargeant les objets blob à copier à partir de la source spécifiée dans la mémoire locale, puis en les téléchargeant sur la destination de stockage d’objets blob.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone. L’approche recommandée consiste à utiliser cette option dans une machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

## <a name="file-download"></a>Fichier : Téléchargement
### <a name="download-single-file"></a>Télécharger un fichier unique

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Si la source spécifiée est un partage de fichier Azure, vous devez soit spécifier le nom de fichier exact, (*par exemple,* `abc.txt`) pour télécharger un fichier unique, soit spécifier l’option `--recursive` pour télécharger tous les fichiers dans le partage de manière récursive. Une erreur se produit si vous tentez de spécifier à la fois un modèle de fichier et l’option `--recursive`.

### <a name="download-all-files"></a>Charger tous les fichiers

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Notez que les dossiers vides ne sont pas téléchargés.

## <a name="file-upload"></a>Fichier : Télécharger
### <a name="upload-single-file"></a>Télécharger un fichier unique

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Télécharger tous les fichiers

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Notez que les dossiers vides ne sont pas chargés.

### <a name="upload-files-matching-specified-pattern"></a>Télécharger des fichiers correspondant au modèle spécifié

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Fichier : Copier
### <a name="copy-across-file-shares"></a>Copier d’un partage de fichier à l’autre

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Lorsque vous copiez un fichier sur plusieurs partage de fichiers, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-from-file-share-to-blob"></a>Copier d’un partage de fichiers vers un objet blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Lorsque vous copiez un fichier d’un partage de fichiers vers un objet blob, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="copy-from-blob-to-file-share"></a>Copier d’un objet blob vers le partage de fichiers

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Lorsque vous copiez un fichier d’un objet blob vers un partage de fichiers, une opération de [copie côté serveur](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) est exécutée.

### <a name="synchronously-copy-files"></a>Copier les fichiers de façon synchrone
Vous pouvez spécifier l’option `--sync-copy` pour copier des données d’un stockage Fichier à un autre stockage Fichier, d’un stockage Fichier à un stockage Blob et d’un stockage Blob à un stockage Fichier de façon synchrone. AzCopy exécute cette opération en téléchargeant les données sources dans la mémoire locale, puis en les chargeant dans la destination. Dans ce cas, des coûts de sortie standard s’appliquent.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Pendant la copie depuis le stockage de fichier vers le stockage d'objets Blob, le type d'objet Blob par défaut est l'objet Blob de blocs. L'utilisateur peut spécifier l'option `--blob-type page` pour modifier le type d'objet Blob de destination. Les types disponibles sont `page | block | append`.

Notez que `--sync-copy` peut générer des coûts de sortie supplémentaires par rapport à la copie asynchrone. L’approche recommandée consiste à utiliser cette option dans une machine virtuelle Azure qui se trouve dans la même région que votre compte de stockage source afin d’éviter les frais de sortie.

## <a name="other-azcopy-features"></a>Autres fonctionnalités AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Copier uniquement les données qui n’existent pas dans la destination
Les paramètres `--exclude-older` et `--exclude-newer` vous permettent d’exclure les ressources source plus anciennes ou plus récentes d’être copiées respectivement. Si vous souhaitez copier uniquement des ressources de code source qui n’existent pas dans la destination, vous pouvez spécifier les deux paramètres dans la commande AzCopy :

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Utilisation d’un fichier config pour spécifier les paramètres en ligne de commande

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Vous pouvez inclure n’importe quels paramètres en ligne de commande AzCopy dans un fichier config. AzCopy traite les paramètres du fichier comme s’ils avaient été spécifiés sur la ligne de commande, réalisant une substitution directe avec les contenus du fichier.

Imaginez un fichier config nommé `copyoperation`, qui contient les lignes suivantes. Chaque paramètre AzCopy peut être spécifié sur une seule ligne.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

ou sur des lignes distinctes :

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy échoue si vous écrivez le paramètre sur deux lignes, comme démontré ici pour le paramètre `--source-key` :

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Spécification d’une signature d’accès partagé (SAP)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Vous pouvez également spécifier une SAP sur l’URI du conteneur :

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Dossier du fichier journal
Chaque fois que vous émettez une commande sur AzCopy, il vérifie si un fichier journal existe dans le dossier par défaut ou dans un dossier que vous avez spécifié via cette option. Si le fichier journal n’existe à aucun de ces emplacements, AzCopy considère l’opération comme nouvelle et génère un nouveau fichier journal.

Si le fichier journal existe, AzCopy vérifie si la ligne de commande que vous entrez correspond à la ligne de commande du fichier journal. Si les deux lignes de commande correspondent, AzCopy reprend l’opération incomplète. Si elles ne correspondent pas, AzCopy invite l’utilisateur à remplacer le fichier journal pour démarrer une nouvelle opération ou à annuler l’opération actuelle.

Si vous souhaitez utiliser l’emplacement par défaut pour le fichier journal :

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Si vous omettez l'option `--resume`, ou spécifiez l'option `--resume` sans le chemin du dossier, comme démontré ci-dessus, AzCopy crée le fichier journal à l'emplacement par défaut, qui est `~\Microsoft\Azure\AzCopy`. Si le fichier journal existe déjà, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez spécifier un emplacement personnalisé pour le fichier journal :

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Cet exemple crée le fichier journal s’il n’existe pas déjà. S’il existe, AzCopy reprend l’opération en se basant sur le fichier journal.

Si vous souhaitez reprendre une opération AzCopy, répétez la même commande. AzCopy sur Linux vous invitera à confirmer l’opération :

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Journaux détaillés de sortie

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Spécification du nombre d’opérations simultanées pour démarrer
L'option `--parallel-level` spécifie le nombre d'opérations de copie simultanées. Par défaut, AzCopy lance un certain nombre d’opérations simultanées pour augmenter la vitesse de transfert des données. Le nombre d’opérations simultanées est égal au nombre de processeurs dont vous disposez, multiplié par huit. Si vous exécutez AzCopy sur un réseau à faible bande passante, vous pouvez spécifier un nombre inférieur pour --parallel-level afin d’éviter l’échec causé par l’utilisation simultanée des ressources.

>[!TIP]
>Pour afficher la liste complète des paramètres AzCopy, consultez le menu « azcopy --help ».

## <a name="known-issues-and-best-practices"></a>Problèmes connus et meilleures pratiques
### <a name="error-net-sdk-20-is-not-found-in-the-system"></a>Erreur : SDK .NET 2.0 introuvable dans le système.
AzCopy varie selon le SDK .NET 2.0 à partir de la version 7.0 de AzCopy. Avant cette version, AzCopy utilisait .NET Core 1.1. Si vous rencontrez une erreur indiquant que .NET Core 2.0 n’est pas installé dans le système, vous devez peut-être l’installer ou le mettre à niveau à l’aide des [Instructions d’installation de .NET Core](https://www.microsoft.com/net/learn/get-started/linuxredhat).

### <a name="error-installing-azcopy"></a>Erreur lors de l’installation d’AzCopy
Si vous rencontrez des problèmes avec l’installation d’AzCopy, vous pouvez essayer d’exécuter AzCopy à l’aide du script bash dans le dossier `azcopy` extrait.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitation des écritures simultanées lors de la copie des données
Lorsque vous copiez des objets blob ou des fichiers avec AzCopy, gardez en tête qu’une autre application peut être en train de modifier les données pendant que vous les copiez. Si possible, assurez-vous que les données que vous copiez ne sont pas modifiées pendant l’opération de copie. Par exemple, lorsque vous copiez un disque dur virtuel (VHD) associé à une machine virtuelle Azure, assurez-vous qu’aucune autre application n’est en train d’écrire sur le disque VHD. Un bon moyen pour ce faire consiste à louer la ressource à copier. Sinon, vous pouvez commencer par créer une capture instantanée du disque VHD et copier ensuite la capture instantanée.

Si vous ne pouvez pas empêcher d’autres applications d’écrire sur les objets blob ou les fichiers pendant qu’ils sont copiés, gardez en tête qu’au moment où la tâche sera terminée, les ressources copiées n’auront peut-être plus une parité complète avec les ressources source.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Exécuter une instance de AzCopy sur un même ordinateur.
AzCopy est conçu pour optimiser l'utilisation de votre ressource de l'ordinateur afin d’accélérer le transfert de données. Nous vous recommandons d'exécuter une seule instance de AzCopy sur un même ordinateur et de spécifier l'option `--parallel-level` si vous avez besoin de plus d'opérations simultanées. Pour plus d'informations, tapez `AzCopy --help parallel-level` dans la ligne de commande.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le stockage Azure et AzCopy, consultez les ressources suivantes :

### <a name="azure-storage-documentation"></a>Documentation d’Azure Storage :
* [Introduction à Azure Storage](../storage-introduction.md)
* [Créez un compte de stockage](../storage-create-storage-account.md)
* [Gérer les ressources de stockage Blob Azure avec l’explorateur de stockage (préversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Utilisation d’Azure CLI 2.0 avec le stockage Azure](../storage-azure-cli.md)
* [Utilisation du stockage d’objets blob à partir de C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Utilisation du stockage d'objets blob à partir de Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Utilisation du stockage d'objets blob à partir de Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Utilisation du stockage d'objets blob à partir de Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Billets de blog Azure Storage :
* [Announcing AzCopy on Linux Preview](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/) (Annonce de la préversion d’AzCopy sur Linux)
* [Présentation de la bibliothèque de déplacement des données dans Azure Storage en version préliminaire](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy : Présentation de la copie synchrone et du type de contenu personnalisé](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy : Annonce de la disponibilité générale d'AzCopy 3.0 plus version préliminaire d'AzCopy 4.0 avec prise en charge de fichier et de table](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy : Optimisation pour les scénarios de copie à grande échelle](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy : Prise en charge des comptes de stockage géo-redondants avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy – Transfer data with re-startable mode and SAS Token](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx) (AzCopy : Transfert des données avec mode reprise et jeton SAP)
* [AzCopy : Utilisation de copie d'objets blob sur plusieurs comptes](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy : Chargement/téléchargement des fichiers pour les objets blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

