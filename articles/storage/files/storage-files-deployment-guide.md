---
title: "Comment déployer Azure Files | Microsoft Docs"
description: "Découvrez comment déployer Azure Files du début à la fin."
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
ms.openlocfilehash: c33639723657d3c2875ed9607a887775d558be16
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-deploy-azure-files"></a>Comment déployer Azure Files
[Azure Files](storage-files-introduction.md) offre des partages de fichiers entièrement gérés dans le cloud, accessibles via le protocole SMB standard. Cet article explique comment déployer pratiquement Azure Files au sein de votre organisation.

Avant de suivre les étapes décrites dans cet article, nous recommandons vivement de lire l’article [Planification d’un déploiement d’Azure Files](storage-files-planning.md).

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous avez déjà accompli les étapes suivantes :

- Créé un compte de stockage Azure avec les options de chiffrement et de résilience, et dans la région de votre choix. Pour obtenir des instructions pas à pas concernant la création d’un compte de stockage, voir [Créer un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- Créé un partage de fichiers Azure avec le quota de votre choix dans votre compte de stockage. Pour obtenir des instructions pas à pas concernant la création d’un partage de fichiers, voir [Créer un partage de fichiers](storage-how-to-create-file-share.md).

## <a name="transfer-data-into-azure-files"></a>Transférer des données dans des fichiers Azure
Vous pouvez migrer des partages de fichiers existants tels que ceux stockés localement vers votre nouveau partage de fichiers Azure. Cette section montre comment déplacer des données dans un partage de fichiers Azure à l’aide de plusieurs méthodes populaires détaillées dans le [guide de planification](storage-files-planning.md#data-transfer-method).

### <a name="azure-file-sync-preview"></a>Synchronisation de fichiers Azure (préversion)
Azure File Sync (préversion) vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Pour cela, elle transforme vos serveurs Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible sur Windows Server pour accéder à vos données localement (y compris SMB, NFS et FTPS) et vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Vous pouvez utiliser la synchronisation de fichiers Azure pour migrer des données dans un partage de fichiers Azure, même si le mécanisme de synchronisation n’est pas souhaité pour une utilisation à long terme. Pour plus d’informations sur l’utilisation de la synchronisation de fichiers Azure pour transférer des données vers un partage de fichiers Azure, consultez [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md) et [Déploiement de la synchronisation de fichiers Azure](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Le service Azure Import/Export permet de transférer en toute sécurité des volumes importants de données dans un partage de fichiers Azure en expédiant des disques durs vers un centre de données Azure. Pour obtenir une présentation plus détaillée du service, voir [Transférer des données vers Stockage Azure à l’aide du service Microsoft Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!Note]  
> Le service Azure Import/Export ne prend pas en charge actuellement l’exportation de fichiers à partir d’un partage de fichiers Azure.

Les étapes suivantes montrent comment importer des données d’un emplacement local dans un partage de fichiers Azure.

1. Procurez-vous le nombre requis de disques durs à envoyer à Azure. Les disques durs peuvent être de toute capacité, mais il doit s’agir de disques SSD ou HDD de 2,5 ou 3,5 pouces prenant en charge la norme SATA II ou SATA III. 

2. Connectez et montez chaque disque sur le serveur/PC effectuant le transfert de données. Pour des performances optimales, nous vous recommandons d’exécuter la tâche d’exportation locale sur le serveur contenant les données. Dans certains cas, par exemple lorsque le serveur de fichiers est un appareil NAS, cela n’est pas possible. Il est alors parfaitement acceptable de monter chaque disque sur un PC.

3. Vérifiez que chaque lecteur est en ligne, initialisé et associé à une lettre de lecteur. Pour mettre en ligne un lecteur, l’initialiser et lui attribuer une lettre de lecteur, ouvrez le composant logiciel enfichable MMC Gestion des disques (diskmgmt.msc).

    - Pour mettre en ligne un disque (s’il ne l’est pas encore), cliquez dessus avec le bouton droit dans le volet inférieur du composant logiciel enfichable MMC Gestion des disques, puis sélectionnez « En ligne ».
    - Pour initialiser un disque (une fois que celui-ci est en ligne), cliquez dessus avec le bouton droit dans le volet inférieur, puis sélectionnez « Initialiser ». Veillez à sélectionner « GPT » lorsque vous y êtes invité.

        ![Capture d’écran du menu Initialiser le disque dans le composant logiciel enfichable MMC Gestion des disques](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Pour attribuer une lettre de lecteur au disque, cliquez avec le bouton droit sur l’espace « non alloué » du disque en ligne et initialisé, puis cliquez sur « Nouveau volume simple ». Cela vous permet d’attribuer une lettre de lecteur. Notez que vous n’avez pas besoin de formater le volume, car cette opération sera effectuée ultérieurement.

        ![Capture d’écran de l’Assistant Création d’un volume simple dans le composant logiciel enfichable MMC Gestion des disques](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Créez le fichier CSV du jeu de données. Le fichier CSV du jeu de données est un mappage entre le chemin d’accès des données locales et le partage de fichiers Azure souhaité vers lequel les données doivent être copiées. Par exemple, le fichier CSV du jeu de données suivant mappe un partage de fichiers local (« F:\shares\scratch ») vers un partage de fichiers Azure (« MyAzureFileShare ») :
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Plusieurs partages peuvent être spécifiés avec un compte de stockage. Pour plus d’informations, voir [Préparer le fichier CSV du jeu de données](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file).

5. Créez le fichier CSV du jeu de lecteurs. Le fichier CSV du jeu de lecteurs répertorie les disques disponibles pour l’agent d’exportation local. Par exemple, le fichier CSV du jeu de lecteurs suivant répertorie les lecteurs `X:`, `Y:` et `Z:` à utiliser dans le cadre de la tâche d’exportation locale :

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Pour plus d’informations, voir [Préparer le fichier CSV du jeu de lecteurs](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file).

6. Utilisez l’outil [WAImportExport](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) pour copier vos données sur un ou plusieurs disques durs.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ne modifiez ni les données ni le fichier journal après la préparation du disque.

7. [Créez une tâche d’importation](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-an-export-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy est un outil de copie bien connu fourni avec Windows et Windows Server. Robocopy peut servir à transférer des données dans Azure Files en montant le partage de fichiers localement, puis en utilisant l’emplacement monté comme destination de la commande Robocopy. L’utilisation de Robocopy est très simple :

1. [Montez votre partage de fichiers Azure](storage-how-to-use-files-windows.md). Pour des performances optimales, nous recommandons de monter le partage de fichiers Azure localement sur le serveur contenant les données. Dans certains cas, par exemple lorsque le serveur de fichiers est un appareil NAS, cela n’est pas possible. Il est alors parfaitement acceptable de monter le partage de fichiers Azure sur un PC. Dans cet exemple, `net use` est utilisé dans la ligne de commande pour monter le partage de fichiers :

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Utilisez `robocopy` dans la ligne de commande pour déplacer les données vers le partage de fichiers Azure :

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy offre un nombre significatif d’options pour modifier le comportement de copie. Pour plus d’informations, voir la page du manuel [Robocopy](https://technet.microsoft.com/library/cc733145.aspx).

### <a name="azcopy"></a>AzCopy
AzCopy est un utilitaire de ligne de commande conçu pour copier des données à destination et à partir d’Azure Files, ou d’un stockage blob Azure, en utilisant des commandes simples avec des performances optimales. L’utilisation d’AzCopy est simple :

1. Téléchargez la [dernière version d’AzCopy sur Windows](http://aka.ms/downloadazcopy) ou [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy).
2. Utilisez `azcopy` dans la ligne de commande pour déplacer les données vers le partage de fichiers Azure. La syntaxe sous Windows est la suivante : 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Sous Linux, la syntaxe de la commande est un peu différente :

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy offre un nombre significatif d’options pour modifier le comportement de copie. Pour plus d’informations, voir [AzCopy sur Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) et [AzCopy sur Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Monter automatiquement sur les PC/serveurs nécessaires
Pour remplacer un partage de fichiers local, il est utile de pré-monter les partages sur les machines sur lesquelles il doit être utilisé. Cela peut être fait automatiquement sur une liste de machines.

> [!Note]  
> Le montage d’un partage de fichiers Azure nécessitant l’utilisation de la clé du compte de stockage en guise de mot de passe, nous recommandons d’effectuer le montage uniquement dans un environnements approuvé. 

### <a name="windows"></a>Windows
PowerShell peut être utilisé pour exécuter la commande de montage sur plusieurs ordinateurs. Dans l’exemple suivant, `$computers` est entré manuellement, mais vous pouvez générer la liste des ordinateurs à monter automatiquement. Par exemple, vous pouvez entrer cette variable avec des résultats d’Active Directory.

```PowerShell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Un simple script bash combiné avec SSH peut produire le même résultat dans l’exemple suivant. De même, la variable `$computer` est laissée pour être entrée par l’utilisateur :

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Étapes suivantes
- [Planifier un déploiement de la fonctionnalité Synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Résoudre les problèmes d’Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Résoudre les problèmes d’Azure Files sous Linux](storage-troubleshoot-linux-file-connection-problems.md)