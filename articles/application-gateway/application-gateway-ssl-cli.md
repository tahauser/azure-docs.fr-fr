---
title: "Créer une passerelle d’application avec un arrêt SSL - Interface CLI Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application et ajouter un certificat pour un arrêt SSL à l’aide de l’interface CLI Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/18/2018
ms.author: davidmu
ms.openlocfilehash: c69ab3db9f23b714f7de9244e4e7015ae60a4f6e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-cli"></a>Créer une passerelle d’application avec un arrêt SSL à l’aide de l’interface CLI Azure

Vous pouvez utiliser l’interface CLI Azure pour créer une [passerelle d’application](application-gateway-introduction.md) avec un certificat pour un [arrêt SSL](application-gateway-backend-ssl.md) qui utilise un [groupe de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour des serveurs principaux. Dans cet exemple, le groupe identique contient deux instances de machine virtuelle qui sont ajoutées au pool principal par défaut de la passerelle d’application.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Configurer un réseau
> * Créer une passerelle d’application avec le certificat
> * Créer un groupe de machines virtuelles identiques avec le pool principal par défaut

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans ce guide de démarrage rapide. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Dans un environnement de production, vous devez importer un certificat valide signé par un fournisseur approuvé. Pour ce didacticiel, vous créez un certificat auto-signé et un fichier pfx à l’aide de la commande openssl.

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Entrez les valeurs pertinentes pour votre certificat. Vous pouvez accepter les valeurs par défaut.

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Entrez le mot de passe du certificat. Dans cet exemple, *Azure123456 !* est utilisé.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#create).

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Créez le réseau virtuel nommé *myVNet* et le sous-réseau nommé *myAGSubnet* à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az_net). Vous pouvez ensuite ajouter le sous-réseau nommé *myBackendSubnet* nécessaire aux serveurs principaux à l’aide de la commande [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de la commande [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Vous pouvez utiliser la commande [az network application-gateway create](/cli/azure/application-gateway#create) pour créer la passerelle d’application. Lorsque vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, notamment la capacité, la référence SKU et les paramètres HTTP. 

La passerelle d’application est assignée aux *myAGSubnet* et *myAGPublicIPAddress* que vous avez créés. Dans cet exemple, vous associez le certificat que vous avez créé et son mot de passe lorsque vous créez la passerelle d’application. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 La création de la passerelle d’application peut prendre plusieurs minutes. Une fois la passerelle d’application créée, vous pouvez voir ses nouvelles fonctionnalités suivantes :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses principal.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* - assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle d’acheminement par défaut associée à *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Dans cet exemple, vous créez un groupe de machines virtuelles identiques qui fournit des serveurs pour le pool principal par défaut dans la passerelle d’application. Les machines virtuelles dans le groupe identique sont associées à *myBackendSubnet* et *appGatewayBackendPool*. Pour créer le groupe identique, vous pouvez utiliser la commande [az vmss create](/cli/azure/vmss#az_vmss_create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installer NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, vous pouvez utiliser la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Avertissement de sécurité](./media/application-gateway-ssl-cli/application-gateway-secure.png)

Pour accepter l’avertissement de sécurité si vous avez utilisé un certificat auto-signé, sélectionnez **Détails**, puis **Atteindre la page web**. Votre site NGINX sécurisé apparaît maintenant comme dans l’exemple suivant :

![Tester l’URL de base dans la passerelle d’application](./media/application-gateway-ssl-cli/application-gateway-nginx.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un certificat auto-signé
> * Configurer un réseau
> * Créer une passerelle d’application avec le certificat
> * Créer un groupe de machines virtuelles identiques avec le pool principal par défaut

Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.
