---
title: "Présentation de DNS dans Azure Stack | Microsoft Docs"
description: "Présentation des fonctionnalités DNS dans Azure Stack"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 86ed2805e93bd147841e22a773b52d1451f8c353
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="introducing-idns-for-azure-stack"></a>Présentation d’iDNS pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

iDNS est une fonctionnalité d’Azure Stack qui vous permet de résoudre les noms DNS externes (par exemple, http://www.bing.com).
Elle vous permet également d’enregistrer des noms de réseau virtuel interne. De cette façon, vous pouvez résoudre des machines virtuelles sur le même réseau virtuel par nom plutôt que par adresse IP, sans avoir à fournir des entrées de serveur DNS personnalisées.

Cette fonctionnalité a toujours été présente dans Azure, mais elle est également disponible dans Windows Server 2016 et Azure Stack.

## <a name="what-does-idns-do"></a>Que fait iDNS ?
Avec iDNS dans Azure Stack, vous obtenez les fonctionnalités suivantes, sans avoir à spécifier d’entrées de serveur DNS personnalisées.

* Services de résolution de noms DNS partagés pour les charges de travail de locataire.
* Service DNS faisant autorité pour la résolution de noms et l’enregistrement DNS dans le réseau virtuel du locataire.
* Service DNS récursif pour la résolution de noms Internet à partir de machines virtuelles de locataire. Les locataires n’ont plus besoin de spécifier des entrées DNS personnalisées pour résoudre les noms Internet (par exemple, www.bing.com).

Vous pouvez toujours configurer votre propre DNS et utiliser des serveurs DNS personnalisés si vous le souhaitez. Toutefois, à présent, si vous voulez simplement résoudre les noms DNS Internet et vous connecter à d’autres machines virtuelles dans le même réseau virtuel, vous n’avez besoin de spécifier aucun paramètre pour pouvoir le faire.

## <a name="what-does-idns-not-do"></a>Que ne fait pas iDNS ?
iDNS ne vous permet pas de créer un enregistrement DNS pour un nom qui peut être résolu en dehors du réseau virtuel.

Dans Azure, vous avez la possibilité de spécifier une étiquette de nom DNS pouvant être associée à une adresse IP publique. Vous pouvez choisir l’étiquette (préfixe), mais Azure choisit le suffixe, qui est basé sur la région dans laquelle vous créez l’adresse IP publique.

![Capture d’écran de l’étiquette de nom DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Dans l’image ci-dessus, Azure crée un enregistrement « A » dans DNS pour l’étiquette du nom DNS spécifiée dans la zone **westus.cloudapp.azure.com**. Le préfixe et le suffixe ensemble représentent un nom de domaine complet (FQDN) pouvant être résolu n’importe où sur le réseau Internet public.

Azure Stack prend en charge iDNS uniquement pour l’enregistrement de noms internes. Dans ce sens, il ne peut pas effectuer les opérations suivantes :

* Créer un enregistrement DNS dans une zone DNS existante hébergée (par exemple, local.azurestack.external).
* Créer une zone DNS (par exemple, Contoso.com).
* Créer un enregistrement dans votre propre zone DNS personnalisée.
* Prendre en charge l’achat de noms de domaine.

