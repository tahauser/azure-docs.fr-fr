---
title: "Délégation de votre domaine à Azure DNS | Microsoft Docs"
description: "Découvrez comment modifier la délégation de domaine et les serveurs de noms Azure DNS pour fournir l’hébergement d’un domaine."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: a13d9b360e2c43aa2a3d4f62215e4570ce42cd5b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="delegate-a-domain-to-azure-dns"></a>Délégation de domaine à Azure DNS

Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Pour que les requêtes DNS d’un domaine atteignent Azure DNS, le domaine doit être délégué à Azure DNS à partir du domaine parent. N’oubliez pas qu’Azure DNS n’est pas le bureau d’enregistrement de domaines. Cet article explique comment déléguer votre domaine à Azure DNS

Pour les domaines achetés auprès d’un bureau d’enregistrement de domaines, ce dernier offre la possibilité de définir les enregistrements NS. Pour créer une zone DNS avec un nom de domaine dans Azure DNS, vous ne devez pas nécessairement être propriétaire de ce domaine. Toutefois, vous avez besoin de posséder le domaine pour configurer la délégation à Azure DNS dans le bureau d’enregistrement de domaines.

Par exemple, supposons que vous achetez le domaine contoso.net et que vous créez une zone avec le nom contoso.net dans Azure DNS. En tant que propriétaire du domaine, votre bureau d’enregistrement vous permet de configurer les adresses de serveur de noms (par exemple, les enregistrements NS) pour votre domaine. Le bureau d’enregistrement stocke ces enregistrements NS dans le domaine parent, dans ce cas « .net ». Les clients du monde entier peuvent ensuite être redirigés vers votre domaine dans la zone Azure DNS lorsqu’ils tentent de résoudre des enregistrements DNS dans contoso.net.

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
1. Dans le menu **Hub**, sélectionnez **Nouveau** > **Mise en réseau** > **Zone DNS** pour ouvrir la page **Créer une zone DNS**.

   ![Zone DNS](./media/dns-domain-delegation/dns.png)

1. Sur la page **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :

   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|contoso.net|Indiquez le nom de la zone DNS.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la passerelle de l’application.|
   |**Groupe de ressources**|**Créer :** contosoRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Lieu**|États-Unis de l’Ouest||

> [!NOTE]
> L’emplacement du groupe de ressources n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

## <a name="retrieve-name-servers"></a>Récupérer les serveurs de noms

Avant de pouvoir déléguer votre zone DNS à Azure DNS, vous devez d’abord connaître les serveurs de noms correspondant à votre zone. Azure DNS alloue des serveurs de noms à partir d’un pool chaque fois qu’une zone est créée.

1. Une fois la zone DNS créée, accédez au panneau **Favoris** du portail Azure et sélectionnez **Toutes les ressources**. Sur la page **Toutes les ressources**, sélectionnez la zone DNS **contoso.net**. Si l’abonnement que vous avez sélectionné comporte déjà plusieurs ressources, vous pouvez saisir **contoso.net** dans la case **Filtrer par nom** pour accéder facilement à la passerelle d’application. 

