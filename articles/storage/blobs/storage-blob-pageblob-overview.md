---
title: "Fonctionnalité unique des objets blob de pages Azure | Microsoft Docs"
description: "Vue d’ensemble des objets blob de pages Azure, avantages et cas d’utilisation avec des exemples de scripts."
services: storage
author: anasouma
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/10/2018
ms.author: wielriac
ms.openlocfilehash: 56e8c4c9f7ab9b40a210f284960f959a437a4e20
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="unique-features-of-azure-page-blobs"></a>Fonctionnalités uniques des objets blob de pages Azure

Stockage Azure propose trois types de stockage blob : les objets blob de blocs, les objets blob d’ajouts et les objets blob de pages. Les objets blob de blocs sont constitués de blocs et sont parfaitement adaptés pour stocker des fichiers texte ou binaires, et pour charger efficacement des fichiers volumineux. Les objets blob d’ajouts sont également constitués de blocs, mais ils sont optimisés pour les opérations d’ajout, ce qui les rend idéaux pour les scénarios de journalisation. Les objets blob de pages sont constitués de pages jusqu’à 512 octets, jusqu’à 8 To de taille totale et sont plus efficaces pour les opérations de lecture/écriture aléatoires fréquentes. Les objets blob de pages constituent la base des disques IaaS Azure. Cet article se concentre sur la description des fonctionnalités et des avantages des objets blob de pages.

## <a name="overview"></a>Vue d'ensemble
Les objets blob de pages représentent une collection de pages de 512 octets, qui offrent la possibilité de lire/écrire des plages arbitraires d’octets. Les objets blob de pages sont donc particulièrement adaptés au stockage de structures de données basées sur des index et diverses comme les disques de système d’exploitation et de données pour machines virtuelles et bases de données. Par exemple, Azure SQL DB utilise des objets blob de pages comme stockage permanent sous-jacent pour ses bases de données. Les objets blob de pages sont également souvent utilisés pour les fichiers avec des mises à jour basées sur une plage.  

Les principales fonctionnalités des objets blob de pages Azure résident dans son interface REST, la durabilité du stockage sous-jacent et les fonctionnalités de migration parfaite vers Azure. Ces fonctionnalités sont abordées plus en détail dans la section suivante. De plus, les objets blob de pages Azure sont actuellement pris en charge sur deux types de stockage : Stockage Premium et Standard. Stockage Premium est plus particulièrement conçu pour les charges de travail nécessitant de hautes performances et une faible latence, ce qui rend les objets blob de pages premium idéaux pour les bases de données de stockage de données hautes performances.  Le stockage Standard est plus économique pour l’exécution de charges de travail insensibles à la latence.

## <a name="sample-use-cases"></a>Exemples de cas d’utilisation

