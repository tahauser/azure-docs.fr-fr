---
title: "Prise en main d’Azure DNS à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment créer une zone et un enregistrement DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide de PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 3f67d4f537a832d1d3e13ff2a67645ae17ed8669
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Prise en main d’Azure DNS à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [Portail Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Cet article vous indique la procédure à suivre pour créer votre première zone et votre premier enregistrement DNS à l’aide d’Azure PowerShell. Vous pouvez également effectuer ces étapes à l’aide du portail Azure ou de l’interface de ligne de commande Azure multiplateforme. Azure DNS prend également en charge la création de domaines privés. Pour obtenir des instructions détaillées sur la création de votre première zone DNS privée et enregistrement, consultez [Bien démarrer avec les zones privées Azure DNS à l’aide de PowerShell](private-dns-getstarted-powershell.md).

Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque enregistrement DNS pour votre domaine est ensuite créé à l’intérieur de cette zone DNS. Enfin, pour publier votre zone DNS sur Internet, vous devez configurer les serveurs de noms du domaine. Chacune de ces étapes est décrite ci-dessous.

Ces instructions supposent que vous avez déjà installé Azure PowerShell et que vous y êtes connecté. Pour obtenir de l’aide, consultez [Gestion des zones DNS à l’aide de PowerShell](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la zone DNS, un groupe de ressources est créé pour contenir la zone DNS. Voici la commande.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS est créée à l’aide de l’applet de commande `New-AzureRmDnsZone` . L’exemple ci-dessous crée une zone DNS appelée *contoso.com* dans le groupe de ressources *MyResourceGroup*. Utilisez l’exemple pour créer une zone DNS, en remplaçant les valeurs indiquées par vos propres valeurs.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Vous pouvez utiliser l’applet de commande `New-AzureRmDnsRecordSet` pour créer des jeux d’enregistrements. L’exemple suivant crée un enregistrement avec le nom relatif « www » dans la zone DNS « contoso.com », dans le groupe de ressources « MyResourceGroup ». Le nom complet du jeu d’enregistrements est « www.contoso.com ». Le type d’enregistrement est « A », avec l’adresse IP « 1.2.3.4 », et la durée de vie est de 3 600 secondes.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Pour découvrir d’autres types d’enregistrements et des jeux d’enregistrements comportant plus d’un enregistrement et pour modifier les enregistrements existants, consultez [Gérer les enregistrements et jeux d’enregistrements DNS dans Azure DNS à l’aide d’Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Affichage des enregistrements

Pour répertorier les enregistrements DNS dans votre zone, utilisez :

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Mettre à jour les serveurs de noms

Une fois que vous jugez que votre zone et vos enregistrements DNS ont été configurés correctement, vous devez configurer votre nom de domaine pour utiliser les serveurs de noms Azure DNS. Cela permet à d’autres utilisateurs sur Internet de rechercher vos enregistrements DNS.

Les serveurs de noms de votre zone sont indiqués par la cmdlet `Get-AzureRmDnsZone` :

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Ces serveurs de noms doivent être configurés avec le bureau d’enregistrement de noms de domaine (dans lequel vous avez acheté le nom de domaine). Votre bureau d’enregistrement offre la possibilité de configurer les serveurs de noms pour le domaine. Pour plus d’informations, consultez [Délégation de domaine à Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure DNS, consultez [Vue d’ensemble d’Azure DNS](dns-overview.md).

Pour en savoir plus sur la gestion des zones DNS dans Azure DNS, consultez [Gestion des zones DNS à l’aide de PowerShell](dns-operations-dnszones.md).

Pour en savoir plus sur la gestion des enregistrements DNS dans Azure DNS, consultez [Gérer les enregistrements et jeux d’enregistrements DNS dans Azure DNS à l’aide d’Azure PowerShell](dns-operations-recordsets.md).