1. Récupérez les serveurs de noms à partir de la page de la zone DNS. Dans cet exemple, la zone contoso.net a été attribuée aux serveurs de noms ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org et ns4-01.azure-dns.info :

   ![Liste des serveurs de noms](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS crée automatiquement des enregistrements NS faisant autorité dans une zone qui contient les serveurs de noms attribués. Pour afficher les serveurs de noms via Azure PowerShell ou l’interface de ligne de commande Azure, récupérez ces enregistrements.

Les exemples suivants présentent également les étapes permettant de récupérer les serveurs de noms d’une zone Azure DNS avec PowerShell et Azure CLI.

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

L’exemple suivant est la réponse :

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

L’exemple suivant est la réponse :

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>Déléguer le domaine

Maintenant que la zone DNS est créée et que vous disposez des serveurs de noms, vous devez mettre à jour le domaine parent avec les serveurs de noms Azure DNS. Chaque bureau d’enregistrement a ses propres outils de gestion DNS pour modifier les enregistrements de serveur de noms pour un domaine. Dans la page de gestion du bureau d’enregistrement DNS, modifiez les enregistrements NS et remplacez-les par ceux créés par Azure DNS.

Lors de la délégation d’un domaine à Azure DNS, vous devez utiliser les serveurs de noms fournis par Azure DNS. Nous vous recommandons d’utiliser les quatre serveurs de noms, quel que soit le nom de votre domaine. La délégation de domaine ne requiert pas que le serveur de noms utilise le même domaine de premier niveau que votre domaine.

N’utilisez pas *d’enregistrements de type glue* pour pointer vers les adresses IP de serveur de noms Azure DNS, car ces adresses IP peuvent changer ultérieurement. Les délégations utilisant les serveurs de noms dans votre propre zone (parfois appelés *serveurs de noms de redirection vers un microsite*) ne sont actuellement pas prises en charge dans Azure DNS.

## <a name="verify-that-name-resolution-is-working"></a>Vérifier que la résolution de noms fonctionne correctement

Une fois la délégation effectuée, vous pouvez vérifier que la résolution de noms fonctionne à l’aide d’un outil tel que nslookup pour interroger l’enregistrement SOA de votre zone (qui est créé automatiquement en même temps que la zone).

Il est inutile de spécifier les serveurs de noms Azure DNS. Si la délégation est correctement configurée, le processus de résolution DNS normal détecte automatiquement les serveurs de noms.

```
nslookup -type=SOA contoso.com
```

Voici un exemple de réponse à partir de la commande précédente :

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-subdomains-in-azure-dns"></a>Déléguer des sous-domaines dans Azure DNS

Si vous souhaitez configurer une zone enfant distincte, vous pouvez déléguer un sous-domaine dans Azure DNS. Par exemple, supposons que vous avez configuré et délégué le domaine contoso.net dans Azure DNS. Vous souhaitez maintenant configurer une zone enfant distincte, partners.contoso.net.

1. Créez la zone enfant partners.contoso.net dans Azure DNS.
2. Recherchez les enregistrements NS faisant autorité dans la zone enfant pour obtenir les serveurs de noms qui hébergent la zone enfant dans Azure DNS.
3. Déléguez la zone enfant en configurant les enregistrements NS de la zone parent pour qu’ils pointent vers la zone enfant.

### <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
1. Dans le menu **Hub**, sélectionnez **Nouveau** > **Mise en réseau** > **Zone DNS** pour ouvrir la page **Créer une zone DNS**.

   ![Zone DNS](./media/dns-domain-delegation/dns.png)

1. Sur la page **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :

   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|partners.contoso.net|Indiquez le nom de la zone DNS.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la passerelle de l’application.|
   |**Groupe de ressources**|**Use Existing :** (Utiliser existant) contosoRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Lieu**|États-Unis de l’Ouest||

> [!NOTE]
> L’emplacement du groupe de ressources n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

### <a name="retrieve-name-servers"></a>Récupérer les serveurs de noms

1. Une fois la zone DNS créée, accédez au panneau **Favoris** du portail Azure et sélectionnez **Toutes les ressources**. Sélectionnez la zone DNS **partners.contoso.net** sur la page **Toutes les ressources**. Si l’abonnement que vous avez sélectionné comporte déjà plusieurs ressources, vous pouvez saisir **partners.contoso.net** dans la case **Filtrer par nom** pour accéder facilement à la zone DNS.

1. Récupérez les serveurs de noms à partir de la page de la zone DNS. Dans cet exemple, la zone contoso.net a été attribuée aux serveurs de noms ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org et ns4-01.azure-dns.info :

   ![Liste des serveurs de noms](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS crée automatiquement des enregistrements NS faisant autorité dans une zone qui contient les serveurs de noms attribués.  Pour afficher les serveurs de noms via Azure PowerShell ou l’interface de ligne de commande Azure, récupérez ces enregistrements.

### <a name="create-a-name-server-record-in-the-parent-zone"></a>Créer un enregistrement de serveur de noms dans la zone parent

1. Accédez à la zone DNS **contoso.net** dans le portail Azure.
1. Sélectionnez **+ Jeu d’enregistrements**.
1. Sur la page **Ajouter un jeu d’enregistrements**, saisissez les valeurs suivantes, puis sélectionnez **OK** :

   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|partenaires|Saisissez le nom de la zone DNS enfant.|
   |**Type**|NS|Utilisez NS pour les enregistrements de serveur de noms.|
   |**TTL**|1|Indiquez la durée de vie.|
   |**Unité de durée de vie**|Heures|Définissez l’unité de durée de vie sur Heures.|
   |**SERVEUR DE NOMS**|{serveurs de noms à partir de la zone partners.contoso.net}|Saisissez les quatre serveurs de noms à partir de la zone partners.contoso.net. |

   ![Valeurs de l’enregistrement de serveur de noms](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegate-subdomains-in-azure-dns-by-using-other-tools"></a>Déléguer des sous-domaines dans Azure DNS à l’aide d’autres outils

Les exemples suivants présentent les étapes permettant de déléguer des sous-domaines dans Azure DNS à l’aide de PowerShell et de l’interface de ligne de commande Azure.

#### <a name="powershell"></a>PowerShell

L’exemple PowerShell suivant illustre cette procédure. Vous pouvez exécuter les mêmes étapes à l’aide du portail Azure ou de l’interface de ligne de commande Azure multiplateforme.

```powershell
# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This information contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name (in this case, "partners").
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

Utilisez `nslookup` pour vérifier que tout est correctement configuré en recherchant l’enregistrement SOA de la zone enfant.

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

Récupérez les serveurs de noms de la zone `partners.contoso.net` à partir de la sortie.

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Créez le jeu d’enregistrements et les enregistrements NS pour chaque serveur de noms.

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create an NS record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

1. Allez dans le panneau **Favoris** du portail Azure, puis sélectionnez **Toutes les ressources**. Sur la page **Toutes les ressources**, sélectionnez le groupe de ressources **contosorg**. Si l’abonnement que vous avez sélectionné comporte déjà plusieurs ressources, vous pouvez saisir **contosorg** dans la case **Filtrer par nom** pour accéder facilement au groupe de ressources.
1. Sur la page **contosorg**, cliquez sur le bouton **Supprimer**.
1. Le portail nécessite que vous saisissiez le nom du groupe de ressources pour confirmer la suppression. Saisissez **contosorg** comme nom du groupe de ressources, puis cliquez sur **Supprimer**. 

La suppression d’un groupe de ressources supprime toutes les ressources qu’il contient. Vérifiez toujours le contenu d’un groupe de ressources avant de le supprimer. Le portail supprime toutes les ressources du groupe de ressources, puis supprime le groupe de ressources lui-même. Cette opération prend plusieurs minutes.

## <a name="next-steps"></a>Étapes suivantes

[Gestion des zones DNS](dns-operations-dnszones.md)

[Gestion des enregistrements DNS](dns-operations-recordsets.md)
