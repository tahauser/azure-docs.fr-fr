---
title: "Créer et modifier un circuit Azure ExpressRoute : CLI | Microsoft Docs"
description: "Cet article explique comment créer, approvisionner, vérifier, mettre à jour, supprimer et déprovisionner un circuit ExpressRoute à l’aide de l’interface de ligne de commande."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Créer et modifier un circuit ExpressRoute à l’aide de l’interface de ligne de commande


Cet article décrit comment créer un circuit Azure ExpressRoute à l’aide de l’interface de ligne de commande (CLI). Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer et déprovisionner un circuit. Si vous souhaitez utiliser une autre méthode pour les circuits ExpressRoute, vous pouvez sélectionner un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Interface de ligne de commande Azure](howto-circuit-cli.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Avant de commencer

* Avant de commencer, installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installer l’interface de ligne de commande Azure 2.0) et [Get Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Prise en main de l’interface de ligne de commande Azure 2.0).
* Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

## <a name="create"></a>Créer et approvisionner un circuit ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Vous connecter à votre compte Azure et sélectionner votre abonnement

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

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Récupérer la liste des fournisseurs, des emplacements et des bandes passantes pris en charge

Avant de créer un circuit ExpressRoute, vous avez besoin d’une liste des fournisseurs de services, des emplacements et des options de bande passante pris en charge. La commande CLI « az network express-route list-service-providers » renvoie ces informations, que vous allez utiliser dans les étapes suivantes :

```azurecli
az network express-route list-service-providers
```

La réponse ressemble à ce qui suit :

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Vérifiez la réponse pour voir si votre fournisseur de connectivité est référencé. Prenez note des éléments suivants, car vous en aurez besoin lors de la création d’un circuit :

* Nom
* PeeringLocations
* BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Création d’un circuit ExpressRoute

> [!IMPORTANT]
> Votre circuit ExpressRoute est facturé à partir de l’émission d'une clé de service. Effectuez cette opération quand le fournisseur de connectivité est prêt à approvisionner le circuit.
> 
> 

Si vous n’avez pas déjà un groupe de ressources, vous devez en créer un avant de créer votre circuit ExpressRoute. Vous pouvez créer un groupe de ressources à l’aide de la commande suivante :

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

L’exemple suivant montre comment créer un circuit ExpressRoute de 200 Mb/s par le biais d’Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, utilisez ces informations quand vous créez votre requête. 

Assurez-vous que vous spécifiez le niveau de référence (SKU) et la famille de références corrects :

* Le niveau de référence détermine si ExpressRoute standard ou un module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier « Standard » pour obtenir la référence SKU standard ou « Premium » pour le module complémentaire Premium.
* La famille de références détermine le type de facturation. Vous pouvez spécifier « Metereddata » pour définir un forfait de données limité et « Unlimiteddata » pour un forfait de données illimité. Vous pouvez remplacer le type de facturation « Metereddata » par « Unlimiteddata », mais que vous ne pouvez pas le changer de « Unlimiteddata » en « Metereddata ».


Votre circuit ExpressRoute est facturé à partir de l’émission d'une clé de service. Voici un exemple de demande pour une nouvelle clé de service :

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

La réponse contient la clé de service.

### <a name="4-list-all-expressroute-circuits"></a>4. Répertorier tous les circuits ExpressRoute

Pour obtenir la liste de tous les circuits ExpressRoute que vous avez créés, exécutez la commande « az network express-route list ». Vous pouvez récupérer ces informations à tout moment à l’aide de cette commande. Pour répertorier tous les circuits, effectuez l’appel sans paramètres.

```azurecli
az network express-route list
```

Votre clé de service apparaît dans le champ *ServiceKey* de la réponse.

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande avec le paramètre « -h ».

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement

« ServiceProviderProvisioningState » fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. Le statut indique l’état du côté Microsoft. Pour plus d’informations, consultez l’article sur les [workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous :

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Vérifier régulièrement le statut et l’état de la clé du circuit

La vérification du statut et de l’état de la clé du circuit vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, « ServiceProviderProvisioningState » a la valeur « Approvisionné », comme le montre l’exemple suivant :

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

La réponse ressemble à ce qui suit :

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Créer votre configuration de routage

Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute](howto-routing-cli.md) pour créer et modifier des homologations de circuit.

