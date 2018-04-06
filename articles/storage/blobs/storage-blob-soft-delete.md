---
title: Suppression réversible pour objets blob de Stockage Azure (préversion) | Documents Microsoft
description: Le Stockage Azure offre désormais fonctionnalité de une suppression réversible (préversion) pour les objets blob, de sorte que vous pouvez plus facilement récupérer vos données en cas de modification ou de suppression erronées par une application ou un autre utilisateur du compte de stockage.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 649838af1d4c753ac1d82a66c855ef313f14e85b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Suppression réversible pour objets blob de Stockage Azure (préversion)

## <a name="overview"></a>Vue d'ensemble

Le Stockage Azure offre désormais une fonctionnalité de suppression réversible (préversion) pour les objets blob, qui vous permet de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses de celles-ci par une application ou un autre utilisateur du compte de stockage.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Quand elle est activée, la suppression réversible vous permet d’enregistrer et de récupérer vos données en cas de suppression d’objets blob ou d’instantanés d’objets blob. Cette protection s’étend aux données d’objet blob effacées à la suite d’un remplacement.

Lorsque les données sont supprimées, elle passent par un état transitoire de suppression réversible au lieu d’être supprimées définitivement. Quand la suppression réversible est activée, lorsque vous remplacez des données, un instantané de l’objet blob supprimé de manière réversible est généré pour enregistrer l’état des données remplacées. Les objets supprimés de manière réversible sont invisibles, sauf s’ils sont répertoriés de façon explicite.
Vous pouvez configurer la durée pendant laquelle les données supprimées de manière réversible seront récupérables avant leur expiration définitive.

