---
title: Prise en main d’Azure DNS à l’aide d’Azure CLI 2.0 | Microsoft Docs
description: Découvrez comment créer une zone et un enregistrement DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide d’Azure CLI 2.0.
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
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Prise en main d’Azure DNS à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Cet article vous guide tout au long de la procédure de création de votre première zone et de votre premier enregistrement DNS à l’aide de l’interface de ligne de commande Azure 2.0 multiplateforme, qui est disponible pour Windows, Mac et Linux. Vous pouvez également effectuer ces étapes à l’aide du portail Azure ou d’Azure PowerShell.

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Enfin, pour publier votre zone DNS sur Internet, vous devez configurer les serveurs de noms du domaine. Chacune de ces étapes est décrite ci-dessous.

Ces instructions partent du principe que vous avez déjà installé Azure CLI 2.0 et que vous êtes connecté. Pour obtenir de l’aide, consultez [Gérer des zones DNS dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-dnszones-cli.md).

À présent, Azure DNS prend également en charge les zones DNS privées (actuellement en préversion publique). Pour en savoir plus sur les zones DNS privées, consultez la session relative à [l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md). Vous trouverez un exemple de création d’une zone DNS privée sur la page [Bien démarrer avec les zones privées Azure DNS à l’aide de CLI](./private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la zone DNS, un groupe de ressources est créé pour contenir la zone DNS. Voici la commande.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l'aide de la commande `az network dns zone create`. Pour consulter l’aide de cette commande, tapez `az network dns zone create -h`.

L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Pour créer un enregistrement DNS, utilisez la commande `az network dns record-set [record type] add-record`. Pour obtenir de l’aide, concernant les enregistrements A par exemple, consultez `azure network dns record-set A add-record -h`.

L’exemple suivant crée un enregistrement avec le nom relatif « www » dans la zone DNS « contoso.com », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », avec l’adresse IP « 1.2.3.4 », et une durée de vie par défaut de 3 600 secondes (1 heure) est utilisée.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Pour découvrir d’autres types d’enregistrements, des jeux d’enregistrements comportant plus d’un enregistrement et d’autres valeurs de durée de vie et pour modifier les enregistrements existants, consultez [Gérer les enregistrements DNS et les jeux d’enregistrement dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Mettre à jour les serveurs de noms

Une fois que vous jugez que votre zone et vos enregistrements DNS ont été configurés correctement, vous devez configurer votre nom de domaine pour utiliser les serveurs de noms Azure DNS. Cela permet à d’autres utilisateurs sur Internet de rechercher vos enregistrements DNS.

Les serveurs de noms de votre zone sont indiqués par la commande `az network dns zone show`. Pour afficher les noms de serveurs de noms, utilisez la sortie JSON, comme indiqué dans l’exemple suivant.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Ces serveurs de noms doivent être configurés avec le bureau d’enregistrement de noms de domaine (dans lequel vous avez acheté le nom de domaine). Votre bureau d’enregistrement offre la possibilité de configurer les serveurs de noms pour le domaine. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Supprimer toutes les ressources
 
Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

Pour en savoir plus sur la gestion des zones DNS dans Azure DNS, consultez [Gérer des zones DNS dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-dnszones-cli.md).

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gérer les enregistrements DNS et les jeux d’enregistrement dans Azure DNS à l’aide d’Azure CLI 2.0](dns-operations-recordsets-cli.md).
