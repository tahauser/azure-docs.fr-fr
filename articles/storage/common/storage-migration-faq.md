---
title: Forum aux questions sur la migration de Stockage Azure | Microsoft Docs
description: "Réponses aux questions fréquemment posées concernant la migration de Stockage Azure"
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Questions fréquemment posées (FAQ) sur la migration de Stockage Azure

Cet article répond aux questions courantes sur la migration de Stockage Azure. 

## <a name="faq"></a>Forum Aux Questions

**Comment créer un script pour copier des fichiers d'un conteneur à un autre ?**

Pour copier des fichiers entre des conteneurs, vous utilisez AzCopy. Voir l’exemple suivant :

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy utilise l'[API copie d'objet blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) pour copier chaque fichier dans le conteneur.  
  
Vous pouvez utiliser n'importe quelle machine virtuelle ou machine locale disposant d'un accès Internet pour exécuter AzCopy. Vous pouvez également utiliser Azure Batch Schedule pour effectuer automatiquement cette opération, mais le processus est plus compliqué.  
  
Le script Automation est conçu pour le déploiement d'Azure Resource Manager plutôt que pour la manipulation du contenu de stockage. Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Des frais sont-ils facturés pour la copie de données entre deux différents partages de fichiers sur un même compte de stockage dans la même région ?**

Non. Ce processus n'entraîne aucuns frais.

**Comment sauvegarder la totalité de mon compte de stockage sur un autre compte de stockage ?**

Il n'existe aucune option permettant de sauvegarder directement la totalité d'un compte de stockage. Mais vous pouvez déplacer manuellement le conteneur de ce compte de stockage vers un autre compte en utilisant AzCopy ou l'Explorateur de stockage. Les étapes suivantes montrent comment utiliser AzCopy pour déplacer le conteneur:  
 

1.  Installez l'outil de ligne de commande [AzCopy](storage-use-azcopy.md). Cet outil vous aide à déplacer le fichier VHD entre des comptes de stockage.

2.  Après avoir installé AzCopy sur Windows à l'aide du programme d'installation, ouvrez une fenêtre d'invite de commande, puis accédez au dossier d'installation AzCopy sur votre ordinateur. Par défaut, AzCopy est installé dans **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   Exécutez la commande suivante pour déplacer le conteneur. Vous devez remplacer le texte par la valeur réelle.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - /source : fournir l'URI du compte de stockage source (jusqu'au conteneur)  
        - /dest : fournir l'URI du compte de stockage cible (jusqu'au conteneur)  
        - /sourcekey : fournir la clé primaire du compte de stockage source ; vous pouvez copier cette clé à partir du portail en sélectionnant le compte de stockage.  
        - /destkey : fournir la clé primaire du compte de stockage cible ; vous pouvez copier cette clé à partir du portail en sélectionnant le compte de stockage.

Après avoir exécuté cette commande, les fichiers du conteneur sont déplacés vers le compte de stockage cible.

**L'interface de ligne de commande AzCopy ne fonctionne pas avec le commutateur « Pattern » lorsque vous effectuez une copie d'un objet blob Azure à un autre.**

Vous pouvez directement copier et modifier la commande AzCopy, puis effectuer une vérification croisée pour vous assurer que le modèle correspond à la source. Vérifiez que vous utilisez les caractères génériques **/S**. Pour plus d’informations, voir [Paramètres AzCopy](storage-use-azcopy.md).

**Comment copier les données d'un conteneur de stockage à un autre ?**

Pour ce faire, procédez comme suit :

1.  Créez le conteneur (dossier) dans l'objet blob de destination.

2.  Utilisez [Azcopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) pour copier le contenu du conteneur d'objets blob d'origine vers un autre conteneur d'objets blob.

**Comment créer un script PowerShell pour déplacer les données d'un partage de fichiers Azure vers un autre stockage Azure ?**

Utilisez AzCopy pour déplacer les données d'un partage de fichiers Azure vers un autre stockage Azure. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment charger des fichiers .csv volumineux dans Stockage Azure ?**

Utilisez AzCopy pour charger des fichiers .csv volumineux dans Stockage Azure. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Je dois chaque jour déplacer les journaux du « lecteur D » vers mon compte de stockage Azure. Comment automatiser ce processus ?**

Vous pouvez utiliser AzCopy et créer une tâche dans le Planificateur de tâches. Chargez les fichiers vers un compte de stockage Azure à l'aide d'un script de commandes par lot AzCopy. Pour plus d’informations, consultez [Comment configurer et exécuter des tâches de démarrage pour un service cloud](../../cloud-services/cloud-services-startup-tasks.md).

**Comment déplacer mon compte de stockage entre des abonnements ?**

