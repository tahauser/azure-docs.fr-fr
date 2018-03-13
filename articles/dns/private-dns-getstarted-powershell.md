---
title: "Prise en main des zones Azure DNS privées à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment créer un enregistrement et une zone DNS privée dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS privé à l’aide de PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 4613e152336eda7ce7cdc4c44b0c6b5e96abac10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Prise en main des zones Azure DNS privées à l’aide de PowerShell

Cet article vous indique la procédure à suivre pour créer votre premier enregistrement et votre première zone DNS privée à l’aide d’Azure PowerShell.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Pour publier une zone DNS privée sur votre réseau virtuel, vous spécifiez la liste des réseaux virtuels qui sont autorisés à résoudre les enregistrements dans la zone.  On parle de « réseaux de résolution ».  Vous pouvez également spécifier un ensemble de réseaux virtuels pour lesquels Azure DNS conserve les enregistrements de nom d’hôte chaque fois qu’une machine virtuelle est créée, change d’adresse IP ou est détruite.  On parle de « réseaux d’inscription ».

Comme cette fonctionnalité est actuellement une préversion managée, un module PowerShell de préversion est fourni lorsque vous envoyez par e-mail [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com).

## <a name="get-the-preview-powershell-module"></a>Obtenir le module PowerShell de préversion

Pour créer une zone DNS privée à l’aide de PowerShell, envoyez un e-mail à [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) pour obtenir le module PowerShell de préversion.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant la création de la zone DNS, un groupe de ressources est créé pour contenir la zone DNS. Voici la commande.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzureRmDnsZone` . L’exemple suivant crée une zone DNS appelée *contoso.local* dans le groupe de ressources appelé *MyResourceGroup* et s’assure que la zone DNS est disponible pour le réseau virtuel appelé *MyAzureVnet*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Si vous avez besoin d’Azure pour créer automatiquement des enregistrements de nom d’hôte dans la zone, utilisez le paramètre *RegistrationVirtualNetworkId* au lieu du paramètre *ResolutionVirtualNetworkId*.  Les réseaux virtuels d’inscription sont automatiquement activés pour la résolution.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Vous pouvez utiliser l’applet de commande `New-AzureRmDnsRecordSet` pour créer des jeux d’enregistrements. L’exemple suivant crée un enregistrement avec le nom relatif « db » dans la zone DNS « contoso.com », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », avec l’adresse IP « 10.0.0.4 », et la durée de vie est de 3 600 secondes.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Pour découvrir d’autres types d’enregistrements et des jeux d’enregistrements comportant plus d’un enregistrement et pour modifier les enregistrements existants, consultez [Gérer les enregistrements et jeux d’enregistrements DNS dans Azure DNS à l’aide d’Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les zones DNS privées, consultez la session relative à [l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md).

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gérer les enregistrements et jeux d’enregistrements DNS dans Azure DNS à l’aide d’Azure PowerShell](dns-operations-recordsets.md).

