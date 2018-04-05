---
title: Configuration du routage d’un circuit Azure ExpressRoute avec l’interface CLI | Microsoft Docs
description: Cet article est conçu pour vous aider à créer et à approvisionner l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer des homologations pour votre circuit.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2017
ms.author: cherylmc
ms.openlocfilehash: f4ad959de1425e828ce11fb658f8b5304e9d8775
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-modify-routing-for-an-expressroute-circuit-using-cli"></a>Créer et modifier le routage d’un circuit ExpressRoute à l’aide de l’interface CLI

Cet article est conçu pour vous aider à créer et à gérer la configuration du routage d’un circuit ExpressRoute dans le modèle de déploiement Resource Manager à l’aide de l’interface CLI. Vous pouvez également vérifier l’état, mettre à jour ou supprimer et déprovisionner des homologations d’un circuit ExpressRoute. Si vous souhaitez utiliser une autre méthode pour votre circuit, sélectionnez un article dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [interface de ligne de commande Azure](howto-routing-cli.md)
> * [Vidéo - Homologation privée](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Avant de commencer, installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI consultez l’article [Installer l’interface de ligne de commande Azure 2.0](/cli/azure/install-azure-cli).
* Veillez à consulter les articles relatifs à la [configuration requise pour ExpressRoute](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [workflows](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être dans un état approvisionné et activé pour être en mesure d’exécuter les commandes de cet article.

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. Pour plus d’informations sur les domaines de routage et les homologations, consultez [Domaines de routage ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Homologation Microsoft

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation Microsoft pour un circuit ExpressRoute.

> [!IMPORTANT]
> L’homologation Microsoft des circuits ExpressRoute qui ont été configurés avant le 1er août 2017 entraînera la publication de tous les préfixes de service via l’homologation Microsoft, même si aucun filtre d’itinéraire n’est défini. L’homologation Microsoft des circuits ExpressRoute qui sont configurés le 1er août 2017 ou après n’entraînera la publication d’aucun préfixe tant qu’un filtre de routage n’aura pas été attaché au circuit. Pour plus d’informations, consultez [Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md) (Configurer un filtre d’itinéraire pour l’homologation Microsoft).
> 
> 

### <a name="to-create-microsoft-peering"></a>Pour créer une homologation Microsoft

1. Installez la dernière version de l’interface Azure CLI. Utilisez la dernière version de l’interface de ligne de commande (CLI) Azure.* Prenez connaissance de la [configuration requise](expressroute-prerequisites.md) et des [workflows](expressroute-workflows.md) avant de commencer la configuration.

  ```azurecli
  az login
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation Microsoft pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit. 

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

  ```azurecli
  az network express-route list
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
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Configurez l’homologation Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.

  * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
  * Préfixes publiés : vous devez fournir une liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
  * **(Facultatif)** ASN client : si vous publiez des préfixes non enregistrés dans le numéro de système autonome d’homologation, vous pouvez spécifier le numéro de système autonome avec lequel ils sont enregistrés.
  * Nom du registre de routage : vous pouvez spécifier les registres RIR/IRR par rapport auxquels le numéro AS et les préfixes sont enregistrés.
  * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.

   Exécutez l’exemple suivant afin de configurer l’homologation Microsoft pour votre circuit :

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
  ```

### <a name="getmsft"></a>Pour afficher les détails de l’homologation Microsoft

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```

Le résultat ressemble à l’exemple suivant :

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Pour mettre à jour la configuration d’homologation Microsoft

Vous pouvez mettre à jour toute partie de la configuration. Les préfixes publiés du circuit sont mis à jour de 123.1.0.0/24 à 124.1.0.0/24 dans l’exemple suivant :

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Pour ajouter des paramètres d’homologation Microsoft IPv6 à une configuration IPv4 existante

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Pour supprimer une homologation Microsoft

Vous pouvez supprimer votre configuration d’homologation en exécutant l’exemple suivant :

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Homologation privée Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation privée Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure

1. Installez la dernière version de l’interface Azure CLI. Vous devez utiliser la dernière version de l’interface de ligne de commande (CLI) Azure.* Prenez connaissance de la [configuration requise](expressroute-prerequisites.md) et des [workflows](expressroute-workflows.md) avant de commencer la configuration.

  ```azurecli
  az login
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute. Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

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
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Configurez l'homologation privée Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

  * Un sous-réseau /30 pour le lien principal. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
  * Un sous-réseau /30 pour le lien secondaire. Le sous-réseau ne doit faire partie d’aucun espace d’adressage réservé aux réseaux virtuels.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation. Veillez à ne pas utiliser le numéro 65515.
  * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.

  Utilisez l’exemple suivant pour configurer l’homologation privée Azure pour votre circuit :

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
  ```

  Si vous choisissez d’utiliser un hachage MD5, utilisez l’exemple suivant :

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
  > 
  > 

### <a name="getprivate"></a>Pour afficher les détails d’une homologation privée Azure

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Le résultat ressemble à l’exemple suivant :

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Pour mettre à jour la configuration d’homologation privée Azure

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant : Dans cet exemple, l’ID VLAN du circuit est mis à jour de 100 à 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Pour supprimer une homologation privée Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’exemple suivant :

> [!WARNING]
> Vous devez vous assurer que tous les réseaux virtuels sont dissociés du circuit ExpressRoute avant d’exécuter cet exemple. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Homologation publique Azure

Cette section explique comment créer, obtenir, mettre à jour et supprimer la configuration d’homologation publique Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation publique Azure

1. Installez la dernière version de l’interface Azure CLI. Vous devez utiliser la dernière version de l’interface de ligne de commande (CLI) Azure.* Prenez connaissance de la [configuration requise](expressroute-prerequisites.md) et des [workflows](expressroute-workflows.md) avant de commencer la configuration.

  ```azurecli
  az login
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.  Suivez les instructions permettant de [créer un circuit ExpressRoute](howto-circuit-cli.md) et faites-le approvisionner par votre fournisseur de connectivité. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’homologation publique Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, continuez la configuration à l’aide de la procédure qui suit.

3. Vérifiez que le circuit ExpressRoute est approvisionné et activé. Consultez l’exemple qui suit :

  ```azurecli
  az network express-route list
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
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Configurez l’homologation publique Azure pour le circuit. Assurez-vous que vous disposez des informations suivantes avant de continuer.

  * Un sous-réseau /30 pour le lien principal. Ce doit être un préfixe IPv4 public valide.
  * Un sous-réseau /30 pour le lien secondaire. Ce doit être un préfixe IPv4 public valide.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
  * **Facultatif :** un hachage MD5 si vous choisissez d’en utiliser un.

  Exécutez l’exemple suivant pour configurer l’homologation publique Azure pour votre circuit :

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
  ```

  Si vous choisissez d’utiliser un hachage MD5, exécutez l’exemple suivant :

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.

### <a name="getpublic"></a>Pour afficher les détails d’une homologation publique Azure

Vous pouvez obtenir les détails de la configuration à l’aide de l’exemple suivant :

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Le résultat ressemble à l’exemple suivant :

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Pour mettre à jour la configuration d’homologation publique Azure

Vous pouvez mettre à jour toute partie de la configuration à l’aide de l’exemple suivant : Dans cet exemple, l’ID VLAN du circuit est mis à jour de 200 à 600.

```azurecli
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Pour supprimer une homologation publique Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’exemple suivant :

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](howto-linkvnet-cli.md).

* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).