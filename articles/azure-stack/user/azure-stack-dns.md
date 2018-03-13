---
title: DNS dans Azure Stack | Microsoft Docs
description: DNS dans Azure Stack
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 394abe5295af4ed99e48d50b5886ac93af87e875
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="dns-in-azure-stack"></a>DNS dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack inclut les fonctionnalités DNS suivantes :
* Prise en charge de la résolution de nom d’hôte DNS
* Création et gestion des enregistrements et zones DNS à l’aide d’API

## <a name="support-for-dns-hostname-resolution"></a>Prise en charge de la résolution de nom d’hôte DNS
Vous pouvez spécifier une étiquette de nom de domaine DNS pour une ressource IP publique, qui crée un mappage pour l’élément *domainnamelabel.location*.cloudapp.azurestack.external vers l’adresse IP publique sur les serveurs DNS gérés par Azure Stack.  

Par exemple, si vous créez une ressource IP publique avec **contoso** comme étiquette de nom de domaine à l’emplacement Azure Stack local, le nom de domaine complet (FQDN) **contoso.local.cloudapp.azurestack.external** est résolu à l’adresse IP publique de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure Stack.

> [!IMPORTANT]
> Chaque étiquette de nom de domaine créée doit être unique dans son emplacement Azure Stack.

Si vous créez l’adresse IP publique à l’aide du portail, elle ressemble à ceci :

![Création d’une adresse IP publique](media/azure-stack-whats-new-dns/image01.png)

Cette configuration est utile si vous souhaitez associer une adresse IP publique avec une ressource à charge équilibrée. Par exemple, vous pouvez avoir un équilibreur de charge traitant les demandes à partir d’une application web. Derrière l’équilibreur de charge se trouve un site web installé sur une ou plusieurs machines virtuelles. Maintenant vous pouvez accéder au site web avec équilibrage de charge à l’aide d’un nom DNS plutôt qu’une adresse IP.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>Créer et gérer des enregistrements et zones DNS à l’aide d’API
Vous pouvez créer et gérer des enregistrements et des zones DNS dans Azure Stack.  

Azure Stack fournit un service DNS comme celui d’Azure, à l’aide d’API qui sont cohérentes avec les API DNS Azure.  En hébergeant vos domaines dans le système DNS Azure Stack, vous pouvez gérer vos enregistrements DNS avec les mêmes éléments que vos autres services Azure : informations d’identification, API, outils, facturation et support. 

Pour des raisons évidentes, l’infrastructure DNS Azure Stack est plus compacte que celle d’Azure. La portée, l’échelle et les performances dépendent donc de l’échelle du déploiement Azure Stack et de l’environnement dans lequel il est déployé.  Ainsi, des éléments tels que les performances, la disponibilité, la distribution globale et la haute disponibilité peuvent varier d’un déploiement à l’autre.

## <a name="comparison-with-azure-dns"></a>Comparaison avec le système DNS Azure
Le système DNS dans Azure Stack est semblable au système DNS dans Azure, avec deux exceptions majeures :
* **Il ne prend pas en charge les enregistrements AAAA**

    Azure Stack ne prend pas en charge les enregistrements AAAA, car il ne prend pas en charge les adresses IPv6.  Il s’agit d’une différence essentielle entre les systèmes DNS Azure et Azure Stack.
* **Il n’est pas multilocataire**

    Contrairement à Azure, le service DNS dans Azure Stack n’est pas multilocataire. Par conséquent, chaque client ne peut pas créer la même zone DNS. Seul le premier abonnement qui tente de créer la zone y parvient, et les requêtes suivantes échouent.  Il s’agit d’un problème connu et d’une différence essentielle entre le système DNS dans Azure et dans Azure Stack. Ce problème sera résolu dans une version ultérieure.

En outre, il existe des différences mineures dans la façon dont le système DNS Azure Stack implémente les balises, les métadonnées, les ETags et les limites.

Les informations suivantes s’appliquent spécifiquement au système DNS Azure Stack, et varient légèrement par rapport à Azure. Pour en savoir plus sur le système DNS Azure, consultez [Zones et enregistrements Etags](../../dns/dns-zones-records.md) sur le site de documentation de Microsoft Azure.

### <a name="tags-metadata-and-etags"></a>Balises, métadonnées et ETags

**Balises**

Le système DNS Azure Stack prend en charge l’utilisation de balises Azure Resource Manager sur des ressources de zone DNS. Il ne prend pas en charge les balises sur les jeux d’enregistrements DNS, bien que l’alternative « métadonnées » soit prise en charge sur les jeux d’enregistrements DNS comme expliqué ci-dessous.

**Métadonnées**

À la place de balises de jeu d’enregistrements, le système DNS Azure Stack prend en charge l’annotation des jeux d’enregistrements à l’aide de « métadonnées ». Comme des balises, les métadonnées permettent d’associer des paires nom-valeur à chaque jeu d’enregistrements. Cela peut être utile, par exemple pour indiquer l’objectif de chaque jeu d’enregistrements. Contrairement aux balises, vous ne pouvez pas utiliser les métadonnées pour produire une vue filtrée de votre facture Azure. Et vous ne pouvez pas non plus en spécifier dans une stratégie Azure Resource Manager.

**ETags**

Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’il a remplacé les modifications créées par quelqu’un d’autre ?

Le système DNS Azure Stack utilise des ETags pour gérer les modifications simultanées de la même ressource en toute sécurité. Les ETags sont différents des « Balises » Azure Resource Manager. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son Etag l’est également. Lors de la mise à jour d’une ressource, vous pouvez choisir de retransmettre l’ETag pour que le système DNS Azure Stack vérifie que l’ETag qui se trouve sur le serveur correspond. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les Etags sont également utilisés lorsque vous créez une ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, PowerShell dans le système DNS Azure Stack utilise des ETags pour bloquer les modifications simultanées apportées à des zones et des jeux d’enregistrements. Le commutateur facultatif *-Overwrite* peut être utilisé pour supprimer les vérifications d’Etags, auquel cas toutes les modifications simultanées qui se sont produites sont remplacées.

Au niveau de l’API REST du système DNS Azure Stack, les ETags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est indiqué dans le tableau suivant :

| En-tête | Comportement|
|--------|---------|
| Aucun   | PUT réussit toujours (aucune vérification Etag)|
| If-match| PUT ne réussit que si la ressource existe et que l’Etag correspond|
| If-match *| PUT réussit seulement si la ressource existe|
| If-none-match *| PUT réussit seulement si la ressource n’existe pas|

### <a name="limits"></a>limites

Les limites par défaut suivantes s’appliquent lors de l’utilisation du système DNS Azure Stack :

| Ressource| Limite par défaut|
|---------|--------------|
| Zones par abonnement| 100|
| Jeux d’enregistrements par zone| 5 000|
| Enregistrements par jeu d’enregistrements| 20|

## <a name="next-steps"></a>Étapes suivantes
[Introduction aux noms de domaine internationaux (IDN) pour Azure Stack](azure-stack-understanding-dns.md)
