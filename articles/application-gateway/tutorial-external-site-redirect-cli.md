---
title: "Créer une passerelle d’application avec une redirection de trafic externe - Interface CLI Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application qui redirige le trafic web interne vers le pool approprié à l’aide de l’interface CLI d’Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: fed23c7138516b3af8af170383948b14c49a61a9
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Créer une passerelle d’application avec redirection externe à l’aide de l’interface CLI Azure

Vous pouvez utiliser l’interface CLI Azure pour configurer une [redirection du trafic web](application-gateway-multi-site-overview.md) lors de la création d’une [passerelle d’application](application-gateway-introduction.md). Dans ce didacticiel, vous configurez un écouteur et une règle pour rediriger le trafic web qui arrive au niveau de la passerelle d’application vers un site externe.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer un écouteur et une règle de redirection
> * Créer une passerelle Application Gateway

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#create).

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau 

Créez le réseau virtuel nommé *myVNet* et le sous-réseau nommé *myAGSubnet* à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az_net). Créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de la commande [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create). Ces ressources sont utilisées pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

Vous pouvez utiliser la commande [az network application-gateway create](/cli/azure/application-gateway#create) pour créer la passerelle d’application nommée *myAppGateway*. Quand vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, telles que la capacité, la référence SKU et les paramètres HTTP. La passerelle d’application est assignée aux *myAGSubnet* et *myPublicIPSddress* que vous avez créés. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 8080 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

La création de la passerelle d’application peut prendre plusieurs minutes. Une fois la passerelle d’application créée, vous pouvez voir ses nouvelles fonctionnalités suivantes :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses backend.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* - assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle de routage par défaut associée à *appGatewayHttpListener*.

### <a name="add-the-redirection-configuration"></a>Ajouter la configuration de redirection

Ajoutez la configuration de redirection qui envoie le trafic provenant de la passerelle d’application vers *bing.com* à l’aide de [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create).

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "http://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Ajouter un écouteur et une règle de routage

Un écouteur est requis pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée. Créez l’écouteur à l’aide de [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) et du port frontal créé avec [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create). Une règle est requise pour que l’écouteur sache vers où rediriger le trafic entrant. Créez une règle de base nommée *redirectRule* à l’aide de [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create) avec la configuration de redirection.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, vous pouvez utiliser la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

Le site *bing.com* devrait s’afficher dans votre navigateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> * Configurer le réseau
> * Créer un écouteur et une règle de redirection
> * Créer une passerelle Application Gateway

> [!div class="nextstepaction"]
> [En savoir plus sur ce que la passerelle d’application vous permet de faire](./application-gateway-introduction.md)