Étudions quelques cas d’utilisation des objets blob de pages avec des disques IaaS Azure. Les objets blob de pages Azure constituent la base de la plateforme de disques virtuels pour IaaS Azure. Le système d’exploitation Azure et les disques de données sont implémentés en tant que disques virtuels dans lesquels les données sont conservées durablement dans la plateforme Stockage Azure, puis remises aux machines virtuelles pour des performances optimales. Les disques Azure sont conservés au [format VHD](https://technet.microsoft.com/library/dd979539.aspx) Hyper-V et stockés en tant qu’[objet blob de pages](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) dans Stockage Azure. En plus des disques virtuels pour machines virtuelles IaaS Azure, les objets blob de pages permettent également des scénarios PaaS et DBaaS tels que le service Azure SQL DB qui utilise actuellement des objets blob de pages pour stocker des données SQL, permettant ainsi des opérations de lecture/écriture aléatoires rapides pour la base de données. Dans un autre exemple, si vous disposez d’un service PaaS pour accéder aux fichiers multimédias partagés d’applications collaboratives d’édition vidéo, les objets blob de pages permettent un accès rapide à des emplacements aléatoires dans le média. Cela permet également à plusieurs utilisateurs de modifier et de fusionner rapidement et efficacement un même média. 

Des services Microsoft internes tels qu’Azure Site Recovery, Azure Backup, ainsi que de nombreux développeurs tiers, ont implémenté des innovations de pointe à l’aide de l’interface REST de l’objet blob de pages. Voici quelques-uns des scénarios uniques implémentés sur Azure : 
* Gestion des captures instantanées incrémentielle orientée application : les applications peuvent exploiter les instantanés d’objet blob de pages et les API REST pour enregistrer les points de contrôle d’application sans duplication coûteuse de données. Le stockage Azure prend en charge les instantanés locaux pour les objets blob de pages, qui ne nécessitent pas la copie de l’intégralité de l’objet blob. Ces API d’instantané publiques permettent également d’accéder à et de copier des deltas entre deux instantanés.
* Migration dynamique d’application et de données locales vers le cloud : copiez les données locales et utilisez les API REST pour écrire directement dans l’objet blob de pages Azure pendant l’exécution de la machine virtuelle locale. Lorsque la cible est interceptée, vous pouvez basculer rapidement vers la machine virtuelle Azure à l’aide de ces données. De cette façon, vous pouvez migrer vos machines virtuelles et disques virtuels locaux vers le cloud avec un temps d’arrêt minime, car la migration des données se produit en arrière-plan pendant que vous utilisez la machine virtuelle. Le temps d’arrêt nécessaire pour le basculement est donc relativement court (quelques minutes).
* Accès partagé [basé sur SAS](../common/storage-dotnet-shared-access-signature-part-1.md), qui permet des scénarios tels que plusieurs lecteurs et un auteur unique avec prise en charge du contrôle d’accès concurrentiel.

## <a name="page-blob-features"></a>Fonctionnalités d’objet blob de pages

### <a name="rest-api"></a>de l’API REST
Consultez le document suivant pour commencer à [développer à l’aide d’objets blob de pages](storage-dotnet-how-to-use-blobs.md). Par exemple, vous pouvez voir comment accéder aux objets blob de pages à l’aide de la bibliothèque cliente de stockage pour .NET. 

Le diagramme suivant décrit les relations globales entre le compte, les conteneurs et les objets blob de pages.

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-certain-size"></a>Création d’un objet blob de pages vide d’une taille spécifique
Pour créer un objet blob de pages, nous créons tout d’abord un objet **CloudBlobClient**, avec l’URI de base pour accéder au stockage blob de votre compte de stockage (*pbaccount* dans la figure 1), ainsi que l’objet **StorageCredentialsAccountAndKey**, comme dans l’exemple ci-dessous. L’exemple montre ensuite la création d’une référence à un objet **CloudBlobContainer**, puis la création du conteneur (*testvhds*) s’il n’existe pas déjà. Ensuite, à l’aide de l’objet **CloudBlobContainer**, nous pouvons créer une référence à un objet **CloudPageBlob** en spécifiant le nom de l’objet blob de pages (os4.vhd) auquel nous voulons accéder. Pour créer l’objet blob de pages, appelez [CloudPageBlob.Create](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.create?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Create_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) en indiquant la taille maximale de l’objet blob à créer. *blobSize* doit être un multiple de 512 octets.

```csharp
using Microsoft.WindowsAzure.StorageClient;
long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

#### <a name="resizing-a-page-blob"></a>Redimensionnement d’un objet blob de pages
Pour redimensionner un objet blob de pages après sa création, utilisez l’API [Redimensionner](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.resize?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudPageBlob_Resize_System_Int64_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). La taille demandée doit être un multiple de 512 octets.
```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes); 
```

#### <a name="writing-pages-to-a-page-blob"></a>Écriture de pages sur un objet blob de pages
Pour écrire des pages, utilisez la méthode [CloudPageBlob.WritePages](/library/microsoft.windowsazure.storageclient.cloudpageblob.writepages.aspx).  Cela vous permet d’écrire un ensemble séquentiel de pages jusqu’à 4 Mo. Le décalage écrit doit commencer sur une limite de 512 octets (startingOffset % 512 == 0) et se terminer sur une limite de 512 - 1.  L’exemple de code suivant montre comment appeler **WritePages** pour un objet blob :

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

Dès qu’une requête d’écriture pour un ensemble séquentiel de pages réussit dans le service blob et est répliquée à des fins de durabilité et de résilience, l’écriture est validée et sa réussite est signalée au client.  

Le diagramme ci-dessous montre 2 opérations d’écriture distinctes :

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Une opération d’écriture commençant au décalage 0 de 1 024 octets de longueur 
2.  Une opération d’écriture commençant au décalage 4096 d’une longueur de 1 024 

#### <a name="reading-pages-from-a-page-blob"></a>Lecture de pages d’un objet blob de pages
Pour lire des pages, utilisez la méthode [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.windowsazure.storage.blob.icloudblob.downloadrangetobytearray?view=azure-dotnet) pour lire une plage d’octets de l’objet blob de pages. Cela vous permet de télécharger le blob complet ou une plage d’octets commençant à un décalage quelconque dans le blob. Lors de la lecture, le décalage ne doit pas nécessairement commencer sur un multiple de 512. Lors de la lecture d’octets d’une page NUL, le service retourne zéro octet.
```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```
La figure suivante montre une opération de lecture avec BlobOffSet de 256 et rangeSize de 4352. Les données retournées sont mises en surbrillance en orange. Des zéros sont retournés pour les pages NUL

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Si vous disposez d’un objet blob peu rempli, vous pouvez vous contenter de télécharger les régions de page valides pour éviter les coûts d’entrée de zéro octet et pour réduire la latence du téléchargement.  Pour déterminer les pages contenant des données, utilisez [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.windowsazure.storage.blob.cloudpageblob.getpageranges?view=azure-dotnet). Vous pouvez alors énumérer les plages retournées et télécharger les données dans chaque plage. 
```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the rangeSize
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // TODO: use the buffer for the page range just read
} 
```

#### <a name="leasing-a-page-blob"></a>Leasing d’un objet blob de pages
L’opération de blob de bail établit et gère un verrou sur un blob pour les opérations d’écriture et de suppression. Cette opération est utile dans les scénarios où un objet blob de pages est accessible à partir de plusieurs clients, pour faire en sorte qu’un seul client à la fois puisse écrire dans l’objet blob. Les disques Azure, par exemple, utilisent ce mécanisme de leasing pour garantir que le disque n’est géré que par une seule machine virtuelle. La durée du verrou peut être de 15 à 60 secondes, ou peut être infinie. Consultez la documentation [ici](/rest/api/storageservices/lease-blob) pour plus de détails.

> Utilisez le lien suivant pour obtenir des [exemples de code](/resources/samples/?service=storage&term=blob&sort=0 ) pour de nombreux autres scénarios d’application. 

En plus des API REST riches en fonctionnalités, les objets blob de pages offrent également un accès partagé, une durabilité et une sécurité renforcée. Nous aborderons ces avantages plus en détails dans les paragraphes suivants. 

### <a name="concurrent-access"></a>Accès simultané
L’API REST des objets blob de pages et son mécanisme de leasing permettent aux applications d’accéder à l’objet blob de pages à partir de plusieurs clients. Par exemple, supposons que vous devez créer un service cloud distribué qui partage des objets de stockage avec plusieurs utilisateurs. Il pourrait s’agir d’une application web proposant une collection d’images importante à plusieurs utilisateurs. Une option d’implémentation consiste à utiliser une machine virtuelle avec des disques attachés. Ceci implique des inconvénients, notamment (i) la contrainte qu’un disque ne peut être attaché qu’à une seule machine virtuelle, limitant ainsi l’évolutivité, la flexibilité et augmentant les risques. En cas de problème lié à la machine virtuelle ou au service exécuté sur la machine virtuelle, et ensuite dû au bail, l’image est inaccessible jusqu’à ce que le bail expire ou qu’il soit interrompu ; et (ii) le coût supplémentaire lié à une machine virtuelle IaaS. 

Une autre option consiste à utiliser les objets blob de pages directement via les API REST de Stockage Azure. Cette option supprime la nécessité de machines virtuelles IaaS coûteuses, offre la flexibilité maximale d’accès direct à partir de plusieurs clients, simplifie le modèle de déploiement classique en supprimant la nécessité de joindre et de détacher des disques, et élimine le risque de problèmes sur la machine virtuelle. Elle offre également le même niveau de performances pour les opérations de lecture/écriture aléatoires qu’un disque

### <a name="durability-and-high-availability"></a>Durabilité et haute disponibilité
Les stockages Standard et Premium constituent un stockage durable où les données d’objet blob de pages sont toujours répliquées pour garantir la durabilité et la haute disponibilité. Pour plus d’informations sur la redondance du Stockage Azure, consultez cette [documentation](../common/storage-redundancy.md). Azure a fourni de façon cohérente une durabilité de classe Entreprise pour les disques IaaS et les objets blob de pages, avec un [taux de défaillance annuel](https://en.wikipedia.org/wiki/Annualized_failure_rate) inégalé dans le secteur de zéro %. Cela signifie qu’Azure n’a jamais perdu de données d’objet blob de pages d’un client. 

### <a name="seamless-migration-to-azure"></a>Migration parfaite vers Azure
Pour les clients et développeurs qui souhaitent implémenter leur propre solution de sauvegarde personnalisée, Azure propose également des instantanés incrémentiels ne contenant que les deltas. Cette fonctionnalité permet d’éviter le coût de la copie initiale complète, ce qui réduit considérablement le coût de sauvegarde. Avec la possibilité de lire et de copier efficacement des données différentielles, il s’agit d’une autre fonctionnalité puissante qui permet encore plus d’innovations plus les développeurs, offrant ainsi la meilleure expérience de sauvegarde et de reprise après sinistre sur Azure. Vous pouvez configurer votre propre solution de sauvegarde ou de reprise après sinistre pour vos machines virtuelles sur Azure à l’aide d’un [instantané d’objet blob](/rest/api/storageservices/snapshot-blob), de l’API [Get Page Ranges](/rest/api/storageservices/get-page-ranges) et de l’API [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), que vous pouvez utiliser pour copier facilement les données incrémentielles pour la reprise après sinistre. 

Nombre d’entreprises disposent en outre des charges de travail critiques déjà en cours d’exécution dans les centres de données locaux. Pour migrer la charge de travail vers le cloud, un des principales préoccupations serait liée au temps d’arrêt nécessaire pour copier les données et au risque de problèmes imprévus après le basculement. Dans de nombreux cas, le temps d’arrêt peut être un frein à la migration vers le cloud. Grâce à l’API REST Page Blobs, Azure résout ce problème en permettant une migration vers le cloud avec une interruption minimale des charges de travail critiques. 

Pour obtenir des exemples sur la prise d’un instantané et la restauration d’un objet blob de pages à partir d’un instantané, reportez-vous à l’article relatif à la [configuration d’un processus de sauvegarde à l’aide d’instantanés incrémentiels](../../virtual-machines/windows/incremental-snapshots.md).