La suppression réversible étant rétrocompatible, il est inutile d’apporter des modifications à vos applications pour tirer parti des protections qu’offre cette fonctionnalité. Toutefois, la fonctionnalité de [récupération des données](#recovery) introduit une nouvelle API de **rétablissement d’objet blob supprimé**.

> [!NOTE]
> Durant la période de préversion publique, l’appel de la commande Définir le niveau du blob sur un objet blob avec des instantanés n’est pas autorisé.
La suppression réversible génère des instantanés pour protéger vos données en cas de remplacement de celles-ci. Nous travaillons activement sur une solution qui permette de hiérarchiser les objets blob avec des instantanés.

### <a name="configuration-settings"></a>Paramètres de configuration

Lorsque vous créez un compte, la suppression réversible est désactivée par défaut. Elle est également désactivée par défaut pour les comptes de stockage existants. Vous pouvez activer ou désactiver la fonctionnalité à tout moment pendant la durée de vie d’un compte de stockage.

Vous pouvez toujours accéder aux données supprimées de manière réversible pour les récupérer lorsque la fonctionnalité est désactivée, pour autant que les données supprimées de manière réversible aient été enregistrées pendant que la fonctionnalité était activée. Lorsque vous activez la suppression réversible, vous devez également configurer la période de rétention.

La période de rétention indique la durée pendant laquelle les données supprimées de manière réversible sont stockées et disponibles pour récupération. Pour les objets blob et instantanés d’objets blob supprimés explicitement, l’horloge de la période de rétention démarre dès la suppression des données. Pour les instantanés supprimés de manière réversible générés par la fonctionnalité de suppression réversible lors du remplacement de données, l’horloge démarre lors de la génération de l’instantané. Actuellement, vous pouvez conserver des données supprimées de manière réversible pendant une période de 1 à 365 jours.

Vous pouvez modifier la période de rétention de suppression réversible à tout moment. Une mise à jour de la période de rétention s’applique uniquement aux données supprimées par la suite. Les données supprimées antérieurement expireront à l’issue de la période de rétention qui était configurée lors de leur suppression.

### <a name="saving-deleted-data"></a>Enregistrement de données supprimées

La suppression réversible préserve vos données dans les nombreux cas où les objets blob ou instantanés d’objets blob sont supprimés ou remplacés.

Quand un objet blob est remplacé à l’aide d’une des commandes **Put Blob**, **Put Block**, **Put Block List** ou **Copy Blob**, un instantané de l’état de l’objet blob avant l’opération d’écriture est généré automatiquement. Cet instantané étant supprimé de manière réversible, il est invisible, sauf si les objets supprimés de manière réversible sont répertoriés explicitement. Pour savoir comment répertorier des objets supprimés de manière réversible, voir la section [Récupération](#recovery).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Si B0 est remplacé par B1, un instantané d’objet blob supprimé de manière réversible de B0 est généré. Si B1 est remplacé par B2, un instantané d’objet blob supprimé de manière réversible de B1 est généré.*

> [!NOTE]
> La fonctionnalité de suppression réversible offre une protection en cas de remplacement pour les opérations de copie uniquement quand elle est activée pour le compte de l’objet blob de destination.

> [!NOTE]
> La fonctionnalité de suppression réversible n’offre pas de protection en cas de remplacement pour les objets blob au niveau archive. Si un objet blob dans l’archive est remplacé par un nouvel objet blob à un niveau quelconque, l’objet blob remplacé expire définitivement.

Lors de l’appel de la commande **Delete Blob** sur un instantané, celui-ci est marqué comme supprimé de manière réversible. Aucun nouvel instantané n’est généré.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Lors de l’appel de la commande **Snapshot Blob**, B0 devient un instantané et B1 est l’état actif de l’objet blob. Quand l’instantané B0 est supprimé, il est marqué comme supprimé de manière réversible.*

Lors de l’appel de la commande **Delete Blob** sur un objet blob de base (tout objet blob qui est pas un instantané), celui-ci est marqué comme supprimé de manière réversible. Conformément au comportement précédent, l’appel de la commande **Delete Blob** sur un objet blob ayant des instantanés actifs renvoie une erreur. L’appel de la commande **Delete Blob** sur un objet blob ayant un instantané d’objet blob supprimé de manière réversible ne retourne pas d’erreur. Quand la suppression réversible est activée, vous pouvez toujours supprimer un objet blob et tous ses instantanés en une seule opération. Cela a pour effet de marquer l’objet blob de base et les instantanés comme étant supprimés de manière réversible.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Ici, un appel de la commande **Delete Blob** est effectué pour supprimer B2 et tous les instantanés qui y sont associés. L’objet blob actif, B2, ainsi que tous les instantanés associés à celui-ci, sont marqués comme étant supprimés de manière réversible.*

> [!NOTE]
> Quand un objet blob supprimé de manière réversible est remplacé, un instantané d’objet blob supprimé de manière réversible reflétant l’état de l’objet blob avant l’opération d’écriture est généré automatiquement. Le nouvel objet blob hérite le niveau de l’objet blob remplacé.

La fonctionnalité de suppression réversible n’enregistre pas vos données en cas de suppression de conteneur ou de compte, ou en cas de remplacement de métadonnées et propriétés d’objet blob. Pour protéger un compte de stockage contre une suppression malencontreuse, vous pouvez configurer un verrou à l’aide d’Azure Resource Manager. Pour en savoir plus, voir l’article sur Azure Resource Manager intitulé [Verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/resource-group-lock-resources.md).

Le tableau suivant indique le comportement attendu quand la suppression réversible est activée :

| Opération d’API REST                                                                                                | Type de ressource                 | Description                                                                                                 | Modification du comportement                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Supprimer](/rest/api/storagerp/StorageAccounts/Delete)              | Compte                       | Supprime le compte de stockage avec l’ensemble des conteneurs et objets blob qui y figurent.                           | Aucune modification. Les conteneurs et objets blob dans le compte supprimé ne sont pas récupérables.                                                                                                                                                                                                                                                                                          |
| [Delete Container](/rest/api/storageservices/fileservices/delete-container)       | Conteneur                     | Supprime le conteneur avec tous les objets blob qui y figurent.                                                | Aucune modification. Les objets blob figurant dans le conteneur supprimé ne sont pas récupérables.                                                                                                                                                                                                                                                                                                       |
| [Put Blob](/rest/api/storageservices/fileservices/put-blob)                       | Objets blob de blocs, d’ajouts et de pages | Crée ou remplace un objet blob dans un conteneur.                                          | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel de la commande est généré automatiquement. Cela s’applique également à un objet blob précédemment supprimé de manière réversible si et seulement si celui-ci est remplacé par un objet blob du même type (objet blob de blocs, d’ajouts ou de pages). En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement. |
| [Delete Blob](/rest/api/storageservices/fileservices/delete-blob)                 | Objets blob de blocs, d’ajouts et de pages | Marque un objet blob ou un instantané d’objet blob pour suppression. L’objet blob ou l’instantané est ensuite supprimé pendant le nettoyage de la mémoire | Si cette opération est utilisée pour supprimer un instantané d’objet blob, celui-ci est marqué comme étant supprimé de manière réversible. Si cette opération est utilisée pour supprimer un objet blob, celui-ci est marqué comme étant supprimé de manière réversible.                                                                                                                                                                                                                           |
| [Copy Blob](/rest/api/storageservices/fileservices/copy-blob)                     | Objets blob de blocs, d’ajouts et de pages | Copie un objet blob source vers un objet blob de destination dans le même ou dans un autre compte de stockage.       | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel de la commande est généré automatiquement. Cela s’applique également à un objet blob précédemment supprimé de manière réversible si et seulement si celui-ci est remplacé par un objet blob du même type (objet blob de blocs, d’ajouts ou de pages). En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement. |
| [Put Block](/rest/api/storageservices/put-block)                                  | Objets blob de blocs                   | Crée un bloc à valider en tant qu’élément d’un objet blob de blocs.                                                | Si cette opération est utilisée pour valider un bloc dans un objet blob actif, rien ne change. Si elle est utilisée pour valider un bloc dans un objet blob supprimé de manière réversible, un nouvel objet blob est créé, et un instantané est généré automatiquement pour capturer l’état de l’objet blob supprimé de manière réversible.                                                                                                                      |
| [Put Block List](/rest/api/storageservices/fileservices/put-block-list)           | Objets blob de blocs                   | Valide un objet blob en spécifiant le jeu d’ID de bloc composant cet objet blob de blocs.                             | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel de la commande est généré automatiquement. Cela s’applique également à tout objet blob précédemment supprimé de manière réversible si et seulement s’il s’agit d’un objet blob de blocs. En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement.                                                |
| [Put Page](/rest/api/storageservices/fileservices/put-page)                       | Objets blob de pages                    | Écrit une plage de pages dans un objet blob de pages.                                                                     | Aucune modification. Les données de l’objet blob de pages remplacées ou effacées par cette opération n’étant pas enregistrées, elles sont irrécupérables.                                                                                                                                                                                                                                                   |
| [Append Block](/rest/api/storageservices/fileservices/append-block)               | Objets blob d’ajouts                  | Écrit un bloc de données à la fin d’un objet blob d’ajouts.                                                         | Aucune modification.                                                                                                                                                                                                                                                                                                                                                           |
| [Set Blob Properties](/rest/api/storageservices/fileservices/set-blob-properties) | Objets blob de blocs, d’ajouts et de pages | Définit les valeurs des propriétés système pour un objet blob.                                                       | Aucune modification. Les propriétés d’objet blob remplacées ne sont pas récupérables.                                                                                                                                                                                                                                                                                                          |
| [Set Blob Metadata](/rest/api/storageservices/fileservices/set-blob-metadata)     | Objets blob de blocs, d’ajouts et de pages | Définit les métadonnées définies par l’utilisateur pour l’objet blob spécifié en tant qu’une ou plusieurs paires nom-valeur.                          | Aucune modification. Les métadonnées de l’objet blob remplacé ne sont pas récupérables.                                                                                                                                                                                                                                                                                                             |

Il est important de noter que l’appel de la commande « Put Page » pour remplacer ou supprimer des plages de pages dans un objet blob de pages n’a pas pour effet de générer automatiquement des instantanés. Les disques de machine virtuelle s’appuient sur des objets blob de pages et utilisent la commande **Put Page** pour écrire des données.

### <a name="recovery"></a>Récupération

Pour faciliter la récupération de données supprimées, nous avons introduit la nouvelle API « Undelete Blob ». L’appel de l’API Undelete sur un objet blob de base supprimé de manière réversible a pour effet de restaurer comme actifs cet objet blob ainsi que tous les instantanés d’objets blob supprimés de manière réversible qui y sont associés. L’appel de l’API Undelete sur un objet blob de base actif a pour effet de restaurer comme actifs tous les instantanés d’objets blob supprimés de manière réversible qui y sont associés. Quand des instantanés sont restaurés comme actifs, ils ressemblent à des instantanés générés par l’utilisateur et ne remplacent pas l’objet blob de base.

Pour restaurer un objet blob en instantané d’objet blob supprimé de manière réversible, vous pouvez appeler la commande **Undelete Blob** sur l’objet blob de base. Ensuite, vous pouvez copier l’instantané sur l’objet blob ainsi activé. Vous pouvez également copier l’instantané vers un nouvel objet blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Ici, la commande **Undelete Blob** est appelée sur l’objet blob B, ce qui a pour effet de restaurer comme actifs l’objet blob de base B1 ainsi que tous les instantanés qui y sont associés, en l’occurrence uniquement B0. Dans la deuxième étape, B0 est copié sur l’objet blob de base. Cette opération de copie génère un instantané supprimé de manière réversible de B1.*

Pour afficher les objets blob et instantanés d’objets blob supprimés de manière réversible, vous pouvez inclure des données supprimées dans la commande **List Blobs**. Vous pouvez afficher uniquement les objets blob de base supprimés de manière réversible ou inclure les instantanés d’objets blob supprimés. Pour toutes les données supprimées de manière réversible, vous pouvez afficher l’heure de leur suppression, ainsi que le nombre de jours avant leur expiration définitive.

### <a name="example"></a>Exemples

Voici la sortie de console d’un script .NET qui charge, remplace, crée un instantané, supprime et restaure un objet blob nommé « HelloWorld » quand la suppression réversible est activée :

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Pour obtenir un pointeur vers l’application qui a produit cette sortie, voir la section [Étapes suivantes](#Next steps).

## <a name="pricing-and-billing"></a>Tarification et facturation

Toutes les données supprimées de manière réversible sont facturées au même tarif que des données actives. Vous ne serez pas facturé pour des données supprimées définitivement à l’issue de la période de rétention configurée. Pour plus de détails sur les instantanés et la manière dont ils augmentent les coûts, voir [Présentation des frais liés aux instantanés](storage-blob-snapshots.md).

Vous ne serez pas facturé pour les transactions associées à la génération automatique d’instantanés. Vous serez facturé pour les transactions **Undelete Blob** au taux des « Opérations d’écriture ».

Pour plus de détails sur les prix du Stockage Blob Azure en général, voir la [page relative aux tarifs de Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Lorsque vous activez initialement la suppression réversible, nous vous recommandons d’utiliser une courte période de rétention pour mieux comprendre l’impact de cette fonctionnalité sur votre facture.

## <a name="quickstart"></a>Démarrage rapide

### <a name="azure-portal"></a>Portail Azure
Pour activer la suppression réversible, accédez à l’option **Suppression réversible** sous **Service Blob**. Cliquez ensuite sur **Activé**, puis entrez le nombre de jours pendant lesquels vous souhaitez retenir les données supprimées de manière réversible.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Pour afficher les objets blob supprimés de manière réversible, activez la case à cocher **Afficher les objets blob supprimés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Pour afficher les instantanés d’objets blob supprimés de manière réversible pour un objet blob donné, sélectionnez celui-ci, puis cliquez sur **Afficher les instantanés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Assurez-vous que la case à cocher **Afficher les instantanés supprimés** est activée.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Lorsque vous cliquez sur un blob ou un instantané d’objet blob supprimés de manière réversible, notez les nouvelles propriétés de l’objet blob. Elles indiquent quand l’objet a été supprimé, et le nombre de jours restants avant l’expiration définitive de l’objet blob ou de l’instantané d’objet blob. Si l’objet supprimé de manière réversible n’est pas un instantané, vous avez également la possibilité d’annuler sa suppression.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

N’oubliez pas que l’annulation de la suppression d’un objet blob a également pour effet d’annuler la suppression de tous les instantanés associés. Pour annuler la suppression des instantanés d’objets blob supprimés de manière réversible pour un objet blob actif, cliquez sur celui-ci, puis sélectionnez **Annuler la suppression de tous les instantanés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Après avoir annulé la suppression d’instantanés d’un objet blob, vous pouvez cliquer sur **Promouvoir** pour copier un instantané sur la racine du blob, ce qui a pour effet de restaurer l’objet blob à son instantané.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob. L’exemple suivant active la suppression réversible pour un sous-ensemble de comptes dans un abonnement :

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Pour récupérer des objets blob supprimés accidentellement, appelez la commande Undelete sur ceux-ci. N’oubliez pas que l’appel de la commande **Undelete Blob** sur les objets blob tant actifs que supprimés de manière réversible, a pour effet de restaurer comme actifs tous les instantanés d’objets blob supprimés de manière réversible. L’exemple suivant appelle la commande Undelete sur tous les objets blob, tant actifs que supprimés de manière réversible, figurant dans un conteneur :
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Bibliothèque cliente Python

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob :

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Bibliothèque cliente .Net

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob :

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Pour récupérer des objets blob supprimés accidentellement, appelez la commande Undelete sur ceux-ci.
N’oubliez pas que l’appel de la commande **Undelete Blob** sur les objets blob tant actifs que supprimés de manière réversible, a pour effet de restaurer comme actifs tous les instantanés d’objets blob supprimés de manière réversible. L’exemple suivant appelle la commande Undelete sur tous les objets blob, tant actifs que supprimés de manière réversible, figurant dans un conteneur :

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Pour récupérer une version spécifique d’un objet blob, commencez par appeler la commande Undelete sur un objet blob, puis copiez l’instantané souhaité sur l’objet blob. L’exemple suivant récupère un objet blob de blocs en restaurant son instantané généré le plus récemment :

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>Dois-je utiliser la suppression réversible ?

S’il existe une possibilité de modification ou de suppression accidentelles de vos données par une application ou un autre utilisateur du compte de stockage, nous vous recommandons d’activer la suppression réversible.
Cette fonctionnalité qui s’inscrit dans le cadre d’une stratégie de protection des données peut vous aider à prévenir toute perte malencontreuse de données.

## <a name="faq"></a>Forum Aux Questions

**Pour quels types de stockages puis-je utiliser la suppression réversible ?**

Actuellement, la suppression réversible est disponible uniquement pour le stockage d’objets blob.

**La suppression réversible est-elle disponible pour tous les types de comptes de stockage ?**

Oui, la suppression réversible est disponible pour les comptes de stockage d’objets blob, ainsi que pour les objets blob figurant dans des comptes de stockage à usage général. Cela s’applique aux comptes Standard et Premium. La suppression réversible n’est pas disponible pour les disques managés.

**La suppression réversible est-elle disponible pour tous les niveaux de stockage ?**

Oui, la suppression réversible est disponible pour tous les niveaux de stockage : chaud, froid et archive. Toutefois, la suppression réversible n’offre de protection contre le remplacement pour les objets blob au niveau archive.

**Les comptes de stockage Premium sont limités à 100 suppressions réversibles par instantané d’objet blob. Les instantanés d’objets blob supprimés de manière réversible sont-ils pris en compte par rapport à cette limite ?**

Non, ils ne le sont pas.

**Puis-je activer la suppression réversible pour des comptes de stockage existants ?**

Oui, la suppression réversible est configurable pour des comptes de stockage tant existants que nouveaux.

**Si je supprime un compte ou un conteneur entiers alors que la suppression réversible est activée, les objets blob associés sont-ils tous enregistrés ?**

Non, si vous supprimez un compte ou un conteneur entiers, tous les objets blob associés sont supprimés définitivement. Pour savoir comment protéger un compte de stockage contre des suppressions accidentelles, voir l’article sur Azure Resource Manager intitulé [Verrouiller les ressources pour empêcher les modifications inattendues](/azure-resource-manager/resource-group-lock-resources.md).

**Puis-je afficher les métriques de capacité pour des données supprimées ?**

Les données supprimées de manière réversible sont incluses dans la capacité totale de votre compte de stockage. Pour plus d’informations sur le suivi et l’analyse de la capacité de stockage, voir l’article [Storage Analytics](../common/storage-analytics.md).

**Si je désactive la suppression réversible, pourrai-je toujours accéder aux données supprimées de manière réversible ?**

Oui, vous pourrez toujours accéder aux données supprimées de manière réversible non expirées, ainsi que les récupérer, même si la suppression réversible est désactivée.

**Puis-je lire et copier les instantanés supprimés de manière réversible de mon objet blob ?**

Oui, mais vous devez commencer par appeler la commande Undelete sur l’objet blob.

**La suppression réversible est-elle disponible pour tous les types d’objets blob ?**

Oui, la suppression réversible est disponible pour les objets blob de blocs, d’ajouts et de pages.

**La suppression réversible est-elle disponible pour les disques de machine virtuelle ?**

La suppression réversible est disponible pour les disques non managés Standard et Premium. La suppression réversible vous permettra de récupérer uniquement des données supprimées par les commandes **Delete Blob**, **Put Blob**, **Put Block List**, **Put Block** et **Copy Blob**. Les données remplacées par un appel de la commande **Put Page** ne sont pas récupérables.

**Dois-je modifier mes applications existantes pour utiliser la suppression réversible ?**

Vous pouvez tirer parti de la suppression réversible, quelle que soit la version d’API que vous utilisez. Toutefois, pour répertorier et récupérer des objets blob et instantanés d’objets blob supprimés de manière réversible, vous devez utiliser la version 2017-07-29 de l’[API REST des services de stockage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou une version supérieure. En général, nous recommandons toujours d’utiliser la version la plus récente, que vous utilisiez cette fonctionnalité ou non.

## <a name="next-steps"></a>Étapes suivantes

* [Exemple de code .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [API REST du service Blob](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Réplication Azure Storage](../common/storage-redundancy.md)

* [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

* [Que faire en cas de panne du Stockage Azure](../common/storage-disaster-recovery-guidance.md)
