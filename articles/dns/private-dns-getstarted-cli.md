---
title: Prise en main des zones Azure DNS privées à l’aide d’Azure CLI 2.0 | Microsoft Docs
description: Découvrez comment créer un enregistrement et une zone DNS privée dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone privée et de votre premier enregistrement DNS à l’aide d’Azure CLI 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Prise en main des zones Azure DNS privées à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

Cet article vous guide tout au long de la procédure de création de votre première zone privée et de votre premier enregistrement DNS à l’aide de l’interface de ligne de commande Azure CLI 2.0 multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également effectuer ces étapes à l’aide d’Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Pour publier une zone DNS privée sur votre réseau virtuel, vous spécifiez la liste des réseaux virtuels qui sont autorisés à résoudre les enregistrements dans la zone.  On parle de « réseaux virtuels de résolution ».  Vous pouvez également spécifier un réseau virtuel pour lequel Azure DNS conserve les enregistrements de nom d’hôte chaque fois qu’une machine virtuelle est créée, change d’adresse IP ou est détruite.  On parle de « réseau virtuel d’inscription ».

Ces instructions supposent que vous avez déjà installé Azure CLI 2.0 et que vous y êtes connecté, et que vous avez également installé l’extension CLI requise pour prendre en charge les zones privées. Pour obtenir de l’aide, consultez [Gérer des zones DNS dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Pour installer/utiliser la fonctionnalité des zones DNS privées Azure (version préliminaire publique)
La fonctionnalité des zones DNS privées Azure est proposée dans la version préliminaire publique via une extension Azure CLI. Installer l’extension Azure CLI « dns » 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la zone DNS, un groupe de ressources est créé pour contenir la zone DNS. Voici la commande.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Créer une zone DN privée

Une zone DNS privée est créée à l’aide de la commande `az network dns zone create`. Pour consulter l’aide de cette commande, tapez `az network dns zone create --help`.

L’exemple suivant crée une zone DNS privée appelée *contoso.local* dans le groupe de ressources *MyResourceGroup* et la rend disponible (lien vers) pour le réseau virtuel *MyAzureVnet* à l’aide du paramètre resolution-vnets. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Remarque : dans l’exemple ci-dessus, le réseau virtuel « MyAzureVnet » appartient au même groupe de ressources et d’abonnement que la zone privée. Si vous devez lier un réseau virtuel qui appartient à un autre groupe de ressources ou à un autre abonnement, spécifiez l’ID Azure Resource Manager complet au lieu du nom de réseau virtuel seulement pour le paramètre --resolution-vnets. 

Si vous souhaitez qu’Azure crée automatiquement des enregistrements de nom d’hôte dans la zone, utilisez le paramètre *registration-vnets* au lieu du paramètre *resolution-vnets*.  Les réseaux virtuels d’inscription sont automatiquement activés pour la résolution.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Pour créer un enregistrement DNS, utilisez la commande `az network dns record-set [record type] add-record`. Pour obtenir de l’aide, concernant les enregistrements A par exemple, consultez `azure network dns record-set A add-record --help`.

L’exemple suivant crée un enregistrement avec le nom relatif « ip1 » dans la zone DNS « contoso.local », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « ip1.contoso.local ». Le type d’enregistrement est « A », avec l’adresse IP « 10.0.0.1 ».

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Pour découvrir d’autres types d’enregistrements, des jeux d’enregistrements comportant plus d’un enregistrement et d’autres valeurs de durée de vie et pour modifier les enregistrements existants, consultez [Gérer les enregistrements DNS et les jeux d’enregistrement dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Obtenir une zone DNS privée

Pour récupérer une zone DNS privée, utilisez `az network dns zone show`. Pour obtenir de l’aide, consultez l’article `az network dns zone show --help`.

L’exemple suivant retourne la zone DNS *contoso.local* et les données associées à partir du groupe de ressources *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

L’exemple suivant est la réponse.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Notez que les enregistrements DNS ne sont pas retournés par `az network dns zone show`. Pour lister les enregistrements DNS, utilisez `az network dns record-set list`.


## <a name="list-dns-zones"></a>Création de la liste des zones DNS

Pour énumérer des zones DNS, utilisez `az network dns zone list`. Pour obtenir de l’aide, consultez l’article `az network dns zone list --help`.

Spécifier le groupe de ressources permet de ne lister que les zones du groupe de ressources :

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Omettre le groupe de ressources permet de lister toutes les zones de l’abonnement :

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Mise à jour d’une zone DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `az network dns zone update`. Pour obtenir de l’aide, consultez l’article `az network dns zone update --help`.

Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Pour les zones privées, vous pouvez mettre à jour les réseaux virtuels d’inscription ou de résolution liés à une zone. 

L’exemple suivant montre comment mettre à jour le réseau virtuel de résolution lié à une zone DNS privée. Le réseau virtuel de résolution lié existant est remplacé par le nouveau réseau virtuel spécifié.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Les zones DNS peuvent être supprimées en utilisant `az network dns zone delete`. Pour obtenir de l’aide, consultez l’article `az network dns zone delete --help`.

> [!NOTE]
> Supprimer une zone DNS supprime également tous les enregistrements DNS de la zone. Il est impossible d’annuler cette opération. Si la zone DNS est en cours d’utilisation, les services utilisant la zone échouent lors de la suppression de la zone.
>
>Pour vous protéger contre la suppression accidentelle de zones, consultez la page [Comment protéger des zones et enregistrements DNS](dns-protect-zones-recordsets.md).

Cette commande vous demande de confirmer l’opération. Le switch `--yes` facultatif supprime cette invite.

L’exemple suivant montre comment supprimer la zone *contoso.local* à partir du groupe de ressources *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources
 
Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

Pour en savoir plus sur la gestion des zones DNS dans Azure DNS, consultez [Gérer des zones DNS dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-dnszones-cli.md).

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gérer les enregistrements DNS et les jeux d’enregistrement dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).