Utilisez AzCopy pour déplacer votre compte de stockage entre des abonnements. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment déplacer environ 10 To de données vers un stockage dans une autre région ?**

Utilisez AzCopy pour déplacer les données. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment copier des données sur site vers Stockage Azure ?**

Utilisez AzCopy pour copier les données. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment déplacer des données sur site vers Azure File Service ?**

Utilisez AzCopy pour déplacer les données. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment mapper un dossier de conteneur sur une machine virtuelle ?**

Utilisez le partage de fichiers Azure.

**Comment sauvegarder un stockage de fichiers Azure ?**

Il n'existe pas de solution de sauvegarde. Mais Azure Files prend en charge la copie asynchrone. Vous pouvez ainsi copier des fichiers d'un partage vers un autre partage (dans le même compte de stockage ou vers un autre compte de stockage) ou vers un conteneur d'objets blob (dans le même compte de stockage ou dans un autre compte de stockage).
Pour plus d’informations, Voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md).

**Comment déplacer des disques managés vers un autre compte de stockage ?**

Pour ce faire, procédez comme suit :

1.  Arrêtez la machine virtuelle à laquelle le disque managé est attaché.

2.  Copiez le disque dur virtuel managé d'une zone à une autre en exécutant le script Azure PowerShell suivant :

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Créez un disque managé en utilisant le fichier VHD dans l'autre région dans laquelle vous avez copié le disque dur virtuel. Pour ce faire, utilisez le script Azure PowerShell suivant :  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Pour plus d'informations sur le déploiement d'une machine virtuelle à partir d'un disque managé, voir [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Comment télécharger environ 1 à 2 To de données à partir du portail Azure ?**

Utilisez AzCopy pour télécharger les données. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).

**Comment changer l'emplacement secondaire par une région en Europe pour un compte de stockage ?**

Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La sélection de la région secondaire dépend de la région primaire et ne peut pas être modifiée. Voir [Réplication Azure Storage](storage-redundancy.md).

**Où puis-je obtenir plus d'informations sur le chiffrement du service de stockage Azure (SSE) ?**  
  
Consultez les articles suivants :

-  [Guide de sécurité du Stockage Azure](storage-security-guide.md)

-   [Azure Storage Service Encryption pour les données au repos](storage-service-encryption.md)

**Comment déplacer ou télécharger des données à partir d'un compte de stockage ?**

Utilisez AzCopy pour télécharger les données. Pour plus d'informations, voir [Transférer des données avec AzCopy sur Windows](storage-use-azcopy.md) et [Transférer des données avec AzCopy sur Linux](storage-use-azcopy-linux.md).


**Comment chiffrer des données dans un compte de stockage ?**

Après avoir activé le chiffrement dans le compte de stockage, les données existantes ne sont pas chiffrées. Pour chiffrer les données existantes, vous devez télécharger à nouveau les données sur le compte de stockage.  Pour ce faire, procédez comme suit :

Utilisez AzCopy pour copier les données dans un autre compte de stockage, puis revenez à un compte de stockage. Vous pouvez également utiliser l'option [Chiffrement au repos](storage-service-encryption.md).

**Comment télécharger un disque dur virtuel sur une machine locale, autrement qu'en utilisant l'option de téléchargement disponible sur le portail ?**

