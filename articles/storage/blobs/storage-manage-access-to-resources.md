---
title: "Activer l’accès en lecture public pour les conteneurs et les objets blob dans Stockage Blob Azure | Microsoft Docs"
description: "Découvrez comment autoriser l’accès anonyme aux conteneurs et aux objets Blob et comment utiliser un programme pour y accéder."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 4ddafb095816b5be82a18faa9c60869094e5e4c6
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob
Vous pouvez activer l’accès en lecture anonyme public pour un conteneur et ses objets blob dans Stockage Blob Azure. En procédant ainsi, vous pouvez accorder un accès en lecture seule à ces ressources sans partager votre clé de compte et sans exiger de signature d’accès partagé (SAP).

L’accès en lecture public est idéal dans les situations où vous voulez conférer à certains objets blob un accès en lecture anonyme permanent. Pour un contrôle plus précis, vous pouvez créer une signature d’accès partagé. Les signatures d’accès partagé vous permettent d’accorder un accès restreint avec différentes autorisations sur une période donnée. Pour plus d’informations sur la création de signatures d’accès partagé, consultez [Utilisation des signatures d’accès partagé (SAP) dans le Stockage Azure](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob
Par défaut, un conteneur et tous les objets blob qu'il contient sont accessibles uniquement par le propriétaire du compte de stockage. Pour fournir aux utilisateurs anonymes des autorisations de lecture sur un conteneur et ses objets blob, vous pouvez configurer les autorisations du conteneur afin d’autoriser l’accès public. Les utilisateurs anonymes peuvent lire les objets blob d’un conteneur accessible publiquement sans avoir à authentifier la demande.

Vous pouvez configurer un conteneur avec les autorisations suivantes :

* **No public read access (Aucun accès en lecture public) :** seul le propriétaire du compte de stockage peut accéder au conteneur et à ses objets blob. Il s’agit de la configuration par défaut de tous les nouveaux conteneurs.
* **Accès en lecture public pour les objets blobs uniquement :** les objets blob présents dans le conteneur peuvent être lus par une demande anonyme, mais les données du conteneur ne sont pas disponibles. Les clients anonymes ne peuvent pas énumérer les objets blob présents dans le conteneur.
* **Accès en lecture public total :** toutes les données du conteneur et des objets blob peuvent être lues par une demande anonyme. Les clients peuvent énumérer les objets blob présents dans le conteneur par une demande anonyme, mais ne peuvent pas énumérer les conteneurs présents dans le compte de stockage.

Vous pouvez définir les autorisations de conteneur par les moyens suivants :

* [Portail Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Par programmation, en utilisant l’une des bibliothèques clientes de stockage ou l’API REST

### <a name="set-container-permissions-in-the-azure-portal"></a>Définir des autorisations de conteneur dans le portail Azure
Pour définir des autorisations de conteneur dans le [portail Azure](https://portal.azure.com), effectuez les étapes suivantes :

1. Ouvrez le panneau de votre **Compte de stockage** dans le portail. Vous pouvez trouver votre compte de stockage en sélectionnant **Comptes de stockage** dans le panneau de menu principal du portail.
1. Sous l’élément **SERVICE BLOB** du panneau de menu, sélectionnez **Conteneurs**.
1. Cliquez avec le bouton droit sur la ligne du conteneur ou sélectionnez les points de suspension pour ouvrir le **menu contextuel** du conteneur.
1. Sélectionnez **Stratégie d’accès** dans le menu contextuel.
1. Sélectionnez un **type d’accès** dans le menu déroulant.

    ![Boîte de dialogue Modifier les métadonnées du conteneur](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Définir des autorisations de conteneur avec .NET
Pour définir les autorisations d’un conteneur en utilisant C# et la bibliothèque cliente de stockage pour .NET, vous devez d’abord récupérer les autorisations existantes du conteneur en appelant la méthode **GetPermissions**. Définissez ensuite la propriété **PublicAccess** de l’objet **BlobContainerPermissions** qui est retourné par la méthode **GetPermissions**. Pour finir, appelez la méthode **SetPermissions** avec les autorisations mises à jour.

Dans l’exemple suivant, nous définissons les autorisations du conteneur pour permettre un accès en lecture public complet. Pour autoriser un accès en lecture public pour les objets Blob uniquement, définissez la propriété **PublicAccess** sur **BlobContainerPublicAccessType.Blob**. Pour supprimer toutes les autorisations pour les utilisateurs anonymes, définissez la propriété sur **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Accéder anonymement aux conteneurs et aux objets Blob
Un client ayant un accès anonyme aux conteneurs et aux objets Blob peut utiliser des constructeurs qui ne nécessitent pas d’informations d’identification. Les exemples suivants illustrent différentes manières de référencer des ressources de service Blob de façon anonyme.

### <a name="create-an-anonymous-client-object"></a>Créer un objet de client anonyme
Vous pouvez créer un nouvel objet de client de service pour permettre un accès anonyme en spécifiant le point de terminaison du service Blob associé au compte. Toutefois, vous devez également connaître le nom d’un conteneur attaché à ce compte qui est disponible pour un accès anonyme.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Référencer un conteneur de manière anonyme
Si vous disposez de l’URL permettant d’accéder à un conteneur accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement le conteneur.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Référencer un objet Blob de façon anonyme
Si vous disposez de l’URL permettant d’accéder à un objet Blob accessible de manière anonyme, vous pouvez l’utiliser pour référencer directement l’objet Blob :

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Fonctionnalités accessibles aux utilisateurs anonymes
Le tableau suivant indique les opérations pouvant être appelées par les utilisateurs anonymes quand la liste de contrôle d’accès (ACL, Access Control List) est définie pour autoriser l’accès public.

| Opération REST | Autorisation avec accès en lecture public complet | Autorisation avec accès en lecture public pour les objets blob uniquement |
| --- | --- | --- |
| List Containers |Propriétaire uniquement |Propriétaire uniquement |
| Create Container |Propriétaire uniquement |Propriétaire uniquement |
| Get Container Properties |Tous |Propriétaire uniquement |
| Get Container Metadata |Tous |Propriétaire uniquement |
| Set Container Metadata |Propriétaire uniquement |Propriétaire uniquement |
| Get Container ACL |Propriétaire uniquement |Propriétaire uniquement |
| Set Container ACL |Propriétaire uniquement |Propriétaire uniquement |
| Delete Container |Propriétaire uniquement |Propriétaire uniquement |
| List Blobs |Tous |Propriétaire uniquement |
| Put Blob |Propriétaire uniquement |Propriétaire uniquement |
| Get Blob |Tous |Tous |
| Get Blob Properties |Tous |Tous |
| Set Blob Properties |Propriétaire uniquement |Propriétaire uniquement |
| Get Blob Metadata |Tous |Tous |
| Set Blob Metadata |Propriétaire uniquement |Propriétaire uniquement |
| Put Block |Propriétaire uniquement |Propriétaire uniquement |
| Get Block List (blocs validés uniquement) |Tous |Tous |
| Get Block List (blocs non validés uniquement ou tous les blocs) |Propriétaire uniquement |Propriétaire uniquement |
| Put Block List |Propriétaire uniquement |Propriétaire uniquement |
| Delete Blob |Propriétaire uniquement |Propriétaire uniquement |
| Copie d'un objet blob |Propriétaire uniquement |Propriétaire uniquement |
| Snapshot Blob |Propriétaire uniquement |Propriétaire uniquement |
| Lease Blob |Propriétaire uniquement |Propriétaire uniquement |
| Put Page |Propriétaire uniquement |Propriétaire uniquement |
| Get Page Ranges |Tous |Tous |
| Append Blob |Propriétaire uniquement |Propriétaire uniquement |

## <a name="next-steps"></a>Étapes suivantes

* [Authentification pour les services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Utilisation des signatures d’accès partagé (SAP)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Délégation de l'accès avec une signature d'accès partagé](https://msdn.microsoft.com/library/azure/ee395415.aspx)
