---
title: "Configurer un pare-feu d’applications web : passerelle Azure Application Gateway | Microsoft Docs"
description: "Cet article explique comment utiliser un pare-feu d’applications web sur une passerelle Application Gateway nouvelle ou existante."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu
ms.openlocfilehash: e60bfc89378569b154f4f973d1dceb683fa58482
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway-with-azure-cli"></a>Configurer un pare-feu d’application web sur une passerelle Application Gateway nouvelle ou existante avec Azure CLI

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Interface de ligne de commande Azure](application-gateway-web-application-firewall-cli.md)

Découvrez comment créer une passerelle d’application avec le pare-feu d’applications web (WAF) activé. Découvrez également comment ajouter un pare-feu WAF à une passerelle d’application existante.

Le pare-feu WAF d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session.

 Application Gateway est un équilibrage de charge de couche 7. La solution assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Application Gateway fournit de nombreuses fonctionnalités de contrôleur de livraison d'applications (ADC) :

 * Équilibrage de charge HTTP 
 * Affinité de session basée sur les cookies 
 * Déchargement SSL (Secure Sockets Layer) 
 * Sondes d’intégrité personnalisées 
 * Prise en charge de la fonctionnalité multisite
 
 Pour obtenir une liste complète des fonctionnalités prises en charge, consultez : [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

Cet article explique comment [ajouter un pare-feu d’applications web à une passerelle Application Gateway existante](#add-web-application-firewall-to-an-existing-application-gateway). Il explique également comment [créer une passerelle Application Gateway utilisant un pare-feu d’applications web](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="prerequisite-install-the-azure-cli-20"></a>Condition préalable : installer Azure CLI 2.0

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure (Azure CLI) pour Mac, Linux et Windows](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu la rubrique [Création d’une passerelle Application Gateway avec Azure CLI](application-gateway-create-gateway-cli.md), vous connaissez les paramètres de référence (SKU) à configurer lorsque vous créez une passerelle Application Gateway. Le pare-feu WAF inclut des paramètres supplémentaires à définir lorsque vous configurez la référence SKU sur une passerelle Application Gateway. Aucune autre modification n’est nécessaire sur la passerelle d’application elle-même.

| **Paramètre** | **Détails**
|---|---|
|**Référence (SKU)** |Une passerelle Application Gateway normale sans un pare-feu WAF prend en charge les tailles **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**. Avec l’introduction d’un pare-feu WAF, deux autres SKU sont disponibles : **WAF\_Medium** et **WAF\_Large**. Les pare-feu WAF ne sont pas pris en charge sur les petites passerelles Application Gateway.|
|**Mode** | Ce paramètre indique le mode du pare-feu WAF. Les valeurs autorisées sont **Détection** et **Prévention**. Lorsque le pare-feu WAF est configuré en mode **Détection**, toutes les menaces sont stockées dans un fichier journal. En mode **Prévention**, les événements sont toujours consignés, mais l’attaquant reçoit une erreur d’autorisation de type 403 de la part de la passerelle Application Gateway.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle Application Gateway existante

La commande suivante transforme une passerelle d’application standard en passerelle d’application avec un pare-feu WAF activé :

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

Cette commande met à jour la passerelle Application Gateway avec un pare-feu WAF. Pour comprendre comment afficher les journaux de votre passerelle Application Gateway, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md). En raison des critères de sécurité inhérents à un pare-feu WAF, consultez régulièrement les journaux pour comprendre la politique de sécurité appliquée à vos applications web.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Créer une passerelle Application Gateway avec un pare-feu d’applications web

La commande suivante permet de créer une passerelle Application Gateway avec un pare-feu WAF :

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Les passerelles Application Gateway créées avec la configuration WAF de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="get-an-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle d’application

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, utilisez un enregistrement CNAME pour pointer vers le point de terminaison public de la passerelle d’application. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour un service cloud Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Pour configurer un enregistrement CNAME, récupérez les détails de la passerelle d’application et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle d’application. Utilisez le nom DNS de la passerelle d’application pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. Il n’est pas recommandé d’utiliser des enregistrements A, car l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
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

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser les règles WAF, consultez [Personnaliser les règles de pare-feu d’applications web par le biais d’Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
