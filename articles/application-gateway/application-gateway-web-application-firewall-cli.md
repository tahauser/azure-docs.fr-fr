---
title: "Créer une passerelle d’application avec un pare-feu d’applications web - Interface CLI Azure | Microsoft Docs"
description: "Apprenez à créer une passerelle d’application avec un pare-feu d’applications web à l’aide de l’interface CLI Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: 611e9b27baeddf61531421d7ad2bed20188ad279
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-cli"></a>Création d’une passerelle d’application avec un pare-feu d’applications web à l’aide de l’interface CLI Azure

Vous pouvez utiliser l’interface CLI Azure pour créer une [passerelle d’application](application-gateway-introduction.md) avec un pare-feu d’applications web [(WAF)](application-gateway-web-application-firewall-overview.md) qui utilise un [groupe de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Le WAF utilise des règles [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) pour protéger votre application. Ces règles incluent la protection contre les attaques telles que l’injection de code SQL, les attaques de script entre sites et les détournements de session. 

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle d’application avec WAF activé
> * Créer un groupe de machines virtuelles identiques
> * Créer un compte de stockage et configurer des diagnostics

![Exemple de pare-feu d’applications web](./media/application-gateway-web-application-firewall-cli/scenario-waf.png)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel exige que vous exécutiez Azure CLI version 2.0.4 ou une version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources Azure nommé *myResourceGroupAG* à l’aide de la commande [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau

Le réseau virtuel et les sous-réseaux sont utilisés pour fournir la connectivité réseau à la passerelle d’application et à ses ressources associées. Créez le réseau virtuel nommé *myVNet* et le sous-réseau nommé *myAGSubnet* à l’aide des commandes [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) et [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Créez une adresse IP publique nommée *myAGPublicIPAddress* à l’aide de la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Créer une passerelle d’application avec WAF

Vous pouvez utiliser la commande [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create) pour créer la passerelle d’application nommée *myAppGateway*. Lorsque vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, notamment la capacité, la référence SKU et les paramètres HTTP. La passerelle d’application est assignée aux *myAGSubnet* et *myPublicIPSddress* que vous avez créés.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

La création de la passerelle d’application peut prendre plusieurs minutes. Une fois la passerelle d’application créée, vous pouvez voir ses nouvelles fonctionnalités suivantes :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses principal.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* - assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle d’acheminement par défaut associée à *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Créer un groupe de machines virtuelles identiques

Dans cet exemple, vous créez un groupe de machines virtuelles identiques qui fournit deux serveurs pour le pool principal dans la passerelle d’application. Les machines virtuelles dans le groupe identique sont associées au sous-réseau *myBackendSubnet*. Pour créer le groupe identique, vous pouvez utiliser la commande [az vmss create](/cli/azure/vmss#az_vmss_create).

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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Créer un compte de stockage et configurer des diagnostics

Dans ce didacticiel, la passerelle d’application utilise un compte de stockage pour stocker des données à des fins de détection et de prévention. Vous pouvez également utiliser Log Analytics ou Event Hub pour enregistrer des données. 

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage nommé *myagstore1* à l’aide de la commande [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Configuration de la collecte des diagnostics

Configurez des diagnostics pour enregistrer des données dans les journaux ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog et ApplicationGatewayFirewallLog. Remplacez `<subscriptionId>` par votre identificateur d’abonnement, puis configurez les diagnostics à l’aide de la commande [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)
storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)
az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Tester l’URL de base dans la passerelle d’application](./media/application-gateway-web-application-firewall-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle d’application avec WAF activé
> * Créer un groupe de machines virtuelles identiques
> * Créer un compte de stockage et configurer des diagnostics

Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.