---
title: "Créer une passerelle d’application - Azure CLI | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application à l’aide d’Azure CLI."
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: bf7e22e86e593045d25a9f31166aebe992caeb45
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-cli"></a>Créer une passerelle d’application à l’aide d’Azure CLI

Vous pouvez utiliser Azure CLI pour créer ou gérer des passerelles d’application à partir de la ligne de commande ou dans des scripts. Ce guide de démarrage rapide montre comment créer des ressources réseau, des serveurs principaux et une passerelle d’application.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite que vous exécutiez la version 2.0.4 minimum d’Azure CLI. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroupAG* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Créer des ressources réseau 

Créez le réseau virtuel et le sous-réseau à l’aide de la commande [az network vnet create](/cli/azure/vnet#az_vnet_create). Créez l’adresse IP publique à l’aide de la commande [az network public-ip create](/cli/azure/public-ip#az_public_ip_create).

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Créer des serveurs principaux

Dans cet exemple, vous créez deux machines virtuelles à utiliser en tant que serveurs principaux pour la passerelle d’application. Vous installez également NGINX sur les machines virtuelles pour vérifier que la passerelle d’application a bien été créée.

### <a name="create-two-virtual-machines"></a>Créer deux machines virtuelles

Vous pouvez utiliser un fichier de configuration cloud-init pour installer NGINX et exécuter une application Node.js « Hello World » sur une machine virtuelle Linux. Dans l’interpréteur de commandes actuel, créez un fichier nommé cloud-init.txt et collez la configuration suivante dans l’interpréteur de commandes. Vérifiez que vous copiez bien l’intégralité du fichier cloud-init, en particulier la première ligne :

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Créez les interfaces réseau avec la commande [az network nic create](/cli/azure/network/nic#az_network_nic_create). Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az_vm_create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Créez une passerelle d’application à l’aide de la commande [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create). Lorsque vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, notamment la capacité, la référence SKU et les paramètres HTTP. Les adresses IP privées des interfaces réseau sont ajoutées en tant que serveurs dans le pool principal de la passerelle d’application.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

La création de la passerelle d’application peut prendre plusieurs minutes. Une fois que la passerelle d’application est créée, vous pouvez voir ses fonctionnalités :

- *appGatewayBackendPool* : une passerelle d’application doit avoir au moins un pool d’adresses principal.
- *appGatewayBackendHttpSettings* : spécifie que le port 80 et le protocole HTTP sont utilisés pour la communication.
- *appGatewayHttpListener* : écouteur par défaut associé à *appGatewayBackendPool*.
- *appGatewayFrontendIP* : assigne *myAGPublicIPAddress* à *appGatewayHttpListener*.
- *rule1* : règle d’acheminement par défaut associée à *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Pour obtenir l’adresse IP publique de la passerelle d’application, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Tester la passerelle d’application](./media/application-gateway-create-gateway-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un groupe de ressources, des ressources réseau et des serveurs principaux. Vous avez ensuite utilisé ces ressources pour créer une passerelle d’application. Pour plus d’informations sur les passerelles d’application et leurs ressources associées, consultez les articles de procédures.