> [!IMPORTANT]
> Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Lier un réseau virtuel à un circuit ExpressRoute

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article sur la [liaison de réseaux virtuels à des circuits ExpressRoute](howto-linkvnet-cli.md).

## <a name="modify"></a>Modification d’un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité. Vous pouvez apporter les modifications suivantes sans temps d'arrêt :

* Vous pouvez activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
* Vous pouvez augmenter la bande passante de votre circuit ExpressRoute à condition que la capacité disponible sur le port le permette. Cependant, la rétrogradation de la bande passante d'un circuit n'est pas prise en charge. 
* Vous pouvez remplacer le plan de mesure Données limitées par Données illimitées. Cependant, le remplacement du plan de mesure Données illimitées par Données limitées n’est pas pris en charge.
* Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le module complémentaire ExpressRoute Premium

Vous pouvez activer le module complémentaire ExpressRoute Premium pour votre circuit existant à l’aide de la commande suivante :

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Les fonctionnalités du module complémentaire ExpressRoute Premium sont ainsi activées pour votre circuit. Nous commençons à vous facturer la fonctionnalité du module complémentaire Premium dès que la commande a été exécutée avec succès.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le module complémentaire ExpressRoute Premium

> [!IMPORTANT]
> Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.
> 
> 

Avant de désactiver le module complémentaire ExpressRoute Premium, comprenez les critères suivants :

* Avant de rétrograder du niveau Premium à Standard, vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10. S’il y en a plus de 10, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.
* Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.
* Pour l’homologation privée, votre table de routage doit comporter moins de 4 000 routages. Si elle contient plus de 4 000 routages, la session BGP est supprimée. Cette session ne sera réactivée qu’une fois le nombre de préfixes publiés repassé sous la barre des 4 000.

Vous pouvez désactiver le module complémentaire ExpressRoute Premium dans votre circuit existant à l’aide de l’exemple suivant :

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Pour mettre à jour la bande passante d’un circuit ExpressRoute

Pour connaître les options de bande passante prises en charge par votre fournisseur, consultez le [FAQ ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n'importe quelle taille supérieure à la taille de votre circuit existant.

> [!IMPORTANT]
> Vous devrez peut-être recréer le circuit ExpressRoute si la capacité sur le port existant est inappropriée. Vous ne pouvez pas mettre le circuit à niveau si aucune capacité supplémentaire n’est disponible à cet emplacement.
>
> Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à déprovisionner le circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.
>

Une fois que vous avez décidé de la taille dont vous avez besoin, utilisez la commande suivante pour redimensionner votre circuit :

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Microsoft se charge de redimensionner votre circuit. Vous devrez ensuite contacter votre fournisseur de connectivité pour qu’il mette à jour les configurations de son côté afin de refléter cette modification. Une fois cette modification apportée, nous commençons à vous facturer pour l’option de bande passante mise à jour.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Pour modifier la référence de limité à illimité

Vous pouvez modifier la référence SKU d'un circuit ExpressRoute à l'aide de l'exemple suivant :

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Pour contrôler l'accès aux environnements classique et Resource Manager

Consultez les instructions dans [Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Annulation de l’approvisionnement et suppression d’un circuit ExpressRoute

Pour déprovisionner et supprimer un circuit ExpressRoute, veillez à bien comprendre les critères suivants :

* Vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.
* Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est **En cours d’approvisionnement** ou **Approvisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour déprovisonner le circuit de son côté. Nous continuons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.
* Vous pouvez supprimer le circuit si le fournisseur de services a déprovisionné le circuit. Lorsqu’un circuit est déprovisionné, l’état d’approvisionnement du fournisseur de services a la valeur **Non approvisionné**. Cette opération arrête la facturation du circuit.

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé votre circuit, effectuez les tâches suivantes :

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](howto-routing-cli.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](howto-linkvnet-cli.md)