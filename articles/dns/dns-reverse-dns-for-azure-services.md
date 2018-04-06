---
title: DNS inversés dans les services Azure | Microsoft Docs
description: Apprenez à configurer des recherches DNS inversées dans les services hébergés par Azure
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: bba2d75ed760a6e4eef2caacb0bb5924680b1f4b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Configurer des DNS inversés dans les services hébergés par Azure

Cet article explique comment configurer des recherches DNS inversées dans les services hébergés par Azure.

Les services Azure utilisent des adresses IP assignées par Azure et détenues par Microsoft. Ces enregistrements DNS inversés (enregistrements PTR) doivent être créés dans les zones de recherches DNS inversées correspondantes, détenues par Microsoft. Cet article explique comment y parvenir.

Il ne faut pas confondre ce scénario avec la capacité à [héberger les zones de recherche DNS inversées de vos plages d’adresses IP assignées dans le service Azure DNS](dns-reverse-dns-hosting.md). Ici, les plages d’adresses IP représentées par la zone de recherche inversée doivent être assignées à votre organisation. C’est généralement le rôle de votre FAI.

Avant de lire cet article, prenez connaissance de cette [Vue d’ensemble des DNS inversés et de la prise en charge dans Azure](dns-reverse-dns-overview.md).

Dans Azure DNS, les ressources de calcul (machines virtuelles, groupes de machines virtuelles identiques ou clusters Service Fabric) sont exposées via une ressource PublicIpAddress. Les recherche DNS inversées sont configurées à l’aide de la propriété 'ReverseFqdn' de la ressource PublicIpAddress.


Actuellement, les DNS inversés ne sont pas pris en charge dans Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Validation des enregistrements DNS inversés

Un tiers ne doit pas pouvoir créer d’enregistrements DNS inversés pour le mappage de son service Azure dans vos domaines DNS. Pour éviter cela, Azure n’autorise la création d’enregistrements DNS inversés que dans les cas où le nom de domaine spécifié dans l’enregistrement DNS inversé est identique au nom DNS ou à l’adresse IP dans la ressource PublicIpAddress ou le service Cloud au sein d’un même abonnement Azure.

Cette validation ne s’effectue que lorsqu’un enregistrement DNS inversé est défini ou modifié. Aucune nouvelle validation périodique n’est effectuée.

Par exemple : admettons que la ressource PublicIpAddress a pour nom DNS contosoapp1.northus.cloudapp.azure.com et pour adresse IP 23.96.52.53. La propriété ReverseFqdn de la ressource PublicIpAddress peut être définie comme suit :
* Nom DNS de la ressource PublicIpAddress : contosoapp1.northus.cloudapp.azure.com
* Nom DNS pour une ressource PublicIpAddress différente au sein du même abonnement. Par exemple : contosoapp2.westus.cloudapp.azure.com
* Nom DNS personnel, comme app1.contoso.com, à condition qu’il soit *d’abord* configuré comme CNAME pour contosoapp1.northus.cloudapp.azure.com ou pour une ressource PublicIpAddress différente au sein du même abonnement.
* Nom de domaine personnel, comme app1.contoso.com, à condition qu’il soit *d’abord* configuré comme un enregistrement A pour l’adresse IP 23.96.52.53 ou pour l’adresse IP d’une ressource PublicIpAddress différente au sein du même abonnement.

Les mêmes contraintes s’appliquent aux DNS inversés dans les services Cloud.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Les DNS inversés pour les ressources PublicIpAddress

Cette section détaille la marche à suivre pour configurer des DNS inversés pour des ressources PublicIpAddress dans le modèle de déploiement du Gestionnaire de ressources, à l’aide d’Azure PowerShell, Azure CLI 1.0 ou Azure CLI 2.0. Actuellement, la configuration de DNS inversés pour des ressources PublicIpAddress n’est pas prise en charge sur le portail Azure.

Actuellement, Azure ne prend en charge les DNS inversés que pour les ressources PublicIpAddress IPv4. Non pris en charge pour IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Ajouter un DNS inversé à des adresses IP publiques existantes

#### <a name="powershell"></a>PowerShell

Pour ajouter un DNS inversé à des adresses IP publiques existantes :

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

Pour ajouter un DNS inversé à des adresses IP publiques existantes :

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

