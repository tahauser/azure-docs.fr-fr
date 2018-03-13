---
title: "Créer une passerelle d’application hébergeant plusieurs sites - Azure CLI | Microsoft Docs"
description: "Découvrez comment créer une passerelle d’application qui héberge plusieurs sites à l’aide d’Azure CLI."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: df475cb6eed2b75275e573721f754e7de87698f5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-cli"></a>Créer une passerelle d’application hébergeant plusieurs sites à l’aide d’Azure CLI

Vous pouvez utiliser Azure CLI pour configurer [l’hébergement de plusieurs sites web](application-gateway-multi-site-overview.md) quand vous créez une [passerelle d’application](application-gateway-introduction.md). Dans ce didacticiel, vous allez créer des pools backend à l’aide de groupes de machines virtuelles identiques. Vous configurerez ensuite des écouteurs et des règles basés sur des domaines qui vous appartiennent pour vérifier que le trafic web arrive sur les serveurs appropriés dans les pools. Ce didacticiel part du principe que vous possédez plusieurs domaines et utilise des exemples de *www.contoso.com* et de *www.fabrikam.com*.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs et des règles de routage
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

![Exemple de routage multisite](./media/tutorial-multisite-cli/scenario.png)

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

Créez le réseau virtuel nommé *myVNet* et le sous-réseau nommé *myAGSubnet* à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az_net). Vous pouvez ensuite ajouter le sous-réseau nommé *myBackendSubnet* nécessaire aux serveurs backend à l’aide de la commande [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Créez l’adresse IP publique nommée *myAGPublicIPAddress* à l’aide de la commande [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create).

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

Vous pouvez utiliser la commande [az network application-gateway create](/cli/azure/application-gateway#create) pour créer la passerelle d’application nommée *myAppGateway*. Quand vous créez une passerelle d’application avec Azure CLI, vous spécifiez des informations de configuration, notamment la capacité, la référence SKU et les paramètres HTTP. La passerelle d’application est affectée à *myAGSubnet* et à *myAGPublicIPAddress*, que vous avez créés. 

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
  --frontend-port 80 \
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

### <a name="add-the-backend-pools"></a>Ajouter les pools backend

Ajoutez les pools backend nommés *contosoPool* et *fabrikamPool* qui sont nécessaires pour contenir les serveurs backend, à l’aide de la commande [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address_pool_create).

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Ajouter les écouteurs

Un écouteur est requis pour permettre à la passerelle d’application d’acheminer le trafic de manière appropriée vers le pool principal. Ce didacticiel vous montre comment créer deux écouteurs pour vos deux domaines. Dans cet exemple, des écouteurs sont créés pour les domaines de *www.contoso.com* et de *www.fabrikam.com*. 

Ajoutez les écouteurs nommés *backendListener* et *fabrikamListener* qui sont nécessaires pour acheminer le trafic, à l’aide de la commande [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create).

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Ajouter les règles de routage

Les règles sont traitées dans l’ordre dans lequel elles sont créées et le trafic est dirigé à l’aide de la première règle qui correspond à l’URL envoyée à la passerelle d’application. Par exemple, si une règle utilise un écouteur de base et qu’une autre utilise un écouteur multisite sur le même port, la règle avec l’écouteur multisite doit être répertoriée avant la règle avec l’écouteur de base pour que la règle multisite fonctionne comme prévu. 

Dans cet exemple, vous créez deux règles et supprimez la règle par défaut qui a été créée au moment de la création de la passerelle d’application. Vous pouvez ajouter la règle à l’aide de la commande [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create).

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Créer des groupes de machines virtuelles identiques

Cet exemple crée trois groupes de machines virtuelles identiques prenant en charge les trois pools backend dans la passerelle d’application. Ils sont nommés *myvmss1*, *myvmss2* et *myvmss3*. Chacun contient deux instances de machines virtuelles sur lesquelles NGINX sera installé.

```azurecli-interactive
for i in `seq 1 2`; do
  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi
  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi
  az vmss create \
    --name myvmss$i \
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
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Installer NGINX

```azurecli-interactive
for i in `seq 1 2`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{
  "fileUris": ["https://raw.githubusercontent.com/davidmu1/samplescripts/master/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="create-a-cname-record-in-your-domain"></a>Créer un enregistrement CNAME dans votre domaine

Une fois la passerelle d’application créée avec son adresse IP publique, vous pouvez obtenir l’adresse DNS et l’utiliser pour créer un enregistrement CNAME dans votre domaine. Vous pouvez utiliser la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) pour obtenir l’adresse DNS de la passerelle d’application. Copiez la valeur *fqdn* de DNSSettings et utilisez-la comme valeur de l’enregistrement CNAME que vous créez. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

L’utilisation d’enregistrements A n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle d’application.

## <a name="test-the-application-gateway"></a>Tester la passerelle d’application

Entrez votre nom de domaine dans la barre d’adresse de votre navigateur. Par exemple : http://www.contoso.com.

![Tester le site contoso dans la passerelle d’application](./media/tutorial-multisite-cli/application-gateway-nginxtest1.png)

Remplacez l’adresse par celle de votre autre domaine. Voici ce qui doit apparaître :

![Tester le site fabrikam dans la passerelle d’application](./media/tutorial-multisite-cli/application-gateway-nginxtest2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configurer le réseau
> * Créer une passerelle Application Gateway
> * Créer des écouteurs et des règles de routage
> * Créer des groupes de machines virtuelles identiques avec les pools principaux
> * Créer un enregistrement CNAME dans votre domaine

> [!div class="nextstepaction"]
> [En savoir plus sur ce que la passerelle d’application vous permet de faire](application-gateway-introduction.md)