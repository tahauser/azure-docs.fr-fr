---
title: "Créer une passerelle Application Gateway - Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment créer une passerelle Application Gateway à l’aide d’Azure CLI 2.0 dans Resource Manager."
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: beb2dab177d021fee1dbbe630f8b6854a7d94f68
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Créer une passerelle Application Gateway à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Une passerelle Application Gateway est une appliance virtuelle dédiée qui intègre Application Delivery Controller (ADC) en tant que service, offrant diverses fonctionnalités d’équilibrage de charge de couche 7 pour votre application.

## <a name="cli-versions"></a>Versions CLI

Vous pouvez créer une passerelle Application Gateway en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) : Azure CLI pour les modèles de déploiement Classique et Azure Resource Manager
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) : CLI nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisite-install-the-azure-cli-20"></a>Condition préalable : installer Azure CLI 2.0

Pour exécuter la procédure indiquée dans cet article, vous devez [installer Azure CLI pour macOS, Linux et Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

> [!NOTE]
> Il vous faut un compte Azure pour créer une passerelle Application Gateway. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle Application Gateway à l’aide du portail Azure.

Ce scénario va :

* créer une passerelle Application Gateway moyenne avec deux instances ;
* créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;

> [!NOTE]
> La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires, se fait après avoir créé la passerelle Application Gateway et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

Une passerelle Application Gateway requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, vous ne pouvez ajouter au sous-réseau que les passerelles supplémentaires.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez l’**Invite de commandes Microsoft Azure**et connectez-vous :

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> Vous pouvez également utiliser `az login` sans le commutateur destiné à la connexion de l’appareil et qui nécessite la saisie d’un code sur aka.ms/devicelogin.

Après avoir entré la commande précédente, vous recevez un code. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![Invite de commande affichant le code de l’appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![Page de navigateur pour la saisie du code][2]

Entrez le code pour vous connecter, puis fermez le navigateur pour continuer.

![Connexion réussie][3]

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la passerelle Application Gateway, créez un groupe de ressource pour la contenir. Utilisez la commande suivante :

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Utilisez les adresses IP principales comme adresses IP de votre serveur principal. Ces valeurs peuvent être des adresses IP privées dans le réseau virtuel, des adresses IP publiques ou des noms de domaine complets pour vos serveurs principaux. L’exemple suivant crée une passerelle Application Gateway avec des configurations supplémentaires pour les paramètres HTTP, les ports et les règles :

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

L’exemple précédent indique plusieurs propriétés non requises lors de la création d’une passerelle Application Gateway. L’exemple de code suivant crée une passerelle Application Gateway avec les informations requises :

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> Pour obtenir la liste de paramètres à utiliser lors de la création, exécutez la commande suivante : `az network application-gateway create --help`.

Cet exemple crée une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool principal, les paramètres http principaux et les règles. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi.

Si l’application web a été définie avec le pool principal dans les étapes précédentes, alors l’équilibrage de charge démarre maintenant.

## <a name="get-the-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway
Après avoir créé la passerelle, vous devez configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, utilisez un enregistrement CNAME pour pointer vers le point de terminaison public de la passerelle Application Gateway. Pour plus d’informations, consultez [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Utiliser DNS Azure pour fournir des paramètres de domaine personnalisé pour un service Azure).

Pour configurer un alias, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Utilisez le nom DNS de la passerelle d’application pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. N’utilisez pas de A-records car l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, exécutez la commande suivante :

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Étapes suivantes

Pour apprendre à créer des sondes d’intégrité personnalisées, consultez [Créer une sonde personnalisée pour une passerelle Application Gateway à l’aide du portail](application-gateway-create-probe-portal.md).

Pour apprendre à configurer le déchargement SSL et retirer les coûts de déchiffrement SSL de vos serveurs web, consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager](application-gateway-ssl-arm.md).

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