Pour ajouter un DNS inversé à des adresses IP publiques existantes :

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Pour ajouter un DNS inversé à une adresse IP publique existante qui ne comporte pas encore de nom DNS, vous devez également spécifier un nom DNS :

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Création d’une adresse IP publique avec un DNS inversé

Pour créer une nouvelle adresse IP publique avec la propriété DNS inversée spécifiée :

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Afficher DNS inversé pour des adresses IP publiques existantes

Pour afficher la valeur définie d’une adresse IP publique existante :

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Suppression d’un enregistrement DNS inversé d’une adresse IP publique existante

Pour supprimer une propriété DNS inversée d’une adresse IP publique existante :

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Configurer un DNS inversé pour les services Cloud

Cette section détaille la marche à suivre pour configurer un DNS inversé pour les services Cloud dans le modèle de déploiement classique, à l’aide d’Azure PowerShell. La configuration de DNS inversé pour les services Cloud n’est pas possible via le portail Azure, Azure CLI 1.0 ou Azure CLI 2.0.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Ajout d’un DNS inversé aux services cloud existants

Pour ajouter un enregistrement DNS inversé à un service cloud existant :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Création d’un service cloud avec un DNS inversé

Pour créer un nouveau service cloud avec la propriété DNS inversée spécifiée :

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Affichage d’un DNS inversé pour les services cloud existants

Pour afficher la propriété DNS inversée pour un service cloud existant :

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Suppression d’un DNS inversé des services cloud existants

Pour supprimer la propriété DNS inversée d’un service cloud existant :

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Forum Aux Questions

### <a name="how-much-do-reverse-dns-records-cost"></a>Combien coûtent les enregistrements DNS inversés ?

Ils sont gratuits.  Les enregistrements DNS inversés ou les requêtes n’entraînent aucun frais supplémentaire.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Mes enregistrements DNS inversés seront-ils résolus à partir d'Internet ?

Oui. Dès que vous avez défini la propriété DNS inversée pour votre service Azure, Azure gère toutes les délégations DNS et les zones DNS requises pour s’assurer que l’enregistrement DNS inversé est résolu pour tous les utilisateurs d’Internet.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Des enregistrements DNS inversés par défaut sont-ils créés pour mes services Azure ?

Non. Le DNS inversé est une fonctionnalité optionnelle. Aucun enregistrement DNS inversé par défaut n’est créé si vous choisissez de ne pas les configurer.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Quel est le format du nom de domaine complet (FQDN) ?

Les noms de domaine complets sont spécifiés dans l’ordre chronologique et doivent se terminer par un point (par exemple, « app1.contoso.com. »).

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Que se passe-t-il si le test de validation pour le DNS inversé que j’ai spécifié échoue ?

Lorsque le test de validation pour le DNS inversé échoue, l’opération de configuration de l’enregistrement DNS inversé échoue également. Corrigez la valeur DNS inversée, puis réessayez.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Puis-je configurer un DNS inversé pour Azure App Service ?

Non. Le DNS inversé n’est pas pris en charge dans Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Puis-je configurer plusieurs enregistrements DNS inversés pour mon service Azure ?

Non. Azure ne prend en charge qu’un seul enregistrement DNS inversé pour chaque service cloud Azure ou adresse IP publique.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Puis-je configurer un DNS inversé pour des ressources PublicIpAddress IPv6 ?

Non. Actuellement, Azure ne prend en charge le DNS inversé que pour les ressources PublicIpAddress IPv4.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Puis-je envoyer des courriers électroniques à des domaines externes à partir d’Azure Compute Services ?

La possibilité technique d’envoyer un e-mail directement à partir d’un déploiement Azure technique varie selon le type d’abonnement. Quel que soit le type d’abonnement, Microsoft recommande d’utiliser des services de relais de courrier de confiance pour l’envoi de courrier sortant. Pour plus d’informations, voir [Sécurité d’Azure renforcée pour l’envoi de courrier – Mise à jour de novembre 2017](https://blogs.msdn.microsoft.com/mast/2017/11/15/enhanced-azure-security-for-sending-emails-november-2017-update/).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le DNS inversé, consultez [Recherche DNS inversée sur Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Découvrez comment [héberger la zone de recherche inversée pour la plage d’adresses IP fournie par votre fournisseur de services Internet dans Azure DNS](dns-reverse-dns-for-azure-services.md).