Vous pouvez utiliser l['Explorateur de stockage](https://azure.microsoft.com/features/storage-explorer/) pour télécharger un disque dur virtuel.

**Existe-t-il des conditions préalables pour modifier la réplication d'un compte de stockage de GRS à LRS ?**

Non. 

**Comment accéder au stockage redondant Azure Files ?**

Read-Access Geo-Redundant Storage (RA-GRS) est requis pour accéder au stockage redondant. Cependant, Azure Files ne prend en charge que les systèmes LRS et les GRS standard qui n'autorisent pas l'accès en lecture seule. 

**Comment passer du stockage Premium au stockage Standard ?**

Pour ce faire, procédez comme suit :

1.  Créez un compte de stockage Standard (ou utilisez un compte de stockage Standard existant dans votre abonnement).

2.  Téléchargez AzCopy. Exécutez l’une des commandes AzCopy suivantes.
      
    Pour copier des disques entiers dans le compte de stockage :

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Pour copier un seul disque, indiquez le nom du disque dans le modèle

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
L'opération peut prendre plusieurs heures.

Pour vous assurer que le transfert s'est déroulé correctement, examinez le conteneur du compte de stockage de destination dans le portail Azure. Une fois les disques copiés dans le compte de stockage standard, vous pouvez les attacher à la machine virtuelle comme un disque existant. Pour plus d’informations, voir [Attachement d’un disque de données géré à une machine virtuelle Windows dans le portail Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Comment convertir en stockage Premium avec un partage de fichiers ?**

Le stockage Premium n'est pas autorisé avec Partage de fichiers Azure.

**Comment passer d'un stockage Standard à un compte de stockage Premium ? Comment passer d'un stockage Premium à un compte de stockage Standard ?**

- Vous devez créer le compte de stockage de destination, copier les données du compte source vers le compte de destination, puis supprimer le compte source.

- Vous pouvez utiliser un outil comme AzCopy pour effectuer la copie des données.

- Si vous utilisez également des machines virtuelles, vous devez effectuer plusieurs étapes supplémentaires avant de migrer les données du compte de stockage. Pour plus d'informations, voir [Migration vers le stockage Azure Premium (disques non gérés)](storage-migration-to-premium-storage.md).

**Comment passer d'un compte de stockage classique vers un compte de stockage Azure Resource Manager ?**

1.  Vous pouvez utiliser l'applet de commande Move-AzureStorageAccount.

2.  Cette applet de commande comporte plusieurs étapes (Valider, Préparer, Exécuter) et vous pouvez valider le déplacement avant de l'exécuter.

3.  Si vous utilisez également des machines virtuelles, vous devez effectuer plusieurs étapes supplémentaires avant de migrer les données du compte de stockage. Pour plus d'informations, voir [Migration de ressources IaaS d’un environnement Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Comment télécharger des données sur un ordinateur Linux à partir d'un compte de stockage Azure, ou charger des données à partir d'une machine Linux ?**

Vous pouvez utiliser l’interface de ligne de commande Azure.

-   Télécharger un seul objet blob

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Charger un seul objet blob : 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Comment autoriser d'autres personnes à accéder à mes ressources de stockage ?**

Pour autoriser d'autres personnes à accéder aux ressources de stockage :

-   Utilisez un jeton avec signature d'accès partagé SAS (Shared Access Signature) pour fournir l'accès à une ressource. 

-   Fournissez à un utilisateur la clé primaire ou secondaire du compte de stockage. Pour plus d’informations, voir [Gérer votre compte de stockage](storage-create-storage-account.md#manage-your-storage-account).

-   Modifiez la stratégie d'accès pour autoriser un accès anonyme. Pour plus d'informations, voir [Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Où AzCopy est-il installé ?**

-   Si vous accédez à AzCopy à partir de la ligne de commande « Microsoft Azure Storage », saisissez 'AzCopy'. La ligne de commande est installée avec AzCopy.

-   Si vous installez la version 32 bits, AzCopy sera installé ici : **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy.**

-   Si vous installez la version 64 bits, AzCopy sera installé ici : **%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**.

**Pour un compte de stockage répliqué (par exemple ZRS, GRS ou RA-GRS), comment accéder aux données stockées dans la région secondaire ?**

-   Si vous utilisez le stockage ZRS (Zone-Redundant Storage) ou GRS (Geo-Redundant Storage), vous ne pouvez pas accéder aux données de la région secondaire à moins qu'un basculement ne se produise. Pour plus d'informations sur le processus de basculement, voir [Que se passe-t-il en cas de basculement d’Azure Storage ?](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs)

-   Si vous utilisez le stockage Read-Access Geo-Redundant Storage (**RA-GRS**), vous pouvez accéder à tout moment aux données de la région secondaire. Pour ce faire, utilisez l’une des méthodes suivantes :  
      
    AzCopy : ajoutez '-secondary' au nom du compte de stockage dans l'URL pour accéder au point de terminaison secondaire. Par exemple :  
     
    https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    Jeton SAS : utilisez un jeton SAS pour accéder aux données du point de terminaison. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

**Comment je utiliser un domaine personnalisé HTTPS avec mon compte de stockage ? Par exemple, comment faire apparaître « https://mystorageaccountname.blob.core.windows.net/images/image.gif » sous la forme « https://www.contoso.com/images/image.gif » ?**

SSL n'est actuellement pas pris en charge sur les comptes de stockage avec des domaines personnalisés.
Mais vous pouvez utiliser des domaines personnalisés non-HTTPS. Pour plus d’informations, consultez la page [Configurer un nom de domaine personnalisé pour un point de terminaison Blob Storage](../blobs/storage-custom-domain-name.md).

**Comment utiliser FTP pour accéder aux données d'un compte de stockage ?**

Il n'existe aucun moyen d'accéder directement à un compte de stockage via FTP. Toutefois, vous pouvez configurer une machine virtuelle Azure puis installer un serveur FTP sur cette machine virtuelle. Vous pouvez faire en sorte que le serveur FTP stocke les fichiers dans un partage Azure Files ou sur un disque de données disponible pour la machine virtuelle.
Si vous souhaitez uniquement télécharger des données sans avoir à utiliser l'Explorateur de stockage ou une application similaire, vous pouvez utiliser un jeton SAS. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.

Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.