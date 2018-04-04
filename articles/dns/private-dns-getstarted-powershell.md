---
title: Prise en main des zones Azure DNS privées à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment créer un enregistrement et une zone DNS privée dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS privé à l’aide de PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Prise en main des zones Azure DNS privées à l’aide de PowerShell

Cet article vous indique la procédure à suivre pour créer votre premier enregistrement et votre première zone DNS privée à l’aide d’Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Pour publier une zone DNS privée sur votre réseau virtuel, vous spécifiez la liste des réseaux virtuels qui sont autorisés à résoudre les enregistrements dans la zone.  On parle de « réseaux virtuels de résolution ».  Vous pouvez également spécifier un réseau virtuel pour lequel Azure DNS conserve les enregistrements de nom d’hôte chaque fois qu’une machine virtuelle est créée, change d’adresse IP ou est détruite.  Nous appelons cela un « réseau virtuel d’enregistrement ».

# <a name="get-the-preview-powershell-modules"></a>Obtenir les modules PowerShell en préversion
Ces instructions supposent que vous avez déjà installé le module et que vous êtes connecté à Azure PowerShell, y compris que vous disposez des modules requis pour la fonctionnalité Zone privée. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant la création de la zone DNS, un groupe de ressources est créé pour contenir la zone DNS. L’exemple suivant montre la commande.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Créer une zone DN privée DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzureRmDnsZone` avec une valeur de « Private » pour le paramètre ZoneType. L’exemple suivant crée une zone DNS appelée *contoso.local* dans le groupe de ressources appelé *MyResourceGroup* et s’assure que la zone DNS est disponible pour le réseau virtuel appelé *MyAzureVnet*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

Notez que si le paramètre ZoneType est omis, la Zone est créée en tant que Zone publique, par conséquent, il est requis si vous avez besoin de créer une Zone privée. 

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

# <a name="list-dns-private-zones"></a>Répertorier les zones privées DNS

En omettant le nom de la zone dans `Get-AzureRmDnsZone`, vous pouvez énumérer toutes les zones d’un groupe de ressources. Cette opération renvoie un tableau d’objets de la zone.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

En omettant le nom de zone et le nom du groupe de ressources de `Get-AzureRmDnsZone`, vous pouvez énumérer toutes les zones dans l’abonnement Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Mettre à jout une zone privée DNS

Vous pouvez apporter des modifications à une ressource de zone DNS à l’aide de `Set-AzureRmDnsZone`. Cette applet de commande ne met pas à jour les jeux d’enregistrements DNS dans la zone (voir [Gestion des enregistrements DNS](dns-operations-recordsets.md)). Elle est utilisée seulement pour mettre à jour les propriétés de la ressource de zone elle-même. Les propriétés de la zone accessible en écriture sont actuellement limitées aux [balises Azure Resource Manager pour la ressource de la zone](dns-zones-records.md#tags), ainsi que les paramètres RegistrationVirtualNetworkId et ResolutionVirtualNetworkId pour les Zones privées.

L’exemple ci-dessous remplace le réseau virtuel d’enregistrement lié à une zone par un nouveau réseau nommé MyNewAzureVnet.

Notez que vous ne devez pas spécifier le paramètre ZoneType pour la mise à jour, contrairement à la création. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

L’exemple ci-dessous remplace le réseau virtuel de résolution lié à une zone par un nouveau réseau nommé MyNewAzureVnet.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Supprimer une zone privée DNS

Les zones privées DNS peuvent être supprimées à l’aide de l’applet de commande `Remove-AzureRmDnsZone` tout comme les zones publiques.

> [!NOTE]
> Supprimer une zone DNS supprime également tous les enregistrements DNS de la zone. Il est impossible d’annuler cette opération. Si la zone DNS est en cours d’utilisation, les services utilisant la zone échouent lors de la suppression de la zone.
>
>Pour vous protéger contre la suppression accidentelle de zones, consultez la page [Comment protéger des zones et enregistrements DNS](dns-protect-zones-recordsets.md).

Utilisez l’une des options suivantes pour supprimer une zone DNS :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Spécifier la zone en utilisant le nom de zone et le nom de groupe de ressources

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone en utilisant un objet $zone

Vous pouvez spécifier la zone à supprimer à l’aide d’un objet `$zone` retourné par `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

L’objet de zone peut également être redirigé au lieu d’être transmis en tant que paramètre :

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Invites de confirmation

Les applets de commande `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` et `Remove-AzureRmDnsZone` prennent en charge les invites de confirmation.

Les invites `New-AzureRmDnsZone` et `Set-AzureRmDnsZone` demandent une confirmation si la variable de préférence PowerShell `$ConfirmPreference` a la valeur `Medium` ou une valeur inférieure. En raison de l’impact potentiellement élevé de la suppression d’une zone DNS, l’applet de commande `Remove-AzureRmDnsZone` vous demande de confirmer si la variable PowerShell `$ConfirmPreference` a une valeur autre que `None`.

Étant donné que la valeur par défaut `$ConfirmPreference` est `High`, seule la commande `Remove-AzureRmDnsZone` demande une confirmation par défaut.

Vous pouvez remplacer le paramétrage actuel de `$ConfirmPreference` par le paramètre `-Confirm`. Si vous spécifiez les paramètres `-Confirm` ou `-Confirm:$True`, les applets de commande vous invitent à confirmer l’exécution. Si vous spécifiez le paramètre `-Confirm:$False`, l’applet de commande ne demande pas de confirmation.

Pour plus d’informations sur les paramètres `-Confirm` et `$ConfirmPreference`, voir [À propos des variables de préférence](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les zones DNS privées, consultez la session relative à [l’utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md).

Consultez certains scénarios courants [Scénarios de zones privées](./private-dns-scenarios.md) qui peuvent être réalisés avec des Zones privées dans Azure DNS.

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gérer les enregistrements et jeux d’enregistrements DNS dans Azure DNS à l’aide d’Azure PowerShell](dns-operations-recordsets.md).

