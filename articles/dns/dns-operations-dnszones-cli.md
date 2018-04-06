---
title: Gérer des zones DNS dans Azure DNS - Azure CLI 2.0 | Microsoft Docs
description: Vous pouvez gérer des zones DNS à l’aide d’Azure CLI 2.0. Cet article explique comment mettre à jour, supprimer et créer des zones DNS sur Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: kumud
ms.openlocfilehash: d384f8867ddfd28acaf78a47a7d32729e87c5580
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Gérer des zones DNS dans Azure DNS à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)


Ce guide montre comment gérer vos zones DNS à l’aide de l’interface de ligne de commande Azure interplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également gérer vos zones DNS à l’aide [d’Azure PowerShell](dns-operations-dnszones.md) ou du portail Azure.

Ce guide porte spécifiquement sur les zones DNS publiques. Pour plus d’informations sur l’utilisation d’Azure CLI pour gérer les Zones privées dans Azure DNS, consultez [Prise en main d’Azure DNS Private Zones à l’aide d’Azure CLI 2.0](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Introduction

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configuration d’Azure CLI 2.0 pour Azure DNS

### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

* Installez la dernière version d’Azure CLI 2.0, disponible pour Windows, Linux ou MAC. Pour plus d’informations, consultez la page [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Connexion à votre compte Azure

Ouvrez une fenêtre de console et procédez à l’authentification à l’aide de vos informations d’identification. Pour plus d’informations, consultez [Se connecter à Azure avec la CLI Azure](https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```
az login
```

### <a name="select-the-subscription"></a>Sélection de l’abonnement

Vérifiez les abonnements associés au compte.

```
az account list
```

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>Facultatif : pour installer/utiliser la fonctionnalité Azure DNS Private Zones (préversion publique)
La fonctionnalité Azure DNS Private Zones est proposée dans la préversion publique via une extension d’Azure CLI. Installer l’extension d’Azure CLI « dns » 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que toutes les ressources DNS sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure DNS.

Ignorez cette étape si vous utilisez un groupe de ressources existant.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Obtenir de l’aide

Toutes les commandes CLI 2.0 liées à Azure DNS commencent par `az network dns`. Une aide est disponible pour chaque commande avec l’option `--help` (forme abrégée : `-h`).  Par exemple : 

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `az network dns zone create`. Pour obtenir de l’aide, consultez l’article `az network dns zone create -h`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup* :

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Pour créer une zone DNS avec des étiquettes

L’exemple suivant montre comment créer une zone DNS avec deux [balises Azure Resource Manager](dns-zones-records.md#tags), *projet = demo* et *env = test*, à l’aide du paramètre `--tags` (forme abrégée : `-t`) :

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Obtention d’une zone DNS

Pour récupérer une zone DNS, utilisez `az network dns zone show`. Pour obtenir de l’aide, consultez l’article `az network dns zone show --help`.

L’exemple suivant retourne la zone DNS *contoso.com* et les données associées à partir du groupe de ressources *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

L’exemple suivant est la réponse.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
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

Cette commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets-cli.md)). Elle est utilisée uniquement pour mettre à jour les propriétés de la ressource de zone elle-même. Ces propriétés sont actuellement limitées aux [« balises » Azure Resource Manager](dns-zones-records.md#tags) de la ressource de zone.

L’exemple suivant montre comment mettre à jour les balises sur une zone DNS. Les balises existantes sont remplacées par la valeur spécifiée.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Suppression d’une zone DNS

Les zones DNS peuvent être supprimées en utilisant `az network dns zone delete`. Pour obtenir de l’aide, consultez l’article `az network dns zone delete --help`.

> [!NOTE]
> Supprimer une zone DNS supprime également tous les enregistrements DNS de la zone. Il est impossible d’annuler cette opération. Si la zone DNS est en cours d’utilisation, les services utilisant la zone échouent lors de la suppression de la zone.
>
>Pour vous protéger contre la suppression accidentelle de zones, consultez la page [Comment protéger des zones et enregistrements DNS](dns-protect-zones-recordsets.md).

Cette commande vous demande de confirmer l’opération. Le switch `--yes` facultatif supprime cette invite.

L’exemple suivant montre comment supprimer la zone *contoso.com* à partir du groupe de ressources *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer des jeux d’enregistrements et des enregistrements](dns-getstarted-create-recordset-cli.md) dans votre zone DNS.

Découvrez comment [déléguer votre domaine à Azure DNS](dns-domain-delegation.md).

