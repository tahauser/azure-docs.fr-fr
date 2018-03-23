---
title: Acheminer des événements de stockage Blob Azure vers un point de terminaison web - Powershell | Microsoft Docs
description: Utilisez Azure Event Grid pour vous abonner à des événements de stockage Blob.
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: 374a24448eb1bf366e26bb55fdf09e470b030c89
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>Acheminer des événements de stockage Blob vers un point de terminaison web avec PowerShell

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous utilisez Azure PowerShell pour vous abonner à des événements de stockage Blob, déclencher un événement et afficher le résultat. 

En règle générale, vous envoyez des événements à un point de terminaison qui répond à l’événement, comme un webhook ou une fonction Azure. Pour simplifier l’exemple présenté dans cet article, les événements sont envoyés à une URL qui collecte seulement les messages. Vous créez cette URL à l’aide d’outils tiers à partir de [RequestBin](https://requestb.in/) ou [Hookbin](https://hookbin.com/).

> [!NOTE]
> **RequestBin** et **Hookbin** ne sont pas destinés à une utilisation avec débit élevé. L’utilisation de ces outils est uniquement à but démonstratif. Si vous envoyez plusieurs événements par push en simultané, vous pouvez ne pas voir tous les événements dans l’outil.

En suivant les instructions de cet article, vous voyez que les données d’événement ont été envoyées à un point de terminaison.

![Données d’événement](./media/storage-blob-event-quickstart/request-result.png)

## <a name="setup"></a>Paramétrage

Pour cet article, vous devez disposer de la version la plus récente d’Azure PowerShell. Si vous devez installer ou mettre à niveau, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Login-AzureRmAccount
```

> [!NOTE]
> La disponibilité des événements de stockage est liée à la [disponibilité](../../event-grid/overview.md) d’Event Grid, et sera disponible dans d’autres régions en même temps qu’Event Grid.

Cet exemple utilise **westus2** et stocke la sélection dans une variable à utiliser partout ailleurs.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les rubriques Event Grid sont des ressources Azure et doivent être placées dans un groupe de ressources Azure. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

L’exemple suivant crée un groupe de ressources nommé **gridResourceGroup** à l’emplacement **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour utiliser des événements de stockage Blob, vous avez besoin d’un [compte de stockage d’objets blob](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) ou d’un [compte de stockage v2 à usage général](../common/storage-account-options.md#general-purpose-v2). Les comptes **v2 à usage général (GPv2)** sont des comptes de stockage qui prennent en charge toutes les fonctionnalités pour tous les services de stockage, notamment Objets BLOB, Fichiers, Files d’attente et Tables. Un **compte de stockage d’objets blob** est un compte de stockage spécialisé pour le stockage des données non structurées en tant qu’objets blob dans Stockage Azure. Les comptes de stockage d’objets blob sont comme vos comptes de stockage à usage général existants et offrent les excellents niveaux de durabilité, disponibilité, évolutivité et performances dont vous bénéficiez aujourd’hui. Ils assurent notamment la cohérence d’API à 100 % pour les objets blob de blocs et d’ajout. Pour les applications qui requièrent uniquement le stockage d’objets blob de blocs ou d’objets blob d’ajout, nous recommandons d’utiliser des comptes de stockage d’objets blob.  

Créez un compte de stockage d’objets blob avec la réplication LRS à l’aide de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), puis récupérez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Si un compte de stockage est utilisé, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. Cet exemple crée un compte de stockage nommé **gridstorage** avec les options de stockage localement redondant (LRS). 

> [!NOTE]
> Les noms des comptes de stockage étant dans un espace de noms global, vous devez ajouter certains caractères aléatoires au nom fourni dans ce script.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>Créer un point de terminaison de message

Avant de nous abonner à la rubrique, nous allons créer le point de terminaison pour le message de l’événement. Au lieu d’écrire du code qui réponde à l’événement, nous allons créer un point de terminaison qui collecte les messages, afin que vous puissiez les consulter. RequestBin et Hookbin sont des outils tiers open source qui vous permettent de créer un point de terminaison et d’afficher les requêtes qui lui sont envoyées. Accédez à [RequestBin](https://requestb.in/), puis cliquez sur **Create a RequestBin** (Créer un RequestBin), ou accédez à [Hookbin](https://hookbin.com/) et cliquez sur **Create New Endpoint (Créer un point de terminaison)**. Copiez l’URL bin et remplacez `<bin URL>` dans le script suivant.

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>Vous abonner à votre compte de stockage

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent. L’exemple suivant s’abonne au compte de stockage que vous avez créé et transmet l’URL à partir de RequestBin ou Hookbin en tant que point de terminaison de la notification d’événement. 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>Déclencher un événement à partir du stockage Blob

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Tout d’abord, nous allons créer un conteneur et un objet. Ensuite, nous chargerons l’objet dans le conteneur.

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Accédez à l’URL du point de terminaison créée précédemment. Ou cliquez sur Actualiser dans le navigateur ouvert. L’événement que vous venez d’envoyer apparaît. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous envisagez de continuer à utiliser ce compte de stockage et l’abonnement à un événement, ne supprimez pas les ressources créées dans cet article. Sinon, utilisez la commande suivante pour supprimer les ressources créées avec cet article.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements, vous pouvez en apprendre davantage sur les événements de stockage Blob et sur ce qu’Event Grid peut vous offrir :

- [Réaction aux événements de stockage Blob](storage-blob-event-overview.md)
- [Event Grid](../../event-grid/overview.md)
