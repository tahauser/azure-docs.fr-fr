---
title: "Configurer des filtres de routage pour l’homologation d’Azure ExpressRoute Microsoft : interface CLI | Microsoft Docs"
description: "Cet article décrit comment configurer des filtres de routage pour l’homologation Microsoft à l’aide de l’interface Azure CLI"
documentationcenter: na
services: expressroute
author: anzaman
manager: ganesr
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anzaman
ms.openlocfilehash: a85a68393f3dc946db651791de9efff0694f9989
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Configurer des filtres de routage pour l’homologation Microsoft : interface Azure CLI

> [!div class="op_single_selector"]
> * [portail Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interface de ligne de commande Azure](how-to-routefilter-cli.md)
> 

Les filtres de routage permettent d’utiliser un sous-ensemble de services pris en charge via l’homologation Microsoft. Les étapes décrites dans cet article vous aident à configurer et à gérer des filtres de routage pour les circuits ExpressRoute.

Les services Dynamics 365 services et les services Office 365, comme Exchange Online, SharePoint Online et Skype pour entreprises, sont accessibles via l’homologation Microsoft. Lorsque l’homologation Microsoft est configurée dans un circuit ExpressRoute, tous les préfixes liés à ces services sont publiés via les sessions BGP établies. Une valeur de communauté BGP est attachée à chaque préfixe pour identifier le service qui est proposé par le biais du préfixe. Pour obtenir la liste de valeurs de communauté BGP et des services auxquels elles sont mappées, consultez les [communautés BGP](expressroute-routing.md#bgp).

Si vous avez besoin de connectivité à tous les services, de nombreux préfixes sont publiés via BGP. Cela augmente considérablement la taille des tables de routage gérées par les routeurs au sein de votre réseau. Si vous envisagez d’utiliser uniquement un sous-ensemble des services offerts par le biais de l’homologation de Microsoft, vous pouvez réduire la taille de vos tables de routage de deux manières. Vous pouvez :

* Filtrer les préfixes indésirables en appliquant des filtres de routage sur les communautés BGP. Ceci est une pratique standard et très courante de mise en réseau.

* Définir des filtres de routage et les appliquer à votre circuit ExpressRoute. Un filtre de routage est une ressource qui vous permet de sélectionner la liste des services que vous envisagez d’utiliser via l’homologation Microsoft. Les routeurs ExpressRoute envoient uniquement la liste des préfixes qui appartiennent aux services identifiés dans le filtre de routage.

### <a name="about"></a>À propos des filtres de routage

Lorsque l’homologation Microsoft est configurée sur votre circuit ExpressRoute, les routeurs de périphérie Microsoft établissent une paire de sessions BGP avec les routeurs de périphérie (les vôtres ou ceux de votre fournisseur de connectivité). Aucun routage n’est publié sur votre réseau. Pour activer les annonces de routage sur votre réseau, vous devez associer un filtre de routage.

Un filtre de routage vous permet d’identifier les services que vous souhaitez utiliser via l’homologation Microsoft de votre circuit ExpressRoute. Il s’agit essentiellement d’une liste blanche de toutes les valeurs de communauté BGP. Une fois qu’une ressource de filtre de routage est définie et jointe à un circuit ExpressRoute, tous les préfixes qui mappent aux valeurs de communauté BGP sont publiés sur votre réseau.

Pour être en mesure de joindre des filtres de routage à des services Office 365, vous devez être autorisé à utiliser les services Office 365 via ExpressRoute. Si vous n’êtes pas autorisé à utiliser les services Office 365 via ExpressRoute, la jointure des filtres de routage échoue. Pour plus d’informations sur le processus d’autorisation, consultez [Azure ExpressRoute pour Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). La connectivité aux services Dynamics 365 ne nécessite pas d’autorisation préalable.

> [!IMPORTANT]
> L’homologation Microsoft des circuits ExpressRoute configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via l’homologation Microsoft, même si les filtres de routage ne sont pas définis. L’homologation Microsoft des circuits ExpressRoute configurés à compter du 1er août 2017 n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été joint au circuit.
> 
> 

### <a name="workflow"></a>Flux de travail

Pour pouvoir vous connecter aux services par le biais de l’homologation Microsoft, vous devez effectuer les étapes de configuration suivantes :

* Vous devez disposer d’un circuit ExpressRoute actif où l’homologation Microsoft est approvisionnée. Vous pouvez utiliser les instructions suivantes pour accomplir ces tâches :
  * [Créez un circuit ExpressRoute](howto-circuit-cli.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être approvisionné et activé.
  * [Créez l’homologation Microsoft](howto-routing-cli.md) si vous gérez la session BGP directement. Sinon, demandez à votre fournisseur de connectivité de configurer l’homologation Microsoft pour votre circuit.

* Vous devez créer et configurer un filtre de routage.
  * Identifiez les services que vous souhaitez utiliser via l’homologation Microsoft.
  * Identifiez la liste des valeurs de communauté BGP associées aux services.
  * Créez une règle pour autoriser la liste de préfixes correspondant aux valeurs de communauté BGP.

* Vous devez joindre le filtre de routage au circuit ExpressRoute.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer, installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installer l’interface de ligne de commande Azure 2.0) et [Get Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Prise en main de l’interface de ligne de commande Azure 2.0).

* Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être approvisionné et activé.

* Vous devez disposer d’une homologation Microsoft active. Suivez les instructions de [création et modification de la configuration d’homologation](howto-routing-cli.md).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement

Pour commencer votre configuration, connectez-vous à votre compte Azure. Utilisez les exemples suivants pour faciliter votre connexion :

```azurecli
az login
```

Vérifiez les abonnements associés au compte.

```azurecli
az account list
```

Sélectionnez l’abonnement pour lequel vous souhaitez créer un circuit ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Étape 1 : Obtenir la liste des préfixes et des valeurs de communauté BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenir la liste des valeurs de communauté BGP

Pour obtenir la liste des valeurs de communauté BGP liées aux services accessibles par le biais de l’homologation Microsoft et la liste des préfixes associés, utilisez la cmdlet suivante :

```azurecli
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Dresser la liste des valeurs que vous souhaitez utiliser

Dressez la liste des valeurs de communauté BGP que vous souhaitez utiliser dans le filtre de routage. Par exemple, la valeur de communauté BGP pour les services Dynamics 365 est 12076:5040.

## <a name="filter"></a>Étape 2 : Créer un filtre de routage et une règle de filtre

Un filtre de routage ne peut avoir qu’une seule règle, et cette règle doit être de type « Autoriser ». Cette règle peut être associée à une liste des valeurs de communauté BGP.

### <a name="1-create-a-route-filter"></a>1. Créer un filtre de routage

Commencez par créer le filtre de routage. La commande 'az network route-filter create' crée uniquement une ressource de filtre de routage. Après avoir créé la ressource, vous devez créer une règle et la joindre à l’objet de filtre de routage. Utilisez la commande suivante pour créer une ressource de filtre de routage :

```azurecli
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Créer une règle de filtre

Exécutez la commande suivante pour créer une règle :
 
```azurecli
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Étape 3 : Joindre le filtre de routage à un circuit ExpressRoute

Exécutez la commande suivante pour joindre le filtre de routage au circuit ExpressRoute :

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Tâches courantes

### <a name="getproperties"></a>Obtenir les propriétés d’un filtre de routage

Pour obtenir les propriétés d’un filtre de routage, utilisez la commande suivante :

```azurecli
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Mettre à jour les propriétés d’un filtre de routage

Si le filtre de routage est déjà joint à un circuit, les mises à jour de la liste de communautés BGP propagent automatiquement les modifications de publication de préfixe appropriées via les sessions BGP établies. Vous pouvez mettre à jour la liste de communautés BGP de votre filtre de routage à l’aide de la commande suivante :

```azurecli
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Détacher un filtre de routage d’un circuit ExpressRoute

Une fois qu’un filtre de routage est détaché du circuit ExpressRoute, aucun préfixe n’est publié via la session BGP. Vous pouvez détacher un filtre de routage d’un circuit ExpressRoute à l’aide de la commande suivante :

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Supprimer un filtre de routage

Vous ne pouvez supprimer un filtre de routage que s’il n’est attaché à aucun circuit. Assurez-vous que le filtre de routage n’est pas attaché à un circuit avant de tenter de le supprimer. Vous pouvez supprimer un filtre de routage à l’aide de la commande suivante :

```azurecli
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
