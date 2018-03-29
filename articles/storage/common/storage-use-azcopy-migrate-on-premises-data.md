---
title: Migrer des données locales vers le Stockage Azure à l’aide d’AzCopy | Microsoft Docs
description: Utilisez AzCopy pour migrer ou copier des données vers ou depuis un blob, une table ou un fichier. Migrez facilement des données de votre stockage local vers le Stockage Azure.
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.openlocfilehash: 1e7292cf4d647b38a6fe8ceb270ba161e548a537
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migrer des données locales vers un stockage cloud à l’aide d’AzCopy

AzCopy est un outil en ligne de commande qui vous permet de copier des données vers ou depuis le Stockage Blob Azure, Azure Files ou le Stockage Table Azure, à l’aide de commandes simples. Les commandes sont conçues pour offrir des performances optimales. Vous pouvez copier des données entre un système de fichiers et un compte de stockage, ou d’un compte de stockage à un autre.  

Vous pouvez télécharger deux versions d’AzCopy :

* [AzCopy sur Linux](storage-use-azcopy.md) est basé sur .NET Core Framework. Il cible les plateformes Linux en fournissant des options de ligne de commande de style POSIX. 
* [AzCopy sur Windows](../storage-use-azcopy.md) est basé sur .NET Framework. Il fournit des options de ligne de commande de style Windows. 
 
Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un compte de stockage. 
> * Utilisez AzCopy pour charger toutes vos données.
> * Modifiez les données pour les besoins du test.
> * Créez une tâche planifiée ou un travail Cron pour identifier les nouveaux fichiers à charger.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel, téléchargez la dernière version d’AzCopy sur [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) ou [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Pour pouvoir télécharger des objets blob d’une région secondaire sur votre stockage local, et inversement, définissez **Réplication** sur **Stockage géoredondant avec accès en lecture**. Cette option crée un compte de [stockage géoredondant](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Créez un conteneur.

Les objets blob sont toujours chargés dans un conteneur. Vous pouvez utiliser des conteneurs pour organiser les groupes d’objets blob comme vous organisez vos fichiers dans des dossiers sur votre ordinateur. 

Pour créer un conteneur, effectuez les étapes suivantes :

1. Sélectionnez le bouton **Comptes de stockage** dans la page principale, puis sélectionnez le compte de stockage que vous avez créé.
2. Sélectionnez **Objets blob** sous **Services**, puis sélectionnez **Conteneur**. 

   ![Créez un conteneur.](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Les noms de conteneur doivent commencer par une lettre ou un chiffre. Ils peuvent contenir uniquement des lettres, des chiffres et des traits d’union (-). Pour prendre connaissance des autres règles de nommage des objets blob et des conteneurs, consultez [Nommage et référencement des conteneurs, objets blob et métadonnées](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Charger tous les fichiers d’un dossier dans le Stockage Blob

Vous pouvez utiliser AzCopy pour charger tous les fichiers d’un dossier dans le Stockage Blob sur [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Pour charger tous les objets blob d’un dossier, entrez la commande AzCopy suivante :

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Remplacez `<key>` et `key` par votre clé de compte. Dans le portail Azure, vous pouvez récupérer votre clé de compte en sélectionnant **Clés d’accès** sous **Paramètres** dans votre compte de stockage. Sélectionnez une clé et collez-la dans la commande AzCopy. Si le conteneur de destination spécifié n’existe pas, AzCopy le crée et y charge le fichier. Mettez à jour le chemin source en indiquant votre répertoire de données, puis remplacez **myaccount** dans l’URL de destination par le nom de votre compte de stockage.

Pour charger le contenu du répertoire spécifié dans le Stockage Blob de manière récursive, spécifiez l’option `--recursive` (Linux) ou `/S` (Windows). Quand vous exécutez AzCopy avec l’une de ces options, tous les sous-dossiers et leurs fichiers sont également chargés.

## <a name="upload-modified-files-to-blob-storage"></a>Charger les fichiers modifiés dans le Stockage Blob
Vous pouvez utiliser AzCopy pour [charger des fichiers](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) en fonction de l’heure de leur dernière modification. Pour les besoins du test, modifiez des fichiers existants ou créez-en d’autres dans votre répertoire source. Pour charger uniquement les fichiers nouveaux ou mis à jour, ajoutez le paramètre `--exclude-older` (Linux) ou `/XO` (Windows) à la commande AzCopy.

Si vous souhaitez copier uniquement les ressources de code source qui n’existent pas dans la destination, spécifiez les deux paramètres `--exclude-older` et `--exclude-newer` (Linux) ou les deux paramètres `/XO` et `/XN` (Windows) dans la commande AzCopy. AzCopy charge uniquement les données mises à jour, en se basant sur leur horodatage.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Créer une tâche planifiée ou un travail Cron 
Vous pouvez créer une tâche planifiée ou un travail Cron qui exécute un script de commande AzCopy. Le script identifie et charge les nouvelles données locales dans le stockage cloud selon un intervalle de temps spécifique. 

Copiez la commande AzCopy dans un éditeur de texte. Mettez à jour les valeurs de paramètre de la commande AzCopy avec les valeurs appropriées. Enregistrez le fichier sous `script.sh` (Linux) ou `script.bat` (Windows) pour AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy s’exécute en mode détaillé avec l’option `--verbose` (Linux) ou `/V` (Windows). La sortie est redirigée dans un fichier journal. 

Dans ce didacticiel, [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) est utilisé pour créer une tâche planifiée sur Windows. La commande [Crontab](http://crontab.org/) est utilisée pour créer un travail Cron sur Linux. 
 Avec **Schtasks**, un administrateur peut créer, supprimer, interroger, modifier, exécuter et terminer des tâches planifiées sur un ordinateur local ou distant. Avec **Cron**, les utilisateurs sur Linux et Unix peuvent exécuter des commandes ou des scripts à la date et à l’heure qu’ils ont spécifiées à l’aide [d’expressions Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Pour créer un travail Cron sur Linux, entrez la commande suivante sur un terminal : 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

La spécification de l’expression Cron `*/5 * * * * ` dans la commande indique que le script d’interpréteur de commandes `script.sh` doit s’exécuter toutes les cinq minutes. Vous pouvez planifier le script pour qu’il s’exécute à une heure précise tous les jours, tous les mois ou tous les ans. Pour en savoir plus sur la définition de la date et l’heure d’exécution d’un travail, consultez la page sur les [expressions Cron](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Pour créer une tâche planifiée sur Windows, entrez la commande suivante à une invite de commandes ou dans PowerShell :

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

La commande utilise les paramètres suivants :
- `/SC` : spécifie une planification en minutes.
- `/MO` : spécifie un intervalle de temps de cinq minutes.
- `/TN` : spécifie le nom de la tâche.
- `/TR` : spécifie le chemin du fichier `script.bat`. 

Pour en savoir plus sur la création d’une tâche planifiée sur Windows, consultez [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Pour vérifier que la tâche planifiée ou le travail Cron s’exécute correctement, créez des fichiers de test dans votre répertoire `myfolder`. Attendez cinq minutes pour laisser le temps aux nouveaux fichiers de se charger dans votre compte de stockage. Accédez à votre répertoire des journaux pour afficher les journaux de sortie de la tâche planifiée ou du travail Cron. 

Pour en savoir plus sur les façons de déplacer des données locales vers le Stockage Azure, et inversement, consultez [Déplacer des données vers et depuis le Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le stockage Azure et AzCopy, consultez les ressources suivantes :

* [Introduction à Azure Storage](../storage-introduction.md)
* [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) 
* [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md) 
* [Créez un compte de stockage](../storage-create-storage-account.md)
* [Gérer les ressources de stockage Blob Azure avec l’explorateur de stockage (préversion)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

